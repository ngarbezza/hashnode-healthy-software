## Refactoring step by step: extract validation logic from a method

### Context

[Testy](https://github.com/ngarbezza/testy) is one of my open-source projects: it is a minimal Javascript testing framework. Testy, like many other testing tools, has a way to execute some code before each test. It is called `before()` and it receives a function as a block of code. The implementation of `before()` was the following ([reference](https://github.com/ngarbezza/testy/blob/main/lib/test_suite.js#L21)):

```jsx
before(initializationBlock) {
  if (isUndefined(this._before)) {
    this._before = initializationBlock;
  } else {
    throw new Error('There is already a before() block. Please leave just one before() block and run again the tests.');
  }
}
```

### The problem

A couple of weeks ago, I’ve found the implementation of before() was not validating enough scenarios. We could pass any object to before() and even more, due to Javascript's argument handling, we could pass no arguments and the code won't fail either. I [reported the issue](https://github.com/ngarbezza/testy/issues/202) and started working on a solution.

I went ahead and started with a failing test (the TDD way), and this is the code I came up with:

```jsx
before(initializationBlock) {
  if (isUndefined(this._before)) {
    if (isFunction(initializationBlock)) {
      this._before = initializationBlock;
    } else {
      throw new Error('The before() hook must include a function. Please provide a function or remove the before() and run again the tests.');
    }
  } else {
    throw new Error('There is already a before() block. Please leave just one before() block and run again the tests.');
  }
}
```

Then I realized I had a design problem, that got worse after introducing the new code.

- We’re mixing validations from the successful case. Most of the time, we’re interested to read the successful case faster and avoid having in our heads code from different execution flows.
- Complexity alert! there are two nested conditionals, making the code hard to read, and increasing the total number of collaborations in the method. There are 7 things to pay attention and they are all at the same level. We have: 2 `if` statements, 2 conditions (one for each conditional), 2 `throw` declarations, and the assignment of `this._before`.

This pattern certainly won’t scale as we add more validations. Let’s have a look at the improvements I made using only automatic refactorings.

### Step 1: “validate and throw”

Part of the complexity was caused by the nested conditionals. We can remove them and `throw` as soon as we find a validation error. After a couple of iterations, I refactored the code this way:

```javascript
before(initializationBlock) {
  if (!isUndefined(this._before)) {
    throw new Error('There is already a before() block. Please leave just one before() block and run again the tests.');
  }

  if (!isFunction(initializationBlock)) {
    throw new Error('The before() hook must include a function. Please provide a function or remove the before() and run again the tests.');
  }

  this._before = initializationBlock;
}
```

¿Which are the improvements compared to the previous version?

- There are 3 clear steps: 2 validations and the valid case. There are some blank lines intentionally left to increase readability.
- Reducing unnecessarily nested if statements. `throw` statements interrupt execution flow, so there’s no need to do if-else style.

I did this change using [Webstorm from Jetbrains](https://www.jetbrains.com/webstorm/), where I could do these sequence of automatic refactorings:

- Set the cursor in the first `if`, then I opened the suggestions tool (Alt-Enter is the default shortcut but it might be different in your setup). There’s an option called *“flip if-else”*.
- Set the cursor in the first  `else` and trigger the suggestions menu again. In this case, I chose *“unwrap else”*.
- Repeat the two above steps for the second validation (*flip if-else*, then *unwrap else*)

The suggestions menu is great when you know you want to do something, but you don’t know how the action is called, or you don’t know the shortcut.

### Step 2: extract each validation as a method

In this step, we’ll move the validations to separate messages. Let’s have a look at the result:

```javascript
before(initializationBlock) {
  this._validateThereIsNoBeforeBlockAlreadyDeclared();
  this._validateTheBeforeBlockIncludesAValidFunction(initializationBlock);

  this._before = initializationBlock;
}

_validateThereIsNoBeforeBlockAlreadyDeclared() {
  if (!isUndefined(this._before)) {
    throw new Error('There is already a before() block. Please leave just one before() block and run again the tests.');
  }
}

_validateTheBeforeBlockIncludesAValidFunction(initializationBlock) {
  if (!isFunction(initializationBlock)) {
    throw new Error('The before() hook must include a function. Please provide a function or remove the before() and run again the tests.');
  }
}
```

Improvements:

- Validations are reified as messages. They have a name that represents what they are expecting to happen, the code becomes more intention-revealing.
- Statement count in `before()` method is reduced. Now we have 3 (2 messages, 1 assignment).
- Reduced load on our brains. if we don’t care about validations when reading this method, we don’t need to look at the implementation of the extracted methods.

Automatic refactorings used:

- Extract method (twice). This can be triggered by the “Refactor this” popup or by hitting the shortcut (Mac’s default is Cmd-Alt-M, for example). This is a shortcut worth learning, as it’s probable the most used refactoring (along with Rename).

### Step 3: extract error messages

We could stop at the previous step, but in order to properly split responsibilities we can go a step further and extract the error messages to separate methods:

```javascript
before(initializationBlock) {
  this._validateThereIsNoBeforeBlockAlreadyDeclared();
  this._validateTheBeforeBlockIncludesAValidFunction(initializationBlock);

  this._before = initializationBlock;
}

_validateThereIsNoBeforeBlockAlreadyDeclared() {
  if (!isUndefined(this._before)) {
    throw new Error(this._alreadyDefinedBeforeBlockErrorMessage());
  }
}

_alreadyDefinedBeforeBlockErrorMessage() {
  return 'There is already a before() block. Please leave just one before() block and run again the tests.';
}

_validateTheBeforeBlockIncludesAValidFunction(initializationBlock) {
  if (!isFunction(initializationBlock)) {
    throw new Error(this._beforeBlockWasNotInitializedWithAFunctionErrorMessage());
  }
}

_beforeBlockWasNotInitializedWithAFunctionErrorMessage() {
  return 'The before() hook must include a function. Please provide a function or remove the before() and run again the tests.';
}
```

What did we improve in this step?

- Continue separating responsibilities. Building an error message might seem trivial in this context and not worth for extraction. But in general, to give [meaningful error messages](https://ngarbezza.hashnode.dev/3-things-error-messages-should-have), I’d say extracting the message is a good idea. Especially when the message needs parameters.
- It allows us to reuse the error message in tests, following the DRY principle.

Automatic refactorings used:

- Extract method (twice)

### Conclusions

- Validating input is a very common design challenge. Many interesting design questions emerge. Where to define those validations? When to validate? How to reuse validations?
- Reifying the validations as methods is a good first step. You may also need to reify them as classes if the validation logic becomes more complex.
- A method with validations has two parts with a clear separation. First, the logic to run validations (ensuring method preconditions are met). Second, executing the actual code.
- We refactored the solution with automatic transformations only, which is something we should aim. Automatic refactorings with guidance from an IDE are key to safely make changes. We’ll still have the unit tests as the safety net.
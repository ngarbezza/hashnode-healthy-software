## Using Acorn to implement a refactoring in Javascript (Part 1: hacking it)

Photo by  [Mila Tovar](https://unsplash.com/@milatovar?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)  on  [Unsplash](https://unsplash.com/s/photos/tree-branches?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 

## Intro

I love making tools for developers. I did [my thesis](https://ngarbezza.com.ar/proyectos/tesis-licenciatura-unq) writing refactorings in Smalltalk and I really enjoyed it (and I also learned a lot!). Now I feel that I need refactorings in whatever language I use, it is one of those things that once you have it, it hurts losing it. I often ask myself: if we don't have this refactoring, how much would it take to implement it?

I'm presenting here an experience writing a simple source code transformation for Javascript.

## Which tools do we need to implement a refactoring?

Probably the most important tool for implementing refactorings is the source code *parser*. In Javascript we have many available options and in this post, I'll walk you through [Acorn](https://github.com/acornjs/acorn). As any parser, its output is an [Abstract Syntax Tree (AST)](https://en.wikipedia.org/wiki/Abstract_syntax_tree), that can be analyzed to understand the elements behind the source code and their semantics.

Installing Acorn is straightforward, it's just an [NPM module](https://www.npmjs.com/package/acorn) that you can install using `npm`:

`npm i --save acorn`

And then require just like any other module

```javascript
const acorn = require('acorn');
```

This module exposes a `parse` function that we'll use in the refactoring implementation. The first argument of `parse` is a source code string and the second is a set of options where `ecmaVersion` is always required (we'll use `11` through this post).

## The example refactoring

In my teaching experiences, I often see some beginner mistakes, like the following:

```javascript
if (a > b) {
  return true;
} else {
  return false;
}
```

Fidel, one of my first Computer Science teachers at the University, likes to call it *"miedo al booleano"* ("fear of booleans"). The underlying problem might be related to understanding the idea that `true` or `false` or `a || (b && !d)` are all expressions of the same type (boolean), but initially `true` and `false` "look more like booleans" than a complex expression.

The fix for this problem is to remove the unnecessary `if` statement and consolidate the condition in a return statement:

```javascript
return a > b;
```

Simple and clean. It is a safe, behavior-preserving source code transformation, that’s why it can be considered a refactoring.

In summary, we can think of a refactoring as a transformation function where the input is a string representing a piece of source code, and the output is another string representing the transformed source code.

## Understanding and navigating the AST

What happens if I try to parse the example above using acorn?

```javascript
> const { parse } = require('acorn')

> program = parse('if (a > b) { return true } else { return false }', { ecmaVersion: 11, allowReturnOutsideFunction: true })
Node {
  type: 'Program',
  start: 0,
  end: 48,
  body: [
    Node {
      type: 'IfStatement',
      start: 0,
      end: 48,
      test: [Node],
      consequent: [Node],
      alternate: [Node]
    }
  ],
  sourceType: 'script'
}
```

I need to specify another option called `allowReturnOutsideFunction` to indicate that having a return statement is fine because the piece of code we're analyzing is usually part of a bigger program.

As we can see, we got a Node object that represents the "program". A program is the root node we can still navigate until we got to the "leaves" of the tree:

```javascript
> ifStatement = program.body[0]
Node {
  type: 'IfStatement',
  start: 0,
  end: 48,
  test: Node {
    type: 'BinaryExpression',
    start: 4,
    end: 9,
    left: Node { type: 'Identifier', start: 4, end: 5, name: 'a' },
    operator: '>',
    right: Node { type: 'Identifier', start: 8, end: 9, name: 'b' }
  },
  consequent: Node { type: 'BlockStatement', start: 11, end: 26, body: [ [Node] ] },
  alternate: Node { type: 'BlockStatement', start: 32, end: 48, body: [ [Node] ] }
```

Now we zoomed in and we are visualizing the if statement. An object of type `'IfStatement` has three different properties: `test`, `consequent` and `alternate`. Let's zoom in one more time and take a look at the consequent:

```javascript
> consequent = ifStatement.consequent
Node {
  type: 'BlockStatement',
  start: 11,
  end: 26,
  body: [
    Node {
      type: 'ReturnStatement',
      start: 13,
      end: 24,
      argument: [Node]
    }
  ]
}
> returnStatement = consequent.body[0]
Node {
  type: 'ReturnStatement',
  start: 13,
  end: 24,
  argument: Node {
    type: 'Literal',
    start: 20,
    end: 24,
    value: true,
    raw: 'true'
  }
}
```

Note that there's a node for the return statement and one for the `true` literal.

I suggest you (for Acorn and any other library) to open up a `node` console and start playing with it. That’s how I learned details of the AST, used later in the actual implementation. Even better, you can write a unit test and play with the output you expect and the output you got. You will get feedback until the test becomes green, at that point you learned how to use it 😎.

## Implementing some validations

The refactoring is very specific, some conditions must be met before the refactoring can be applied:

- The source code selection must contain a single if statement.
- Each branch of the `if` must be a `return true` and `return false` respectively. More code inside these branches can make the transformation to not preserve behavior.

Otherwise, the refactoring cannot be applied. There is no restriction on the if condition.

This is the code that checks all the preconditions:

```javascript
sourceCode = 'if (a > b) { return true } else { return false }'
const acorn = require('acorn')
programNode = acorn.parse(sourceCode, { ecmaVersion: 11, allowReturnOutsideFunction: true })

containsOneStatement = programNode.body.length === 1
ifStatement = programNode.body[0]
statementIsAnIf = ifStatement.type === 'IfStatement'
thenHasASingleStatement = ifStatement.consequent.body.length === 1
elseHasASingleStatement = ifStatement.alternate.body.length === 1
thenReturnsTrue = ifStatement.consequent.body[0].argument.value === true
elseReturnsFalse = ifStatement.alternate.body[0].argument.value === false
refactoringCanBeApplied = containsOneStatement && statementIsAnIf && thenHasASingleStatement && elseHasASingleStatement && thenReturnsTrue && elseReturnsFalse
```

As you can see, the main AST node is a program node, that contains a `body` which is an array of statements. A single statement is required, and this statement should be an `if`. Then we look at the properties `consequent` and `alternate`, these have code blocks as well, that must have a single statement with a `return`. Values should be `true` for the consequent, and `false` for the alternate (we can also implement a similar refactoring with false and true, and flipping the condition, but I wanted to keep the example simple).

## Implementing the actual refactoring

Once the refactoring is safe to be applied, we go to the next step. Applying the refactoring is just returning the source code string we want as result. Then this string can be used by the IDE to rewrite the original code (topic for another post!).

In this case, we want to return `return a > b`.

Super important thing: Acorn provides **source code ranges** as part of the AST, so we know the structure and each part of the code, but we also know where in the source code each node is located. This is pretty convenient to manipulate the source code.

We can use a code like this to generate the result string:

```javascript
originalConditionCode = sourceCode.slice(ifStatement.test.start, ifStatement.test.end)
refactoredSourceCode = `return ${originalConditionCode}`
```

We did it! We performed a safe source code transformation completely in an automated way.

I captured the whole solution in the following Gist:

%[https://gist.github.com/ngarbezza/a2b7f58846aa1632e2cc4fc77427e3a0]

## Next steps

The solution I've presented here is a "hacky" one (it's just a script with a bunch of steps). In the next post, I'll share with you the object-oriented solution with more intention-revealing code, division in subtasks, some abstractions we can use to implement more refactorings... and last but not least, tests!

Another next step is to integrate this logic with an IDE, so that it can be triggered from your current workflow, ideally with a configurable keystroke, and get your rewrite in place.

## Conclusions

- Implementing refactorings is not as hard as it seems to be! The main tool you need is a good parser, that provides you a rich AST including source ranges for you to manipulate.
- Any refactoring has 2 main actors: **preconditions**, and **application**. Preconditions need to navigate the AST structure and the application needs the source ranges to decide how to rewrite the original code.
- Acorn has all we need to implement this refactoring and it is a very lightweight library. We can implement this transformation in less than 20 lines of code!
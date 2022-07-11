## What is a code hotspot?

### Intro

Not all lines of code have the same *value*. Some are most used than others, some are most changed than others, some are most critical than others.

For us as developers, it is very important to visualize and compare different files/classes/modules with respect to importance in the application, so that we focus our refactorings/tests/documentation around those pieces of code.

### Definition of Hotspot

A **Hotspot** is a piece of code that is both **complex** and **changed often**. This article from the "Understanding Legacy Code" blog, which I highly recommend, explains it in detail: ["Focus refactoring on what matters with Hotspots Analysis"](https://understandlegacycode.com/blog/focus-refactoring-with-hotspots-analysis/), including how to measure complexity, and churn rate (a way to indicate how often files change).

### How to spot a hotspot?

There are many reasons to have hotspots:

* God classes: classes that take many responsibilities, and are always used on every feature.
* Classes with volatile logic: classes whose purpose is not so clear and it has frequent big changes, either in the structure or in the behavior.
* Classes with complex and growing logic: maybe a class is not a god class, but it has a trend indicating more complexity as features are added.
* Utility modules: *bags* of things are also potential hotspots because they have many dependents.

Sometimes it is not a bad thing to have hotspots, when they cannot be avoided, it is good to at least control them.

### An Example

This is one example from one of my personal projects, [Testy: a JS testing library designed for teaching](https://github.com/ngarbezza/testy/issues/170) (I'll talk about it in upcoming articles!).

Testy, as a testing tool, has an assertions runner, that determines if a test passes or fails. Right now, this is being handled in a single `Asserter` object which contains both the assertion runner logic and the different assertions' logic (check for equality/null/collection inclusion/exceptions and more). In the beginning, we had a few assertions so the problem was not that big, but as the number of assertions increased, we always end up touching the `Asserter` which is a violation of the Open-Closed Principle and ends up generating a hotspot on that class.

Here's the CodeClimate report which clearly indicates that class is the most critical hotspot in the project:

![Screenshot_20201127_203730.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1606521762895/povoiW1ra.png)

I'll be refactoring that object and I'll talk about the experience in the next posts, so keep tuned!

### Conclusions

* It is very important to have metrics and visualize hotspots because time changes everything. Things that today are simple objects, tomorrow can be complex. For open-source projects, services like CodeClimate gives that chart for free! Keep that in mind when adding changes to your project.
* Keep refactoring focused on hotspots, because the impact will always be high on those cases. Hotspots mean risks and it is a good thing to distribute them.
* There is a high relationship between having hotspots and code smells (like [Large Class](https://refactoring.guru/smells/large-class)) and violation of SOLID principles (especially Single Responsibility Principle and Open-Closed Principle).
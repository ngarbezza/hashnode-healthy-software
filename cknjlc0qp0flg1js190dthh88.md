## Dealing with upgrades

Photo by <a href="https://unsplash.com/@carrier_lost?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Ian Taylor</a> on <a href="https://unsplash.com/s/photos/better?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>

## Intro

A very important part of software maintenance is to keep languages, libraries, and framework versions up to date.

I've experienced this many times while working on Ruby projects, and I've collected some tips or patterns that helped me to deal with future cases. Pretty much everything presented here applies to different languages and dependency management systems.

## Possible Scenarios

First of all, we need to understand the motivation for an upgrade. There could be many reasons:

* We need a new feature and the current version does not provide it
* A security alert (like a known vulnerability) was reported
* A new version comes with a performance fix/refactor and we want to take advantage of that
* An EOL (end-of-life) support date is approaching (or it already happened). It’s wise to consider that in your project roadmap.
* We want to ensure the library’s stability by migrating from an unstable/development/alpha/beta gem to a stable one (like moving from 0.x to 1.x)
* We want to keep doing regular upgrades to prevent the above situations

## Should we upgrade?

Some questions that could help to make a decision:

* What could happen if our app breaks after the update? How hard would it be to recover? How bad could it be for the users of your system?
* A vulnerability is definitely something not good, but how likely is for some malicious user to exploit that vulnerability? For instance, if we know our users are internal employees of a company, it’s clearly not the same as having a public page where everyone can try to break
* Is this a good time to do the upgrade? How does it play with other commitments or deadlines? What are the costs of postponing it? Also, don't wait for the best/perfect time.
* How much do we trust in our test suite or linting tools to early catch problems? How much we can test in an automated way vs. manual? Or in general, how many possible errors can we catch in environments before production?
* Are there references/upgrade guides (either official or non-official) for the changes that could break ? Does the project say something about backwards compatibility?
* How is the history of the latest upgrades in the project? How did they go? If there was a problem, how much time did it pass until we detected it and how much time did it take to recover?

## Things to have in mind

* Upgrade chains/trees: If a library has dependencies, it's possible for the dependencies to need an upgrade as well. And dependencies of the dependencies. It can be a tree full of upgrades. Start by the easy upgrades, the leaves on that tree, when possible.
* Changelog analysis: Review carefully CHANGELOG files, when they exist. Pay special attention to breaking changes or behavior changes on the features you use.
* [Semantic Versioning](http://semver.org/): SemVer makes everything easier, when library authors follow it. If the convention is not followed or not strictly respected, treat every version change as a potentially breaking one.
* Test strategy: How will you test the upgrade? I suggest measuring test coverage around the code affected by the upgrade. That way you’ll have a high-level view and you’ll know how much manual testing you will need.
* Deprecation warnings: New versions might introduce deprecations, which are things that are going to be removed in next versions, but you can still use them, receiving warnings. I’d say an upgrade process is not fully complete until we fix them, because today’s deprecations are blockers for tomorrow’s upgrades.
* Allowing upgrades within a safe version range: We have `~>` in Ruby's Bundler (the  [pessimistic operator](https://guides.rubygems.org/patterns/#pessimistic-version-constraint)), same thing in Python with Pip’s `~=` ( [compatible release](https://www.python.org/dev/peps/pep-0440/#compatible-release) clause). This plays nicely for projects following SemVer, you can make upgrades without changing your dependencies specs, while sticking into safe margins (like preventing major version upgrades).
* Check for outdated packages: Most package managers give commands that you can run and see which packages need upgrades, like NPM’s `npm outdated` or Bundler's `bundle outdated`. This proactive approach can save you some headaches in the future.
* Reading others experiences: most of the time, an upgrade it’s not a task you need to figure out on your own. There are a lot of great articles about upgrade stories with different experiences, pay attention to them and the possible errors/solutions they offer. One great example of this is [FastRuby](https://www.fastruby.io), a company that specializes in Rails upgrades, their experiences and tools are very useful.

## Leaving a codebase

Photo by <a href="https://unsplash.com/@lmtrochezz?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Lina Trochez</a> on <a href="https://unsplash.com/s/photos/gift?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>

Leaving a codebase, especially if you worked on it for a long time, requires some work to properly hand it off to your colleagues that will continue with the project.

I'm leaving a codebase where I contributed for 6 years. I've made more than 2000 commits and I've added, modified, and removed a lot of lines of code. Here are some things I have in mind as part of my offboarding process.

## Backlog grooming

It's very likely that during your contribution period, your project's backlog accumulated several ideas, new features, bugs, investigation or technical debt tasks.

It could be very confusing if a team inherits a backlog with a bunch of incomprehensible tickets... I've been there!

- Are there tickets no longer relevant? Let's delete those to avoid confusion!
- Do the tickets have enough context? Ideally, anyone from the team should be able to read it and start working on it.
- Are they well categorized? Using labels/issue types of epics depending on the case could help you find tasks faster.
- Do they have a manageable size? Do they need to be broken down into smaller tickets?
- Are there any technical notes you can leave as "hints" to tackle those tickets?

When in doubt, if we feel we're not ready to pick a ticket and nobody understands the context behind it, my suggestion is to remove that ticket because it can cause confusion. If it's important, it will appear again in the future.

## Knowledge sharing sessions

Sometimes it is a good idea to just sit together and discuss a specific topic, with a main goal: for the leaving person, to share what they have learned, and for the rest of the audience, to learn as much as they can and ask a lot of questions.

I know that we used to call this a "knowledge transfer" session, but I think the word "sharing" sounds better and more human.

It does not need to be a super organized event, you can improvise if you don't have enough time, [I wrote a post about that](https://ngarbezza.hashnode.dev/sharing-knowledge-in-an-improvised-way), in case you are looking for a first-hand experience 😬

Some pattern I follow for these type of presentations is the following:

- A little bit of history and context about the thing you are presenting.
- Architecture overview (if you have charts, this is the moment to show them).
- Deep dive into the code. (this can be optional depending on how much time do you, and your colleagues have)
- Resources (presentations, links to diagrams, relevant parts of the code, wiki pages).

A good idea is to also record those sessions so that people joining the team after you, can use them as onboarding material.

## Cleanup code

Any time is a good time to think about unused code and getting rid of it. Especially if you are leaving the codebase and you know there are things that are deactivated or just unused. It can be an easy task but also challenging because you don't want anything else to break. Also, make sure you work on removing all the code that is unused. I recommend you reading this article I wrote some time ago called ["Clean Code Cleanups"](https://blog.10pines.com/2020/06/22/clean-code-cleanups/) for more information about ways to safely delete code.

## Document architecture and important parts of the code

A more "formal" way of doing a hand-off is leaving technical documents describing the most important areas of your project.

- Don't go too low-level. Class and method names may change, technologies, and version numbers as well.
- What are the system's interactions with other systems? Who are its users (people, other systems, both)?
- Setup instructions

And if you are documenting multiple apps and services, make sure to have a centralized "discovery" page/document to access all the docs from a single place.

## Setup slots for pairing and mobbing

Usually, your last days are not going to be working solo and/or being that key person of a long development.

This is a good opportunity to maximize your availability to pair with other people, letting them be the drivers of the sessions most of the time, and you playing a supporting role. Imagine what could happen if you are not there. Mob programming sessions are helpful as well, but always letting the rest of the people drive.

If you want to keep your agenda organized, you can block time for pairing preemptively. So your colleagues know when they can find you for pairing or for just some questions.

## Make a plan

All the things considered before can be part of an offboarding plan.

How many days/weeks do you need to achieve that? Which people do you need to be involved with during the process?

Where would you leave all your deliverables? Like diagrams or documents or links to recorded sessions.

I recommend making a checklist and making sure all your items are done before you leave! And share this information with everyone so they can request things and see your progress and outputs.

In summary, I think this is one of the aspects that makes a developer more professional and kind about the code and the people you work with. I hope you find this information useful. If you have any other tips or ideas, let me know!
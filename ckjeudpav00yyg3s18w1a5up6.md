## 3 Things Error Messages Should Have

(picture by Tim Mossholder on [Unsplash](https://unsplash.com/photos/QwbFHvt_Uzk))

## Intro

One of the things that we as developers tend to underestimate, or not paying much attention to is writing good error messages.

I will use an example throughout the post, which I recently had to implement on one of my side projects, [Testy (a minimalist testing tool for JS)](https://github.com/ngarbezza/testy). This project has internationalization, and I wanted to validate the user is configuring a supported language. If not, the user should see an error message explaining the issue.

## What is the problem

What is the nature of the problem? Is it an invalid value? An unexpected problem? Something temporary? The more clear the message is the better. But if we are surfacing an error message to the end-user, we might want not to say `"MySQL error 1022"` type of errors which are too technical.

In our example, the problem is that the language we're trying to use is not supported by the tool. But saying `"Unsupported language"` is not enough...

## Where was the problem

All right! We know that a problem happened and we might even know which part of the software is failing. But how we ended up in that error? Was it a wrong input? Did we as users do something wrong?

In our example, we might want to indicate which was the input that caused problems and make it part of the error message. So the improved error message would be `"Unsupported language: 'klingon'"`. Good enough? Maybe, but we can do something better...

## How we can solve it

Now that you know what was the problem and where exactly things went wrong, it is good to learn how to solve it. Remember, good software "teaches" its users. If it is validation, what are the allowed values? If it was a temporary error, when it is a good time to try again?

We go back to our example and improve it by indicating which are the allowed values, so the user has info to change their configuration and try again: `"Unsupported language: 'klingon'. Please select one of the following: 'spanish', 'english', 'portuguese'"`.

## Conclusions

Keep in mind those three aspects when writing about error messages. If you got an error message and you need to search for it on the internet, probably it is a poor message. Do not "save time" writing short error messages! And if you are curious about how I implemented the example on this post, here's [the code](https://github.com/ngarbezza/testy/blob/develop/lib/i18n.js#L106) and [the test](https://github.com/ngarbezza/testy/blob/develop/tests/core/i18n_test.js#L69).
## Obvious programming

There's a recurring topic when I teach Object-Oriented Programming and clean code techniques. Students ask me "how do I know if my program is right?" or "how do I know when I should refactor?" or "when I should stop refactoring my code?"

Those are great questions. Answers are hard. Much harder if we try to answer those in a general perspective, without looking at a concrete example. How to say something more than: “it depends” or “use your judgment”?

I wanted to help the students. I started using this mental model for myself, but I thought it would be a good idea to socialize it and share it with you.

The process is simple. Focus on a small piece of code, like a method. if the code you are seeing looks obvious to you, it means there might be no need to refactor it. You can increase your confidence and other people: is this code obvious? So that you can remove the biases you can have on your own code.

Let's look at an example of an obvious method that we can write in Ruby:

```ruby
def selected_items
  @items.filter(&:selected)
end
```

Let's take a look at another obvious code, this time in Smalltalk:

```smalltalk
overThreshold
    ^ threshold >= self allowedThreshold
```

Obvious code is equivalent to **intention-revealing** code. We read the code and then immediately understand what's going on, we can tell its intention. This is strongly connected with good naming: if we don't understand the words, it won't be obvious code. Ideally, we need to use consistent words used by the domain experts. This is a Domain-Driven Design concept called [ubiquitous language](https://martinfowler.com/bliki/UbiquitousLanguage.html).

If the code you are seeing is not obvious, it means at least you can do one improvement to make it obvious. You can detect this problem when explaining the code to others that have not seen it before. Watch their faces and hear their questions. If you need to explain too much, it's not obvious!

Writing obvious code means that we need to do a lot of “extract” refactorings (extract method, extract constant, extract variable). Doing this process manually is not fun. Take advantage of the automatic code transformations your IDE provides. Repeat them until your code looks obvious.

There are exceptions though. Code that won't be obvious no matter how much you refactor it, due to this essential complexity. One that comes to mind is sorting algorithms. Some algorithms use low-level features of the programming language. Some algorithms have optimizations that usually make the code more obscure and complex. And all of that decreases readability and obviousness (my new cool metric!). But, our goal, in this case, is to find this clever, non-obvious thing and encapsulate it. In a class, in a method, in a function. That way, users of this class/method/function can forget about this non-obviousness. The power of abstraction!

During this process, you might find detractors. Some people are better at reading complex code. They might find this “extreme” refactoring technique unnecessary. In that case, avoid a flame war and try to seek a collective agreement. The goal is for everyone on the team to feel well with the code everyone writes.

When I was reflecting on this technique, I had an interesting thought. We all can suffer from a programmer's bias on our own code. The code we just finished writing is perfectly understandable and will still be understandable over time. But that is not always true. As time advances, the understanding might decrease, due to all the context information that is not in the code. Obvious code pays off if a complete stranger looks at a code written 3 years ago and understands it quickly.

Making code more obvious can be seen as an eXtreme Programming practice: something we do and take it to the extreme in order to see the value it provides.
Have you seen or written obvious pieces of code? Did you experience good feelings when reading or explaining obvious code to others? Or reading your own code from years ago?

Let's make our code more obvious, join me!

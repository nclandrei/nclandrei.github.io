---
layout: post
title: Comments are nice until they aren't
categories:
  - Programming
---

I've read a great book from John Ousterhout called [A Philosophy of Software Design](https://www.amazon.com/Philosophy-Software-Design-John-Ousterhout/dp/1732102201) that I strongly encourage every programmer to buy.

It has a list of good recommendations for designing a software system, such as various ways of decomposing your code into re-usable and readable modules or how to better name variables or methods. However, there is one point that I completely disagree with, and that is *comment-driven development*.

What does *comment-driven development* mean? Well, you've probably heard about the ~~horrendous~~ famous TDD approach to programming. When following the TDD cult, you start writing tests first rather than the actual implementation. Well, you guessed it: *comment-driven development* recommends you write comments first and then the actual implementation.

Many times, I've seen programmers struggle with building something new or fixing some bug just because they didn't f**king write down what they're thinking. What is the problem? What am I trying to solve here? Is there any other similar problem I've solved that can help me? (by the way, a great book for developing problem solving skills is [How to Solve It](https://www.amazon.com/How-Solve-Mathematical-Princeton-Science/dp/069111966X) by George Polya)

Yes, *comment-driven development* can sometimes help with that - it forces you to explain in a comment what that specific method or class is supposed to do. That comment will most probably sediment the problem statement and solution you came up with. Heck, it might even spark some alternatives to the original solution. But I don't think comments are the best way to solve those issues.

I think there are a couple of vital characteristics for every software system:

* programmer happiness
* quality
* readability
* performance
* maintenance

Let's take them one after the other and start with programmer happiness. Programmers, simply put, like programming, the act and experience in itself: solving a complex problem, coming up with an elegant solution to something that seems hard to grasp, leveraging the tools at hand, such as programming languages, to write beautiful and expressive code (hey, Ruby!). Have comments ever made you happy? I highly doubt it.

Readability - well, readability is clearly out of the table because when you write comments, you add more **information** to digest and it makes the whole package harder to swallow. Human beings have a [limited short term memory](https://hackernewsletter.us1.list-manage.com/track/click?u=faa8eb4ef3a111cef92c4f3d4&id=8f4bcd54c9&e=d6b0ceb659) and simply bombarding programmers with more information will make them proner to forget or make mistakes, regardless of the specific type of information. 

Another problem in regards to readability is that, if you explicitly need the urge to add comments to your methods or classes, maybe the code itself is not so readable. Preferably, the code should be expressive enough to speak for itself. 

Quality? Well, comments don't contribute in any way to the quality of the code itself as they're not actually part of it.

Performance is not affected at all by your comments - comments might explain why you took a certain performance decision, but they don't directly influence latency or other relevant performance metrics.

And, the cherry on top, maintenance: when you add comments, you've inherently taken the decision that there's another piece of information that needs to be continuously maintained. Change some logic? Better not forget about those comments because, in the end, **no comments are much better than wrong or outdated comments**.

So what would be a better general alternative? Well, in short, properly written **technical designs**. Why?

Simply put, **separation of concerns** - as we try to apply this principle in our codebases every day, I believe we should do that with documentation as well. 

If you have a different medium to write your design, constraints, alternatives, you separate your thinking from the code - you're more focused to actually **think first** rather than jump in and code a half-baked solution. Sure, I am totally for experimenting while you go along with implementing the solution - some rabbit holes might not be caught in the technical design and you find them along the way. However, investing proper time in technical designs will pay off in your velocity and flow of work.

Moreover, we usually write technical designs in apps that allow your team to comment, such as Basecamp, Notion or Google Docs. If you push your design decisions into comments directly in the codebase, they will be mashed with all the code in the PR you create. Let your fellow programmers focus on the code and actual logic in the PR and comment on the design decisions in a different medium, prior to the said PR. Let them also do the thinking first and then review the actual logic.

Having said that, you might come back and ask: alright, but time passes and the technical design might become outdated too, what should be done then? Well, I'm no believer in keeping books of technical designs - they can be thrown away as soon as the team made a decision how to proceed. However, with comments, they are more or less ingrained into your codebase and it's harder to both get rid of or maintain.

Invest a good amount of time in technical designs. Think about solutions. Come with at least a couple of alternatives. Try to see as many rabbit holes as possible. Learn how to become a good writer. And, then, when you're good to go and finally code, think twice before adding a comment.






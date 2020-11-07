---
layout: post
title:  "Book: The Pragmatic Programmer"
date:   2019-09-27
categories: codecraft books
---
* The Pragmatic Programmer
* Andrew Hunt and David Thomas
* Adison Wesley, 2000
* ISBN: 978-0-201-61622-4

This book considers the craftsmanship of software creation from specification through to deployment. It contains a huge amount of excellent advice which should be well understood by anybody who develops software for a living. Although the book is fairly old by the standards of the industry there is little here that does not remain relevant. Many of the ideas are now so completely accepted that they are as much a part of normal procedure as, say, using a keyboard and so seem almost trite but it is interesting to see that this was not always the case and this perhaps illuminates the importance of the remainder. Other elements of the advice here have to some extent been overtaken by modern practices and comprehensive unit testing in particular renders a lot of the suggestions here redundant. That is not to say that this book doesn't suggest unit testing though - it does but the authors perhaps did not consider the extent to which it might ultimately be used and how doing so might confer some of the benefits they expect from some of their other suggested practices. This also speaks to the comprehensive nature of the advice in this book - undoubtedly it was expected that different teams operating under different constraints would adopt different elements of the advice offered and that surely remains true.

The book offers, as the title suggests, a pragmatic approach to software development. This means that the advice is intended to help us produce high quality software efficiently within real world constraints such as tight deadlines, moving specifications, changing teams and, perhaps most importantly, recognising that human beings, with all their faults, are doing the actual work. We are all dealing with these things daily, of course, and have adopted our own practices for doing so. Thus we may find that much of what is in the book is already familiar, especially to those of us who have been doing this for longer than we care to recall, but the book is nonetheless useful - in part because it will inevitably include some new practices, and in part because it takes our own practices that we may have built up slowly for ourselves and formalises them, refines them and documents them, validating what may previously have been little more than vaguely formed notions. With this in mind I recommend doing some of the exercises in the book - they really do help confirm that you have understood the content and are more powerful than you might expect.

This is a great book and would be useful to any software developer.

Merlyn Kline  
September 2019

------

*Here is a quick overview of some of the advice contained in the book*

* #### Chapter 1
  * Previews what is to come.
* #### Chapter 2
  * DRY (Don't repeat yourself). The book starts with this advice and spends more time on it than on most other things. I think that's probably a good thing - following this single piece of advice leads to more all round benefits than pretty much anything else.
  * Make code re-usable.
  * Write orthogonal code, i.e. decoupled so that changes in one part don't affect other parts.
  * To help with orthogonality, write wrappers for any libraries you use so that swapping them out only requires change in one place.
  * Nothing is fixed in stone - write for flexibility.
  * Start by writing "tracer code" - a working sample outline that can be expanded until it is production ready. This gets an early MVP for demonstration, gets you thinking about structure first, gives you an integration platform early in the process, explores proof of principle early and makes progress evident to others.
  * For difficult problems write a prototype. This is different from tracer code in that it is disposable - once you have proof of principle you start over, replacing the prototype with (rather than expanding it into) production code.
  * Prototypes do not need to be actual code (e.g. they could be on paper or post-it notes).
  * Write simple DSLs (Domain Specific Languages) to express business rules. Try to make them isomorphic with specifications.
  * Distinguish between data languages, that just describe data structures, and imperative languages that describe behaviour.
  * Estimates are based on models.
  * Break estimates into smaller components when necessary.
  * Iterate over estimates as you iterate over the code.
* #### Chapter 3
  * Use source control!
  * Use one editor and know it well.
  * Use plaintext files wherever possible.
  * Accept debugging as a constructive and expected part of development work.
  * In-line traces are generally the best debugging tool.
  * Explaining code to a third party is a great debugging tool.
  * Beware of Dollis Hills! (See [Underground Liff](https://doi.org/10.1007/978-1-4302-0003-1_13)). The problem is almost never in the compiler / library / OS / power line.
  * Write code to help you write code (hilarious example of [Perl]({% post_url 2019-09-27-ILikePerl %}) script to generate accessors for Java classes).
* #### Chapter 4
  * We can't write perfect code. Programming defensively includes defending your code from your own errors as well as others'.
  * Design by contract (DBC) guarantees elements of functionality as well as API.
  * [Liskov substitution principle](https://en.wikipedia.org/wiki/Liskov_substitution_principle) - sub-classes must be drop-in replacements for their parent classes in all respects (except function).
  * Unit tests describe contracts.
  * Invariants specify things that must not change.
  * Semantic invariants are in the meaning of the design and can never change, unlike business rules which change all the time.
  * Runtime contract checking is powerful but potentially dangerous (e.g. if checking a contract has a side-effect) and potentially very slow.
  * Fail fast so errors are exposed at the right time.
  * When you think "that could never happen" you are wrong! Add an assertion to test that at runtime.
  * Use exceptions to handle exceptional errors.
  * Manage resource (de)allocation carefully, with clear allocation of responsibility.
* #### Chapter 5
  * [Law of Demeter](https://en.wikipedia.org/wiki/Law_of_Demeter): only call methods on locally created and class variables (unless that seriously impacts performance). That's what delegation is for - to make local wrappers for methods on other objects, so your clients can call them on you without breaking the law of demeter. See also advice re wrapping libraries.
  * Prefer configuration to integration - use metadata to control details of behaviour.
  * Code is for instructions, configuration is for the details.
  * Make it possible to re-load configuration at runtime if you can.
  * Use human-readable plaintext configuration whenever possible.
  * Beware of temporal coupling.
  * Design for concurrency when possible.
  * [MVC](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller)
  * Use a "blackboard" model for complex and changing business rules.
* #### Chapter 6
  * Make sure you know *why* your code works so it's not just luck.
  * Understand performance characteristics of algorithms.
  * Refactor as you go, repeatedly.
  * Refactoring should not change functionality - do that separately.
  * Design testable code.
  * Code generators can be useful but be sure you understand what they are doing.
* #### Chapter 7
  * Don't just gather requirements - dig for them.
  * Try to generalise requirements.
  * Identify "policies" as requirements that may change over time.
  * Identify with your users.
  * Don't over-specify - try to stick with abstractions.
  * Track and measure feature-creep etc.
  * Maintain a project glossary.
  * Beware of assumed constraints when facing apparently intractable problems.
  * Listen to your nagging doubts.
  * Use prototypes to confirm ideas or discover where they are wrong.
  * Don't be a slave to formal methods.
* #### Chapter 8
  * Quality is a team issue - don't allow any "broken windows" to set a low bar.
  * Don't let problems creep up on you or accumulate in small increments (don't become a [boiled frog](https://en.wikipedia.org/wiki/Boiling_frog)).
  * Give your team/project a (light hearted) name & logo and use them a lot.
  * Divide teams around functionality of product rather than job function.
  * Automate everything you can.
  * [The cobbler's children have no shoes](https://tvtropes.org/pmwiki/pmwiki.php/Main/TheCobblersChildrenHaveNoShoes) - don't let that happen to you; use your dev skills for your own ends.
  * Test your tests by breaking your code.
  * Write an automated test for every bug found.
  * Sign your work and be proud of it!
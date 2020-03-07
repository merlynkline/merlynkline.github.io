# Good code has been reviewed

Code review is critical to our success at writing good code but it is an expensive process because it consumes significant resources. It is therefore very important that we understand the goals of code review and how best to achieve them.

Developers typically spend around six hours every week preparing code for review or reviewing other developers' code. This is a large amount of effort so it is important to make the most of it. Code should be prepared for review properly, and comments should be valuable. At Microsoft, 75% of developers review code daily and around half of these do so multiple times per day. This behaviour is encouraged because it is valuable. The primary perceived benefits of code reviews are maintaining code quality and discovering defects but they are also highly valued for knowledge sharing, team-building, mentoring and fostering collaboration.

The main goals of code review are to:

* Improve code quality
* Share knowledge
* Prevent faults
* Collaborate better

## Feeding back

Code review is about giving and receiving feedback, which falls firmly into the domain of "soft" skills (so called because they are very hard). Being good at this is difficult; it takes care and practice. It is the sole subject of many training courses, so I won't try to cover it here except very briefly:

* This is a *peer* process. Set aside your notional seniority. Resist the urge to command your juniors by fiat. Resist the urge to capitulate needlessly to your seniors. Treat everyone as equals.
* All parties to a code review must act with empathy, sensitivity, humility and respect. All must be prepared to engage in a constructive dialogue, and be willing to learn, to change their minds and to compromise. It is important to focus on the desired outcomes: better code, better developers and better reviewers.
* As the author of a code review you must expect criticism of your work, and you must be prepared to accept it as constructive and respond appropriately. When you feel a negative reaction to comments, take time to think it through before responding. Remember that if reviewers do not easily understand what you have done then nor will future maintainers; these will often include you, next week!
* As a reviewer, you must express your comments in a way that makes them easy to receive. Make sure they are in scope and address relevant issues. Provide context and describe the reasons behind your thinking.

## Creating the code review

The first step in making sure that a review will be as effective as possible is preparing the code for review. Reviewers shouldn't be wasting time with obvious problems that could have been caught before the review was raised. The details of preparation will depend on your team and organisation but some common tasks include:

* Using whatever relevant tools are available to check code quality automatically, e.g. static analysis tools, test suites, spelling checkers, linters, formatters, compilers(!) etc. Much of this can often be automated in your workflow.
* Checking for outstanding TODOs and other recorded actions to make sure nothing obvious has been missed.
* Testing the functionality yourself!
* Looking at the diffs in the review tool yourself to get a fresh view of your changes.
* Talking through the changes with another developer before submitting the review.
* Considering carefully how the reviewers will review your code and what they might say.

Small code reviews are more effective so it's sensible where possible to break changes down into small, coherent parts that can be reviewed independently. At Google, 90% of code reviews contain 24 or fewer changed lines, spread across 10 or fewer files. This leads to turn-around times averaging four hours, rarely beyond five and typically more like one. Nonetheless these reviews are considered effective.

Having prepared your code, creating a code review is just a matter of writing a good description of the change for reviewers so they know why you're making it. This should make reference to existing material such as tickets in the ticketing system, user stories or anything else that will help reviewers understand the change.

## Reviewing the code

Once a properly prepared review has been created, it must be reviewed. When reviewing code you will need to consider whether it meets the specific requirements of your team and organisation in order to make valuable comments. If you do make a comment, it should be couched in language that is respectful and helpful, and you should provide supporting information and explain your reasoning. Some common important considerations include:

* Maintainability. If you can't immediately understand a change then it might not be easy to maintain in the future. Remember that broken code which is maintainable can easily be fixed but working code that is not maintainable will be expensive to fix when it inevitably goes wrong; this makes maintainability more important than correctness.
* Code design. Are responsibilities in the right places in the code? Are abstractions at the right level? Are [SOLID](https://en.wikipedia.org/wiki/SOLID) practices being followed? Are recognisable patterns being used?
* Acceptability. Will the solution be acceptable to the users?
* Correctness. Are there flaws in the implementation? These might include edge cases not being catered for, inadequate validation, logic flaws, etc.
* Test coverage.
* Names. Naming things is one of the two hard problems in computing. It really is worth the effort to get it right.
* Nagging doubts. Don't ignore them! If you have any doubts at all, consult the author.
* Dependencies. Are there new external dependencies being introduced? How will these be satisfied?
* Duplication. Almost any duplication of logic should be considered problematical if the code itself doesn't make the necessity for duplication immediately and intrinsically clear. Watch out for new code that duplicates existing code.
* Comments. I don't like comments except in very special cases. That's a subject for another article. If you agree, code reviews should be looking out for them.
* NFRs (or CFRs). Does the code meet your team's and organisations's non-functional (or cross-functional, if you prefer) requirements? These might include security, privacy, performance, compliance, testing, etc.
* Text. For a lot of projects, there should be no literal text in the code that would ever become visible to the user.
* Common pitfalls. Every project has its own collection of these.
* Big picture. How does this change fit into the project as a whole?
* Reinforcement. Positive comments are valuable. If you notice things that you particularly like for any reason, say so and say why (without being condescending!).

Authors of code reviews tend to feel that the most valuable comments are those that point out potential failure modes, those that suggest better implementations and those that identify functional issues. For organisations, the most valuable comments are those that point out maintainability issues because maintenance is the most expensive element of development work for most organisations. Code reviewers are well placed to make such comments as their view of the code is most like that of future maintainers.

In addition to the considerations above it can be valuable to use a code review as a forum to discuss points of interest that might be relevant to the future direction of the code and coding practices. In these cases it's important to think about whether the conclusions of any such dialogue should subsequently be propagated more widely through other forums such as documentation or communities of practice.

## Making the most of it

To help make code reviews as effective and efficient as possible it is a good idea to make condensed checklists for use by authors and reviewers. These should be very brief, or they won't get used, but should nonetheless try to remind authors and reviewers of all the important considerations for your team and organisation.

Because code reviews are expensive, many organisations will want to track their cost and benefits. The value of code reviews is often measured by quantifying the change that results, on the premise that if reviews don't lead to change then they aren't worth doing. This is something of a flawed approach though: it misses the value of education, collaboration, recording and validation that code reviews provide. Unfortunately all of these things are much harder to quantify so are often ignored when there is a demand for simple metrics for analysis. You might also argue that if the amount of change decreases over time then that demonstrates that code reviews are leading to the creation of better code to start with.

Although code reviews are expensive, careful preparation and thoughtful comments will pay dividends in improved code quality, increased knowledge sharing, reduced fault count and better collaboration. Take the time to get this right and you will create better code, better developers, better teams and a better organisation.

Merlyn Kline  
February 2020

---

References

* For more about code review at Microsoft see
  * [Characteristics of Useful Code Reviews: An Empirical Study at Microsoft](https://www.michaelagreiler.com/wp-content/uploads/2019/02/Characteristics-Of-Useful-Comments.pdf)
  * [Code Reviews Do Not Find Bugs.
How the Current Code Review Best Practice Slows Us Down](https://www.microsoft.com/en-us/research/wp-content/uploads/2015/05/PID3556473.pdf)
  * [Code Reviewing in the Trenches: Understanding
Challenges and Best Practices](https://www.michaelagreiler.com/wp-content/uploads/2019/03/Code-Reviewing-in-the-Trenches-Understanding-Challenges-Best-Practices-and-Tool-Needs.pdf)
* For more about code review at Google see [Modern Code Review: A Case Study at Google (PDF)](https://sback.it/publications/icse2018seip.pdf)
* For a great deal more on all aspects of code review see [Michaela Greiler's site](https://www.michaelagreiler.com/)

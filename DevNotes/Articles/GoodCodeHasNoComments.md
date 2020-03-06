# Good code has no comments

Early in my career as a software developer I learned that good programmers sprinkle their code liberally with comments. Having comments everywhere was considered by everyone to be a necessary (and sometimes, I suspect, sufficient) feature of "good" code. This view remains so widely and strongly held that suggesting otherwise is usually met with bafflement. Evidence of this deeply entrenched habit is visible in the vast majority of the code that I see. For example looking in a commercial Perl codebase I have access to right now I find that in 212 `.pm` files up to 65% of non-blank lines in any one file are solely comments, with around half the files having between 10% and 30% of non-blank lines devoted to them. There are many additional comments on lines that also contain code. Obviously this codebase must be a model of clarity if not concision.

These comments in the code are (hopefully) intended to help the hapless reader to understand it properly so that she may more easily add new features, remove bugs and generally improve things. What could be wrong with that? Nothing could be wrong with that. If only it worked that way. The reality is that this is very often not the case - comments are wrong or misleading for a variety of reasons and every time this happens it costs time and effort.

When you come across a bad comment you need to devote more effort to understanding the code than if the comment weren't there at all. If you believe the code does something different from what the comment says then you need to decide which is wrong: the comment, the code or your own sanity. Note that these options are not mutually exclusive and any of them may suffer as a result of your efforts. Getting to the bottom of this takes time and interrupts your flow. Even when you think you've understood it enough for whatever your particular purposes are you still might be loath to change the comment just in case you've missed something, so the problem persists for future maintainers. These future maintainers typically include yourself in about an hour's time, adding further to the frustration.

Many things can cause comments in code to be less than useful. In particular:

- Nobody reads them
- They are often unclear
- They don't get maintained, even during initial development
- They get copied to new code but not updated
- They are often redundant clutter
- They are a sign of weak code

Comments may well be the root of all evil in your codebase. Even the "good" ones are likely to become bad ones over time. Let's look at some of these things in more detail.

## Nobody reads them

When you're in the flow of reading and understanding code you tend to skip over the comments because they aren't really part of it. This habit is reinforced by the features of comments described elsewhere in this article. It's worse when the comments aren't near the code they describe. All that text you wrote in a giant comment at the head of the module so everyone would understand the details of the API? Didn't even see it. All the documentation of the subroutines you grouped at the end of the code for convenient reading? Didn't get that far. The big comment prior to the subroutine declaration that says what it does? Didn't notice it. All these comments are hard to see, especially as they often don't match the string you grepped for to find the code in the first place

## They are often unclear

Developers make their careers out of describing things with great precision using unforgiving computer languages but nonetheless are often poor at describing things in human languages or using those languages correctly, especially when the language in question is not their mother tongue. Many comments are confusing per se before you even look at the code they are supposed to relate to. They assume context or special domain knowledge or transient brain state you may not have when reading, they contain errors of grammar and spelling or ambiguity caused by homonyms or poor wording, or they are sometimes just nonsense. There is no helpful compiler or bug report to tell us when these mistakes are present so they often just hang around forever.

## They don't get maintained, even during initial development

A common source of comments is developers writing down what they intend the code to do, in the form comments in the source file, before fleshing that out with the actual code. This can be a very good way to get your ideas sorted out before getting down to the detail of executable code, and then making sure nothing is missed when the actual code is written. The problem is that as you write the code you discover details that weren't covered by the initial comments and these may even evolve into quite large differences as the code grows. The chances of the comments changing to track this? Quite low, it turns out. Best to write these initial comments in a separate file where they won't get in the way later. You can always paste in anything you really want to keep. Better to explore TDD if you want to write code this way (and you should!).

## They get copied to new code but not updated

This is really a specific case of not maintaining comments even during initial development. Here's a simple example taken from our commercial codebase:

    # XML escape the supplied data.
    sub escapeXML {
       my ($data) = @_;

       $data =~ s/&/&amp;/g ; ## do this first or it spoils all the others!!

And then the next subroutine starts:

    # XML escape the supplied data.
    sub unescapeXML {
       my ($data) = @_;

       $data =~ s/&amp;/&/g ; ## do this first or it spoils all the others!!

Both the comments related to that second subroutine are wrong. In this case it's pretty easy to see what happened: the author quite reasonably started off the `unescapeXML` sub by copying the `escapeXML` sub and modifying it. Unfortunately that modification process didn't include updating the comment that describes the sub as a whole or removing the now-erroneous comment on the ampersand conversion. Unfortunately, not all cases are this clear cut. Understanding errors of this type can take a lot of time and effort away from productive maintenance.

## They are often redundant clutter

Using the same example:

    # XML escape the supplied data.
    sub escapeXML {
       my ($data) = @_;

Really?! Perhaps the subroutine could have had a slightly better name. Perhaps not. The comment adds nothing but a liability with no useful return.

## They are a sign of weak code

This, perhaps, is the worst thing about comments. If you feel the need to put a comment in your code, perhaps you should consider whether that code is clear enough. Almost always it would be better to refactor to use longer names or break things down into smaller classes, methods, subroutines etc and give those more descriptive names. If you are having difficulty naming a method, perhaps it has too much responsibility or is in the wrong context. It always helps to use named constants instead of literals. Sometimes you can make things clearer by changing the logic or the flow of control; often this reveals faults in your original design or implementation. Clear code should not need comments to describe what it does and because the description is in the code itself it automatically tracks any future changes. From this perspective comments are unwanted repetition and if comments aren't the root of all evil in your codebase, repetition of any sort certainly is. That is a point that bears endless repetition.

So if comments are so evil, why did we ever think they were a good idea? The simple answer to that is that they were. Once. When we were stuck with languages that didn't have all those modern features like long variable names, rich control structures, expressive subroutines, classes, methods, deep data structures, and all the other things we can use to express our intent clearly and unambiguously to the human reader as well as the computer. When we were stuck with hardware that didn't have the available memory to properly exploit what language features we did have, nor the speed to do so without significant effect on the user experience. When we didn't have tools like IDEs and VCSs. Back then, comments really were a necessary evil. Working through uncommented assembler is no fun. Trying to divine the meanings of variables with two-letter names or the purpose of subroutines identified only by line numbers is worse than solving a misprinted cryptic crossword. These programs needed comments. The comments were still a liability, for all the reasons mentioned here. But the value of benefit conferred by those comments was greater than the cost of the liability so we put up with them until they became part of the folklore.

There is no doubt that there was a time when comments were a very important feature of good code. That time is past.

In the bad old days the primary purpose of the code was describe to the computer how to perform a task and do so in the most efficient possible manner so as not to waste valuable computing resources. Helping the human reader of the code to understand what was going on necessarily came a distant second. Today, in a world of endless cheap computing resources, that has changed: the primary purpose of the code is to describe intent to a human maintainer. That this is constrained by the requirement to do so in a way that can be reliably understood by a simple-minded computer is sometimes annoying but overall beneficial because it ensures we apply the necessary clarity and precision that is so often missing from comments.

If we shouldn't write comments in our code then what should we do instead? The simple answer to that is write code that doesn't need comments! Modern languages let us write code that is largely free of comments without compromising the readability or the clarity of intent. Care in decisions about how we express our ideas in code can produce results that are as good as any comments we can write and are guaranteed to accurately reflect the executing code, even as it evolves, because they *are* the executing code. Where there are ideas that can't be made completely obvious in the code, we can further elaborate by writing unit tests. Good tests don't just prove that the code works in certain conditions - they also make those conditions explicit so we can tell what the code is and isn't supposed to do, and how it's supposed to do it. Careful boundary analysis and equivalence partitioning in the design of tests is revealing and again the information is guaranteed to stay up to date as the code changes and evolves. Behaviour-oriented tests are explicit in describing the intent of the code.

Having said all of the above, it is true that sometimes we must accept comments as a necessary evil, whose benefits are greater than the their liability cost. Sometimes we are restricted by the language we are writing in, e.g. when passing SQL to a database. Sometimes we are writing code that must interact with something so strange that it will never be clear without explanation. Sometimes we are forced to express the irrational requirements of intransigent users. These cases typically occur at the borders of our own code where we interface with some other system beyond our control. When this happens we must do the necessary but be aware of the problems associated with comments and try to mitigate them as far as possible. The sad truth is that in cases like these we are forced to work with tools that prevent us from building truly good code; nonetheless it behoves us to create the best code we can with the tools we have at hand in the circumstances we find ourselves in.

Comments really are more of a liability than a benefit. When they are not just redundant clutter they are often misleading and cause more problems than they solve. They are difficult to get right in the first place and they are difficult to maintain. Accurate comments are by definition a duplication of your logic. Even when you can succeed in getting and keeping them useful their very presence is a signal that your code and your tests could be better. You might sometimes need comments but they should be reserved for the last resort.

Merlyn Kline  
September 2015

<small><small>See also chapter four of [*Clean Code* by Bob Martin, Prentice Hall, 2008. ISBN-13: 978-0132350884](http://www.amazon.co.uk/dp/0132350882)</small></small>
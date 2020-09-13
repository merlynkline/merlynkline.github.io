---
layout: post
title:  "Good code is broken"
date:   2016-11-21
categories: codecraft
---
When I talk to developers about what they are striving for when they write their code the answers I get most often revolve around getting the code to be correct in the sense that it should implement the requirements correctly and sometimes, in wilder flights of fancy, in the sense that it should be bug-free. I like to think of this as the *Correct Code Fallacy*. I believe it is a flawed approach to writing code. That may seem counter-intuitive but in fact there is something more important than producing correct code and that is producing maintainable code. Even if such a thing existed, a correct implementation which is not maintainable would be difficult and expensive to fix when the requirements change whereas maintainable code which is not correct will, by definition, be easy and cheap to fix.

The Correct Code Fallacy is founded on several assumptions:

* The requirements have been communicated properly.
* You understand the requirements.
* The requirements won't change.
* It is possible to write correct code.
* You can get the code right first time.

These assumptions all turn out to be false.

The requirements have *not* been communicated properly. Where did the requirements even come from? The users of the software don't know exactly what they want until they try something and discover that's not it. Even if they have a very clear idea, their articulation of it will be founded on assumptions of knowledge about the way the system works. They probably won't be communicating their requirements directly to you so someone in between, despite their best efforts, will further muddy the waters. However it's done there is a good chance that what you get won't be a comprehensive description of what's needed.

You *don't* understand the requirements. However carefully they've been communicated, and no matter how hard you've tried, you've misunderstood something and you won't find out what until the users start trying to use your software. Perhaps not for some time after that. Even if the requirements have been communicated properly, you have not fully understood them.

The requirements *will* change. They always do. We don't live in a static universe. Users' needs grow and evolve over time as the world they're working in changes around them. Even if the requirements have been communicated properly and you have understood them completely, they will change before you finish writing the code.

It is *not* possible to write correct code. This has been proven over and over again. There's always a bug. Always. Thus attempting to create correct code, maintainable or otherwise, is doomed to failure. On the other hand attempting to produce maintainable code, correct or otherwise, at least stands some chance of success. Even if the requirements have been communicated properly and you have understood them completely and they haven't changed, there will still be a bug in your code that someone, possibly you, will have to fix.

You *can't* get the code right first time. Nobody can. Writing code is a difficult task which is not very compatible with the way human minds work. We've built a huge array of tools to help us do this task better but we still aren't able to do it perfectly and we probably never will be. Even if the requirements have been communicated properly and you have understood them completely and they haven't changed and there are no bugs in the code, it won't be the first pass at the code that is so impossibly good. To get to that point you will already have done some maintenance on your embarrassing first effort.

All of these things mean that the code will need maintenance so somebody will have to maintain it. When that happens, the maintainability of the code is critical to success. It is well understood that all code has at least one bug and at least one spurious instruction. The corollary of this is that all code can be reduced to a single instruction which would be wrong. It would be wrong because it would be unmaintainable, even if it was technically correct. Any approach to development which is founded on producing bug-free code is flawed. Producing maintainable code is the first step on the path to success. Any useful definition of "good" code must allow for the indisputable fact there will be errors in it; it will be broken.

Merlyn Kline  
November 2016

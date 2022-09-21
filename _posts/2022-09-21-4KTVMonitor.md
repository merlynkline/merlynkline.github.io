---
layout: post
title:  "Using a 4K TV as a monitor"
date:   2022-09-21
categories: hardware
---
I recently noticed that 4K UHD televisions (3840 x 2160px resolution) have become very cheap and wondered whether it would be possible to use one to replace my long-standing twin FHD (1920 x 1080px) setup with one of these. After a quick check that my computers, some of which are quite elderly as these things go, would support a 4K display, I decided to give it a go and see what happened.

After a little consideration I decided that for this experiment there were two key constraints:

* The display should be about twice as wide as one of my existing FHD monitors so that the final result, running at 100% scale, would render things at the same size but have twice as much real estate as my existing twin display, giving the equivalent of four similar monitors in a two by two grid.
* Having no idea how this would work out, I should choose the cheapest display of that size that I could find that didn't have any obvious show-stopper problems.

After a little research I bought a Hisense 43A7100FTUK for £230. This is a 43 inch 4K UHD HDR LCD television with 3 HDMI inputs and a VESA mount so I could attach it to my existing monitor arm on my desk. I disconnected my existing monitors and also the sound system I was using, as the TV obviously has sound built in and HDMI is a supported sound output channel on my computers, and connected up the new TV.

## Results

First and foremost, this is fantastic! It took a little bit of getting used to, and some tweaking of various things to get the most out of it, but having all this screen real estate is revolutionary. Day-to-day work is hugely improved by being able to have so many more windows open and visible at once, the possibility of stretching windows for special cases like showing lots of code or side-by-side pages of a document in a single view suddenly seems indispensable, and giant views of things like maps, 3D models and, of course, video are game-changing. I spend quite a lot of time reading side-by-side diffs for code review and that is suddenly much, much easier! This bottom-of-the-line display was a huge value-for-money upgrade and I can't recommend it enough. I am now looking hard at paying a sensible price for a much better monitor but honestly it feels like, for me, that extra step would be hard to justify.

Having said all that, there are some things that could definitely be improved:

* This display was not designed to be looked at from such a close range so there is a strange effect around the periphery where the back-light appears to roll away under the last few pixels along each edge, leaving them floating and unilluminated unless you move your head slightly towards them. I suspect this would be better on a smaller display, which would naturally have a less acute viewing angle at the edges, or a better panel with a better back light (or even OLED or similar). In day-to-day use this is not a problem and I rarely notice it.
* Colour and contrast are not ideal. A better display would undoubtedly solve this. Careful tweaking of contrast, brightness and sharpness has made this perfectly acceptable for the work I do but if you are doing a lot of image processing you would find this more of a problem.

  * Note that, perhaps counterintuitively, you want to set the sharpness to zero because your computer is providing a perfectly sharp image so sharpening it artificially just introduces strange artifacts.

* Unlike the monitors this TV is replacing, it does not auto-select inputs when a signal appears on them. As I'm using it as a display for more than one computer this means I have to use the remote to select the right input when switching between computers (which involves putting one to sleep and waking another). Although this (and everything) can be done without the remote, the single-button UI is a bit slow and clunky so I do feel the need to keep the remote to hand.
* Similarly the TV does not automatically wake up from sleep when a signal appears on an input, which is again unlike the monitors it replaces.
* Although the TV does sleep automatically after a while when signal is lost, it can't really be configured to do this quickly enough for my liking, and it is very noisy with messages on the display in the period before shutdown. Again, I can just turn it off manually.
* Refresh rate is slow (50Hz) so if you need a fast refresh for games or similar this isn't going to be adequate. Fortunately this makes little difference to me as I don't use it for anything speed sensitive. The TV does have a "game" mode which is supposed to reduce lag and improve responsiveness so I have that turned on but I'm not sure it's giving me any noticeable benefit.
* The surface of the screen is quite high gloss, as seems to be fashionable for reasons I can't fathom. This means that reflections can be annoying if you don't position it carefully.
* Overall I think a slightly smaller screen would have been better for me. Although I didn't think the two monitors it replaces were too wide, I now realise that they were, of course, mounted at an angle to one another rather than completely flat as is obviously the case with this TV. This means that some head movement is occasionally required to peer at things near the edges of the screen with my aged eyes. On the other hand I don't feel that a smaller pixel size would be a problem. For my next monitor I might look at something more like a 36" or 38" panel.
* Managing window layout could be difficult - Windows is not designed for screens with this resolution at 100% scale, and indeed defaulted to 150% when I first connected the monitor. However there are various utilities that offer solutions to this problem and I am currently using FancyZones, which is supplied as part of Windows PowerToys, with a template of my own creation that provides a four by four grid of roughly equal zones.
* Finding the right position for the monitor required a bit of trial and error, and is probably a personal thing. Conventional wisdom says that the top of your monitor should be at the same level as your eyes but I found that this was a little low for a monitor of this size so I have it a bit higher.

All of these problems are really only minor niggles and most of them have good solutions for my application. Balanced against this are all the benefits mentioned above and some unexpected ones too:

* The sound quality is surprisingly good, and I don't need high fidelity for my uses anyway, so I have been able to remove three speakers, a USB sound card and a lot of cable from my desk. There is an optical out for sound so it would be easy to upgrade with a soundbar should I wish to, without adding any more cables or switches to the setup.
* Each computer now needs only one monitor cable rather than two so that's a lot more cable gone from my desk.
* The TV is of course a "smart" TV with all the features you would expect. This is fortunately completely unobtrusive when used as a monitor (e.g. it doesn't insist on returning to the home screen on power-up) but it is there if I should decide I want to watch Netflix or something at my desk when other TVs are in use by others.
* It has USB ports which are provided so you can play media from USB sticks and similar. I don't use them for that but they are a handy power source for the video light I have mounted on top of the display for use in conference calls.

All in all, having a 4K monitor is a huge improvement over all my previous setups and this cheap TV has filled the role surprisingly well. There are some minor issues but they aren't really affecting me and I will make sure they go away completely with my next upgrade.

Merlyn Kline  
September 2022

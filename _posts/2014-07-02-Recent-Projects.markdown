---
layout: post
---

Lately, I've been toying with a lot of project ideas. I ought to write them
down here, just to keep track of what I've been up to this summer.

Rhythm Gate
-----------
[Rhythm Gate](https://github.com/Speculative/rhythm-gate) is a rhythm game
that was conceived at HackRU, where it won an award for Best Designed. The
idea is very simply to "break" the gates to the beat. I feel that this concept
actually has strong potential to be a fun rhythm game (with additional
variation in beatmaps - some other mechanic that would mesh well). Our main
roadblock to working on it further is that it was written in PyGame
and we're having massive FPS issues.

At the moment, the plan is to reimplement it from the ground up (it is a
hackathon project, after all, and is just a minimal proof of concept).
I've also been itching to write some C after the Advanced Programming course
I took this past semester, so I've been toying around with SDL.

Our team consisted of myself, [Max](http://maxwell.huang-hobbs.com/),
[PJ](http://pjrosa.com/), and [Gabe](http://studio-cation.com/).

SCP Wiki
--------
I wrote [a bookmarklet called SCP-Names](https://github.com/speculative/scp-names)
that scans a page for SCP-#### designations and appends the "pet names" that
seem to be listed only on the main SCP lists. Someone on /r/scp had requested
it, and though it seems someone beat me to the punch on making a greasemonkey
userscript for it, it was still a fun little project.

It seemed like a waste of time to go scraping the wiki for a single name every
time I wanted to use the bookmarklet, so instead I wrote an associated python
script that performs a full scrape of the wiki and then dumps it out as JSON.

My next project is probably going to be a terminal-based SCP wiki browser. I
can't think of a great reason for why anyone would use this, other than the
fact that browsing Foundation documents in a terminal sounds cool. That's
basically why I'm writing it, after all.

Other Projects
--------------
[0x40 Hues](https://github.com/nol888/0x40hues) - Nolan invited me to help him
out with this one, but I've been a bit busy lately, and I still need to get
around to fixing my Fedora install first so I can develop.
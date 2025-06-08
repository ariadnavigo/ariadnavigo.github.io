+++
title = "A Case of Bad Journalism: Git Rebase Drama on the Linux Kernel"
date = '2025-06-09T14:46:51+02:00'
author = "Ariadna"
tags = ["news", "linux", "journalism"]
keywords = ["linus torvalds", "linux", "kees cook"]
+++

You may have read [the news][rebase-phoronix] last week. Linus Torvalds exploded
on the [LKML][linus-lkml] because of a series of modifications on Kees Cook's
tree that he thought of being a malicious attack against the Linux kernel code.
Cook has been a kernel developer for a long time, so him being accused of
malicious intent by Torvalds himself of course made the news as soon as it
happened. After some back and forth, [developer Konstantin
Ryabitsev][ryabitsev-lkml] was able to trace the origin of the problem. Some
script Cook used to rebase some of his commits had run amok and unintentionally
corrupted his tree. After realizing what had happened, Cook was reinstated as
part of the project. No harm was done; business went on as usual after that.

But the FOSS "press" was so quick to milk the clickbait on this one. Just on
YouTube, you'll find videos like [this,][savvynik-1] later expanded with the
full resolution of the story [here;][savvynik-2] or [this one][robertson] and
[this other one in Spanish.][librebyte] On the written side of things, you may
find articles like [this one][itsfoss] and probably many more. All of these, of 
course, resort to titles where they bait potential readers with the possibility
of some malicious _intent_ of attacking the Linux source tree, even when the
video or the article itself later backs off that narrative by explaining how
everything was just a misunderstanding due to a faulty script, no bad intentions
whatsoever.

Phoronix, on the other hand, did the right thing in the article I cited first,
by using a way more neutral title: _Apparent Git Scripting Issue Raised Concerns
Of Possible Malicious Linux Kernel Activity._ However I have noticed that the
article URL says _Linux 6.16 Git Gone Wrong._ I don't know if that was an
earlier title that was later edited as soon as new information came in, but in
any case even that apparent first title is way more cautious than just assuming
Torvalds' instinctive (and quite understandable, though rushed) first reaction 
at face value.

Even Linus can get things wrong from time to time. Therefore, when reporting on 
something that is still a developing story that can go either way, the
ethical move is to wait. We're living in an era of fast-paced media and there
are reasons for publishing an ongoing story. The problem is when people jump to
conclusions that imply something really bad and big happened, without proof or
an analysis yet.

This is surprising to me in a niche like FOSS, where you'd expect people to be
more methodical precisely because most of us have some relation to coding,
computers, etc., so we should know that many, many times things go wrong not
because of bad intention, but because we're dealing with complex systems and
mistakes can easily be made. This goes for Linus himself, of course, although I
can't even start to imagine the amount of stress he carries over his shoulder as
the leader of such a critical project like the Linux kernel. Stress often leads
to bad emotional management (but no, I'm not justifying Linus's first reaction
accusing his colleague of _bad intention,_) but I'm sure the FOSS press can do
better.

[rebase-phoronix]: https://www.phoronix.com/news/Linux-6.16-Git-Gone-Wrong

[linus-lkml]: https://lore.kernel.org/all/CAHk-=wj4a_CvL6-=8gobwScstu-gJpX4XbX__hvcE=e9zaQ_9A@mail.gmail.com/

[ryabitsev-lkml]: https://lore.kernel.org/all/202505312300.95D7D917@keescook/

[savvynik-1]: https://www.youtube.com/watch?v=Zu42F-XMNC4

[savvynik-2]: https://www.youtube.com/watch?v=NnrMmq8Sf44

[robertson]: https://www.youtube.com/watch?v=GhfhzTDQdUU

[librebyte]: https://www.youtube.com/watch?v=AQBx_N3-R1c

[itsfoss]: https://news.itsfoss.com/linus-kernel-broken-pull-request/

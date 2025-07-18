+++
title = "S2idle and the Nature of Laptops"
date = '2025-07-18T01:20:18+02:00'
author = "Ariadna"
tags = ["linux", "hardware", "my stories", "history"]
keywords = ["s0ix", "acpi", "linux", "hardware", "sleep states"]
+++

You tell your laptop to "suspend to RAM." Are you really sure that's what your
system just did? I was utterly surprised, when I purchased my current laptop (a
model of the HP 15 series that was released in 2024), that _things had changed_
since the last laptop I had bought back in 2013.[^1] Power management has
changed, and things smell a lot of "mobile convergence" these days.

It's funny because using Linux shields you from one day discovering that your
10 years old PC doesn't work anymore. Linux supports old systems by philosophy,
where MacOS and Windows are picky about how old your laptop, especially laptops,
is. Microsoft is more lenient on the software side of things, such that only
until recently you still could run old Win16 software on modern Windows
versions. When it comes to hardware, though, good luck if your system doesn't
come with UEFI and "certified" hardware, and good luck in a couple of years.

So, while my old ASUS X55C had the power management features of a laptop from
2013, this HP 15 showed me that... even "sleep" had changed. I'm not sure how I
noticed that something was different; I guess it was the fact that waking up
from "sleep" was super fast on this new laptop, with almost no time delay when
it came to reconnect to WiFi or Bluetooth. I immediately knew where to look at
and I was presented with something I had never met before:

```bash
$ cat /sys/power/mem_sleep 
[s2idle]
```

In the old days of regular ACPI S3 sleep, you would've seen a couple of
different options, like ``shallow`` and ``deep``, the latter being what we would
usually call _Suspend to RAM_ or "actual" S3. The ``shallow`` setting is for
_Suspend to standby._ For more details about what all of this means, there's
[documentation available][sleep-docs] from the Linux kernel itself explaining
everything in detail.

The way the sysfs files for power management work is interesting. Files under
``/sys/power`` only show power states that your system _supports._ That
information, obviously, comes from the firmware itself. Therefore, if I tried
something like the following on **my** computer to put it to sleep into an 
actual, traditional S3 state that powers everything off except for the RAM...

```bash
# echo 'deep' > /sys/power/mem_sleep
bash: echo: write error: Invalid argument
```

So, my new laptop only supports a sleep state that is implemented somewhere in
the motherboard (the UEFI?). The important bit about S0ix/s2idle states is that
there are different levels, beyond the one that is equivalent to _suspend to
RAM;_ you actually may enter a S0i1 state just by having your laptop sitting
idle with the screen off: some parts of the motherboard may enter a low-power
state, some not... Deeper S0ix states will power down more parts of the hardware
or make them enter deeper low-power states. Of course, this requires the Linux
kernel to support requesting these states to the firmware.

If this sounds oddly familiar to how smartphones manage power, bingo. Although
smartphones, as far as I know do not follow ACPI specifications, the behavior is
practically the same. When you leave your smartphone sitting idle, it selective
powers down components to save energy. On Android, this is called
[Doze.][android-doze]

You know what else has happened to laptops in the last couple of years? I
noticed this when I got this new one. They're significantly harder to extend, to
open up, etc., if not straight impossible. I can't even upgrade the RAM on this
one. It's a sealed box, meant to stay as is since it was shipped from the
factory. If you have the skills to get inside it, you may do so, but it is not
meant to be opened. My old laptop, and any old laptop, at least allowed you to
change some components: I had replaced my old ASUS's spinning hard drive with a
SATA SSD drive, extended its RAM with a pair of extra modules, and replaced its
battery pack. All three upgrades or repairs are _hardly possible_ on this new HP
15.

There are many reasons why we got into this situation, Apple being an important
part of it (I guess), but there's also another side to this. If you, the vendor,
can predict that the hardware _will_ stay the same, then you are able to
implement coherent power management to all components the laptop ships with. One
of the issues with traditional S3 sleep, or even S4 (_Suspend to disk,_ aka
"hibernation"), was that many times hardware components entered undefined states
when woken up. This wasn't even a problem exclusive to Linux: this could happen
to you on Windows as well. The reason behind this is obvious: the platform was
open, you couldn't predict whether the user was going to change or upgrade
something that would break when entering a sleep state because of drivers or
your firmware not being able to handle that new component correctly.

I'm under the impression that a modern laptop is much closer to a smartphone 
than to a desktop workstation. We're still on a version of x86 that internally 
complies with the PC standard, but laptops have evolved to behave much more like
our ARM-based smartphones. My HP 15 is practically a big tablet running Arch
Linux. And you know what? There are improvements to take into account from this
historical evolution.

I no longer suspend to disk. I know this laptop will _reliable_ wake up every
single time I ask it to suspend _to idle._ I know my WiFi and Bluetooth cards
will resume operations immediately as soon as I wake the system up. I know there
will be no graphical gitches or slow-downs either (I was plagued with these on
my old ASUS, thanks to the infamous i915 driver). I'm not even getting into the
improvements in battery life that come from smart charging and discharging
implemented in UEFI and modern power supplies.[^2] Suspending to disk was sort
of a workaround I used to save power and time, but also had its own issues
(again, the i915 driver, but sometimes also networking). Now, with this new 
machine, I know things will work out of the box (unless some kernel update
breaks something, which is unlikely).

Convenience is mostly always good, unless it comes with ethical problems [like
generative AI does.][ai-post] Of course, everyone's definition of _convenience_
will be different. Arch Linux is convenient for me, but you might find Ubuntu
more convenient. We all love tinkering and I guess we all also love the thrill
(and frustration) of fixing an unexpected issue on our computers, but in the end
of the day, we use these machines _to get things done._ And I'm someone who
wants her computer to be up and running as fast as possible from a sleep state
because I might want to write down an idea on a text editor and close the lid a
minute later.

I understand the problems with closing down the platforms we use daily, though.
I'm also nostalgic of the past where PCs were your hardware playground. Laptops
were always more closed down than desktop PCs, but still. However, I do wonder
whether we were compensating for the shortcomings of the industry back in the
90s and 00s. Gravis, from the [_Cathode Ray Dude_ YouTube channel,][crd-yt] has
repeatedly stated that the PC platform has become a "solved problem" in the last
decade or so. There are no weird hardware experiments like [strange PCMCIA
cards,][crd-pcmcia-camera] peripherals have all converged into USB devices
(maybe HDMI is the only standing exception), WiFi has become practically
universal (this laptop does _not_ have an Ethernet port), we have gotten rid of
SD cards and other external storage devices... I am surprised that my new laptop
comes with a headphone jack. The PC in general, but probably laptops even more,
have become "boring." This is the price of having figured a technology out, I
guess.

[^1]: I tried to extend its life as much as I could, but after 11 years of many
    repairs, changed parts, that old ASUS X55C was in no shape to be my main
    system at all. It was a sad goodbye, because I wrote my PhD thesis on it,
    and we had endured many, many adventures together.

[^2]: Have you noticed the data pin living inside the DC connector?

[sleep-docs]: https://www.kernel.org/doc/html/latest/admin-guide/pm/sleep-states.html

[android-doze]: https://source.android.com/docs/core/power/platform_mgmt

[ai-post]: /posts/ai-not-friend-not-therapist.md

[crd-yt]: https://www.youtube.com/@CathodeRayDude

[crd-pcmcia-camera]: https://www.youtube.com/watch?v=xpJBHjl9_VU

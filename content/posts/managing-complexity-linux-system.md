+++
title = "Managing Complexity in a Linux System"
date = '2025-06-16T18:18:48+02:00'
author = "Ariadna"
tags = ["linux", "virtualization", "sysadmin", "my stories"]
keywords = ["QEMU", "virt-manager", "Linux", "SysAdmin", "virtual machines"]
+++

This story begins with me innocently wanting to try out [virt-manager,][virtman]
a graphical frontend to libvirt, which in turn is a tool to manage virtualized
systems, with support for various hypervisors. In my case, my hypervisor of
choice has been QEMU for many, many years. Little did I know when I started my
testing what virt-manager was going to do to my setup.

The way I use QEMU reflects the reason why I use it in the first place. I keep a
FreeBSD VM and an OpenBSD VM to test the portability of my code when I'm writing
a project. The reason why I do so is because on Linux testing against MUSL is
trivial and substituting Clang/LLVM for GCC is also a trivial thing to do.
However, when it comes to POSIX portability, one might get _funny results_ when
dealing with syscalls or with UNIX commands. Even though neither of both BSD
systems mentioned is 100% POSIX compliant, stepping outside the Linux bubble is
important to ensure that your code is more portable than what you might think
from only reading the _Standards_ section of manpages on Linux. So, these two
VMs are two very simple systems, without any graphical environment, and that I
fire up every once in a while on my desktop. I don't keep them up for more than
half an hour when I use them. So, accordingly, I'm used to fire them up via a
simple shell script that is just one single line:

```bash
#!/bin/sh

qemu-system-x86_64 freebsd.qcow2 --enable-kvm -m 2G \
    -net user,hostfwd=tcp::10022-:22 -net nic
```

The one for OpenBSD changes the forwarding port to 10023, so that SSH doesn't
comply because of mismatching fingerprints, and it also changes the filename of
the disk image accordingly. In everything else, both scripts are identical.

I wanted to have a look at virt-manager because I had many years ago and found
it a bad knock-off of VirtualBox's UI. For some time now I have transitioned
from a very terminal-heavy setup based on sway as my WM to GNOME for reasons
that I might talk about in a future post. So, this script-based approach comes
from those old days and I truly wanted to know whether I was missing something
or not by keeping my "VM management" based on having a script for each VM
available.

So, I installed virt-manager and yes, it works, but here comes the huge caveat.
As you might already know, access to host resources from the guest system is
limited due to security restrictions, most notably networking, but also libvirtd
(a daemon) also requires access to files. Therefore, you need to ["authenticate"
with libvirtd][libvirt-arch-wiki] as a pretty good trade-off for not running any
of this as root. On the other hand, virt-manager is able to do some of this
authentication "automagically" behind the scenes if it fails to gain access to
the files it needs. And it will try to do so.

One of the best permission practices on modern desktop Linux systems is to set
your home directory permissions to 700 and never touch umask. Setting your home
directory to 700 makes it and all its contents inaccessible for every user
regardless of what your default umask is. In part this comes from the increased
complexity that umask has gained in the past years due to systemd and polkit
overriding the traditional umask set at ``/etc/profile`` and ``~/.profile``.
Most users will have what they need by closing access at ``$HOME`` itself,
without worrying about anything else.

Well, virt-manager of course needs access to your VM images outside
``/var/lib/libvirt``. [The Arch Wiki][libvirt-group] will tell you to give the 
libvirtd-qemu system user access to the folders you'd like it to have access to,
but if ``$HOME`` is set to 700, how will Libvirt or virt-manager be able to
traverse down the tree inside your home directory? Well, I was shocked to see
that it could even if I didn't open the permissions up for my home directory.

Any seasoned Linux user knows that the user-groups-other triplet is just a part
of the story. ACLs are a thing too in some filesystems, and they are in ext4,
for instance. As soon as I saw this output in my shell I knew what black magic
virt-manager had practiced in order to gain access to my VM folder without using
``chmod``:

```bash
[ari@arch home]$ ls -l
total 20
drwx------+ 25 ari  ari   4096 jun 16 16:27 ari
drwx------   2 root root 16384 ene  2 12:47 lost+found
```

That plus (+) sign means that ACLs have been set in place for that file or
directory and that you should list them with ``getfacl`` and maybe reset them
using ``setfacl -b``. Libvirt had given itself access to the insides of my home
directory using ACLs, which isn't a bad practice _per se,_ because setting the
basic "other" permissions for the directory to full access would have been even
worse. However, the problem is that it didn't asked me or told me anything about
setting up a special extended ACL for itself.

What has this to do with managing complexity? The answer lies in the fact that I
went back to my small scripts setup. Although I can see the advantages of
virt-manager or even GNOME Boxes (another Libvirt frontend), my use case doesn't
need systems with a big array of virtualized hardware, and my VM library won't
ever grow beyond a couple more systems (I had a Windows 2000 VM for years, just
for nostalgia's sake). So, the reasonable amount of complexity I need is closer
to having a couple of scripts to fire QEMU manually whenever I want, with no
special daemons running in the background, no special permissions set, no
frills. If I was running a pool of VMs that was running in the background, meant
to be accessible by more users that just mine, then a set up based on Libvirt
would make a lot of sense, but that is not the case here.

Whenever we set up a system, we should set it up for what we are doing with it
and meaning to do with it. Falling into perfectionism only ruins our lives. Yes,
from a theoretical point of view, it's very likely that Libvirt is a great
solution, but when we're using our systems, it is for a certain set of tasks
that is particular to our needs. I don't want nor need a complex permissions
systems taking over my home directory in order to make VMs work. So, firing QEMU
semi-manually makes sense. Let's stick to what makes sense in our own context.

That's why everyone's system looks and "should" look different. When
recommending tools, environments, DEs, web browsers, or anything else, we should
always keep in mind that everyone has a different picture of what their needs
are and how they should be approached. I'll always claim that technology is much
more personal than what it looks like _prima facie,_ even in surprising areas.

So, I'm sharing this little story to show that, especially on your personal
computers, you're managing the complexity of a complex system according to your
needs. If you feel that things have become too cumbersome for what you use your
system for, I invite you to try a new setup. These are tools, they should serve
us, the users, as we need them to.


[virtman]: https://virt-manager.org/

[libvirt-arch-wiki]: https://wiki.archlinux.org/title/Libvirt#Set_up_authentication

[libvirt-group]: https://wiki.archlinux.org/title/Libvirt#Using_libvirt_group

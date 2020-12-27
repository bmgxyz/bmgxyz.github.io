---
layout: post
title:  "Keep the Sand in the Sandbox"
date:   2020-11-28 19:15:44 -0500
---

This is a metaphor for the separation between stuff on a computer that's fun to
play with and the stuff that must work.

Recently---well, for some years---I've been keeping sand in my house. Sand is
miscellany. Stuff that once had half a purpose. Development files, unfinished
projects, half-baked ideas. My house is the structure I use to live, and the
appliances inside it. These are my email client, my Web browser, my Emacs
configuration. Without these, the computer isn't mine.

Sand piles up. Maybe I spent a weekend working on a project and installed some
software. And that software needed a weird network configuration. But then I got
bored, and who wants to clean up all this sand? Curiously, few tutorials explain
how to clean up.

And this has been a problem for me, because sometimes stuff that I needed to
work would turn out to have sand all over it. You can't use a toaster with sand
all inside it any more than you can use an email client on top of a borked
network stack. Sand is no good.

Last week I decided to upgrade my machine from BunsenLabs Helium, a flavor of
Debian 9 Stretch, to Debian 10 Buster. Debian 9 is reaching end-of-life, and
after a few years under this OS it was time to upgrade. And I knew it would be a
struggle, but I also knew it would be an opportunity to make things easier.
That's when I had this idea: keep the sand in the sandbox. With virtualization
technology, I could make neat little sandboxes and put all the sand in there.
Suddenly I didn't have to keep sand where I was trying to sleep and eat, but I
could still play with the sand whenever I wanted to.

So I installed Debian, and it didn't take long because it's easy enough to
install. And then I stepped back and thought, OK, have a new house now. Gotta
keep it clean. I need to be thoughtful about what I install here, because if I'm
not, then I'll end up with sand in my house again. From now on, no more sandy
toast. I made a list of stuff I had to have: `fish`, `emacs`, `git`, some
dependencies for Doom, etc, and I installed and configured them. And I was even
careful about installing stuff at the user level when I needed to[^1]. And after
all that, I converged to a point where there wasn't anything more that wanted to
add. I'll never finish tweaking, but I intend to avoid big changes from now on
without thought and testing.

And that's it. That's my house. But the sandboxes are there whenever I want
them. `qemu` and EC2 are never more than a few clicks away. Yeah, there's a
little more configuration at the beginning---you have to be more thoughtful and
deliberate about what you're trying to do. It's not quite as easy as `sudo
apt-get whatever-you-want`. But in exchange for that initial effort, you get the
ability to dust off your hands and leave when you're done.

Hey, wipe your feet.

[^1]: For example, Emacs 27 isn't available in Debian 10 stable yet, but I need it for Doom, so I compiled it from source and installed it in my home directory.

---
layout: post
title:  "Be Careful with Undefined Behavior"
date:   2021-02-19
---

I had a good laugh last week with a coworker / friend. She was reading some
documentation for a library we're using for our project, and it warned that some
actions would result in "undefined behavior". The author had a little fun with
the docs and went on to say that "undefined behavior" can mean *anything*,
including nasty stuff like formatting your hard drive.

Of course, nobody really thinks of that kind of thing when they imagine
undefined behavior. Mostly we expect a segfault, garbage output, or an outright
error that gets caught or thrown later down the line.

Nobody, that is, except us.

What if, we thought, the compiler did some really bonkers stuff when you wrote
some code with undefined behavior? Pretty soon we had a joke formula. It goes
like this:

> I tried to [thing that causes undefined behavior], so the compiler [outlandish
> thing that would really ruin your day].

Examples:

> I tried to bitwise shift a negative value, so the compiler randomly adjusted
> the Hubble constant.

> I tried to divide by zero, so the compiler set P=NP.

> I tried to use an out-of-bounds index, so the compiler has removed the number
> 6 from the universe.

Try it out, it's fun. For the first part, you can refer to the [source of all
human knowledge][0] (that's what I did). You have to bring your own funny for
the second part (sorry).

This reminded me of a few somewhat-related bits of media and esoterica, such as
[this classic xkcd][1] and the impenetrable [INTERCAL][2].

Finally, I should mention that my counterpart suggested that it might be "fun"
to build a compiler that has some *real* ability to do bad stuff when presented
with undefined behavior.[^1] At compilation, it would replace any cases of
undefined behavior with a random routine from the Table of Doom. This is,
without doubt, a bad idea. But maybe it *would* be fun, at least at little.

Come to think of it, `clang` might have support for this behavior...

[0]: https://en.wikipedia.org/wiki/Undefined_behavior
[1]: https://xkcd.com/293/
[2]: https://en.wikipedia.org/wiki/Intercal

[^1]: This would be much more localized stuff than adjusting universal
      constants, but still bad. Imagine if the compiler made your program email
      your browser history to all of your contacts.

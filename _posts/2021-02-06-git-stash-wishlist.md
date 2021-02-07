---
layout: post
title:  "Git Stash Wishlist"
date:   2021-02-06
---

`git stash` has become an important part of my `git` workflow. It's useful the
way it is, but I have two tweaks that I think could make it a bit better (for
me, mostly).

## Checkpoint

When I'm writing code, sometimes I get to a point where *something* is working.
You might know this feeling, too. You've been pushing to get a feature doing
what it's supposed to, or the bug you found is mostly gone, or whatever. But
it's not quite you want it. Something is missing, or not quite right, or messy.
It'll need more work, but at this exact moment things are somehow in a
metastable state that's worth saving.

This is a problem in disguise. If I make a commit at this position, I'll end up
splitting one atomic change over two commits. What I've got isn't enough to
stand on its own, but if I don't somehow save my work, I risk losing track of my
metastable working directory. I need something that lies between the calamity of
my working directory and the pristine historical documentation of a full commit.
This is one of the use cases for `git stash`. Using something like `git stash
save -u "descriptive name"`, I can push my changes onto the stash stack.[^1]

But now we have a new problem. By `stash`ing my changes, I've removed them from
my working directory. What I really wanted was to make a stash but keep my
working directory untouched, so I can continue on until I'm ready to make a full
commit. No problem, you may think. Just apply the stash back to your working
directory with `git stash apply 0`. And you're right, that works fine, and it's
what I do in this situation.

My tweak: I think `git stash` should have a subcommand for this `save`/`apply`
process. I propose that it be called `git stash checkpoint`. Here's how it
works:

```
$ git status
...
<various changes>
...
$ git stash list
$ git stash checkpoint "descriptive name"
$ git status
...
<same changes as above, completely untouched>
...
$ git stash list
stash@{0}: On <branch>: descriptive name
```

By rolling what would be two commands into one, you could save *tens of minutes*
across a career in software. (Never mind that you'll [spend][0] a good deal of
that saved time reading this post.)

[0]: https://what-if.xkcd.com/22/

A proper implementation would modify `git` to support this new subcommand, but a
simpler alternative would be to write a script that does the same thing. Have I
done this? No. But it wouldn't be too hard, I think. The disadvantage would be
that it couldn't be a part of the normal `git` syntax. As for the proper
implementation, it could do the very same thing internally (call whatever `save`
and `apply` methods already exist), or perhaps something more clever.

## Drop Multiple

If that edge case wasn't edgey enough for you, try this instead. You know how
stashes form a stack, right? And when you `git stash drop` some stash index, all
the other stashes above the one you `drop`ped get shuffled down to take up the
empty space. That's fine if you just want to drop one, but what if you want to
drop *more* than one?

Well, that's easy. First you `drop` the lowest index you want to delete---say
it's `2`---and then proceed to the next, and the next, until---wait a minute.
That doesn't work, because as you `drop` the lower indices, the others change
before you can `drop` them, too. So really you have to start at the *largest*
index and work your way down, or somehow keep track or check the indices after
every `drop`. What a drag.

Consider the alternative: multiple `stash drop` index arguments. For example:

```
$ git stash list
stash@{0}: On <some-branch>: doc updates
stash@{1}: On <some-branch>: testing alternate design
stash@{2}: On <some-branch>: failed attempt to fix bug
stash@{3}: On <some-branch>: add more buttons
stash@{4}: On <some-branch>: remove the letter g from all source files
stash@{5}: On <some-branch>: add a few bugs just too keep us in business
stash@{6}: On <some-branch>: append Flesch-Kincaid readability score to every file
$ git stash drop 4 1 5 6
Dropped refs/stash@{0} (d161d66841304f4f67aad65ab027e24ff1b65603)
Dropped refs/stash@{0} (6d96270004515a0486bb7f76196a72b40c55a47f)
Dropped refs/stash@{0} (358f544b8d7de6f87bf2bfbe3b6abe1b62bea09d)
Dropped refs/stash@{0} (e74ae340451f83ff617816017bb9a0a26f380040)
$ git stash list
stash@{0}: On <some-branch>: doc updates
stash@{1}: On <some-branch>: failed attempt to fix bug
stash@{2}: On <some-branch>: add more buttons
```

This little change could save you *even less time* than the previous one, but
wouldn't it be cool? This is also a decent candidate for a script.

Maybe someday I'll fork `git` and add these features in for myself. Or maybe
I'll just write about them online. I understand why the `git` engineers probably
don't want to incorporate every bozo's pet ideas. They need to adhere to the
Unix Philosophy, and I respect that. But I can dream.

[^1]: A friend told me that stashes are actually commits as well, but they
      differ from normal commits in important ways that I don't remember.

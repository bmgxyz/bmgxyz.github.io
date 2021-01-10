---
layout: post
title:  "Database of Things"
date:   2021-01-09
---

I have a lot of stuff. A lot of it is stuff that I don't need. But I've also got
plenty of stuff that I don't need right *now* but that I might want later. Many
of the items in this category are hobby electronic components and devices.
Parts and pieces tend to pile up because different projects need different
kinds of components, connectors, etc., and storing them is often easier than
disposing of them.

But storing stuff is hard. What I've done in the past---and what I guess many
others must do too---is less like storing and more like stirring. Everything
goes in one or a few bins or drawers, and when I need something I push it all
around until I spot the specific sprig of silicon that I want. Put another way,
the organization method I've used in the past has been to mostly not have any
method at all.

One real method I've tried is to categorize items. *Oh, this is a magnetometer
and it's a small board, so it goes in the small board sensors bin.* Or: *these
are resistors, so they go in the passive components bin.* And then there are
subdivisions based on size, intensity, function, color---that kind of thing. The
core quality of this system is that you come up with the rules as you go along.
The problem is that this often requires more work than it's worth because of all
the refactoring you have to do when new items arrive. *Oh, it turns out that
there's a type of eInk display that's too large for the container I provisioned
for them. Now I have to come up with an exception to this rule and put this
particular display somewhere else. Or I have to move my existing eInk displays
into a larger container. Or I have to make a pair of subcategories: this drawer
is for eInk displays under, say, four inches, and that bin is for the rest.*
Pretty soon you have more exceptions than rules, and you're not tracking them
anywhere, so it's back to stirring[^1].

## Solution

I started using a better solution about a year ago. For me, the trick was to
model a filesystem. Think of each item as a file. It's a discrete collection of
data (atoms) that together forms a useful unit. Maybe you can subdivide it a
little (e.g. a book of resistors), but mostly it's a single item. Once you have
that abstraction, you can do some interesting stuff.

I took stock of my containers. I had some small plastic drawers in a case, a few
larger drawers, and a couple of big bins. These are the blocks in my filesystem.
I assigned a number to each one, giving each block an address. The last setup
step was to create a file allocation table. I used a SQLite database, but I'm
sure you could get away with almost anything. Even pen and paper would work.
Anything you want, as long as you can do basic CRUD.

To use the storage system, act like an operating system. For each item, write a
little description for it in the database. You can also include a quantity or
condition field, or anything else you care to. Then, find a place in the
drawers and bins where the item will fit. Try to find the smallest container
that will fit the item, since this will make searching easier. Note the
container number in the item's row in the database. You've just written the new
file to disk. To read the file back and retrieve the item, use any applicable
interface to search your database. When you find the item you want, go to the
container with the correct address number and grab the item.

You may say that this is a simple system. You're right, and I thank you for the
compliment. This system doesn't have to be that complicated, and indeed it
shouldn't be. In fact, the simpler it is, the more likely you are to use it. If
you want to, you can add arbitrary complexity to it, but you don't have to.

You may instead say that this is a lot of work. You're right about that, too.
But in my view that's a property of having a lot of stuff, not the way you
store it. Stirring bins around and not knowing where stuff is takes work, too.
We're just moving the work around.

## Bottom Line

Costs

 - You must maintain an up-to-date database. If you don't, then the filesystem
   will return bad blocks, and you might not be able to find stuff quickly or at
   all.
 - Database management takes time and effort that you could spend doing
   something more fun or interesting.
 - Like any other storage system, it's vulnerable to external rearrangement and
   corruption (e.g. by a child).

Benefits

 - You can put any item anywhere you want in the system. Put it wherever it
   fits, not where it "belongs".
 - If you have access to your database, you can find anything in the system in
   constant time[^2].
 - The system scales up and down with constant cost per item.
 - You may apply the system to any subset of your items, if not the full set.
 - You don't have to remember anything.

I'm sure other people have come up with this. I'm not sure I didn't notice
someone else using a similar system years ago and steal it from them. But in any
case it's useful for me.

[^1]: In the same way that all communication systems eventually become email,
      all storage systems eventually become stirring.

[^2]: This assumes a negligible cost for locating an item within a container.
      For large containers with lots of small items, this may not be a valid
      assumption. In that case, it's back to stirring for you. But at least you
      know that you don't have to search the whole system, only one container.

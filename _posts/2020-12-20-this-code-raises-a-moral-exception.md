---
layout: post
title:  "This Code Raises a Moral Exception"
date:   2020-12-20 14:38:01 -0500
---

The other day I had an idea related to audio transcription. Approaches to this
problem fall into two general categories: human and automatic[^1]. The former
requires that a human listen to the input and type out their interpretation of
the speech they hear. In the latter, a computer does the same thing, usually
after training on thousands of hours of examples.

Most audio transcription companies price their services by the duration of
recorded audio. For humans, a common starting price I saw was \$1.25 per minute.
The automatic systems usually bill around \$0.0005 per second. And that makes
sense, because that's the only way you can give a reliable quote to a customer
before the transaction completes and the transcription begins. A better model
might be to charge by the number of transcribed words, but the problem is that
you can't know how many words there are in a transcription until you've
completed it. Duration is the only practical option, without introducing
uncertainty to the customer. But this model has a problem, too, and we can
exploit it. If we can somehow pack more speech into the same duration, then we
can transcribe at a lower cost.

Ordinary audio usually contains plenty of silence. Speaker changes, breaths, and
brief pauses for thought live between all the useful speech. For the purposes of
transcription, these silent moments carry zero information[^2]. The
transcription system doesn't mind if they're missing. So let's take them out:

```sox original.wav no-silence.wav silence -l 1 0.3 -55d -1 0.3 -55d```

People have a wide range of speaking rates. Typically we measure these in words
per minute, and most speakers fall between 100 and 200. Transcription systems
must handle all kinds of speakers, all with different rates. To pack more
information into a shorter duration, we can speed up the input audio a little.
The system again won't mind[^3].

```sox original.wav faster.wav tempo 1.25```

I tested these techniques on podcast audio, both separately and together. In
general I managed a 20 or 25% decrease in duration. 20% comes from the 25% tempo
increase, and another 2--8% comes from silence removal. Other spoken audio
domains may benefit more or less from silence removal, but tempo increase works
for all except the fastest samples.

A 25% reduction in duration means a 25% reduction in cost for the same audio.
The market application for this technique is clear. Who wouldn't want to pay
less for the same job? I got to work implementing this idea, and it didn't take
long either, thanks to `sox`. I chained the two filters above into one command,
and that was it. The rest would be simple cloud architecture surrounding this
core function, with a Web frontend on top. The performance was acceptable; in my
virtual machine, `sox` processed an hour of audio in about 100 seconds. I
couldn't think of a reason why it wouldn't work well and be a success.

I thought a bit about the pricing. I knew that automatic systems were about a
hundred times cheaper than humans. That makes sense, because automatic systems
are much faster, and they don't get tired. The quality is also lower. Human
systems usually guarantee 99% accuracy, while the state of the art for automatic
systems is around 95%. In the real world it's less, and automatic systems also
don't always add punctuation or do much formatting. Well, I thought, if I'm
going to bother then I might as well price it high. Better to take 25% of a
larger whole. That would put the price on the order of \$1.00 per hour. Most of
the savings would go to the client, who might have to spend \$20 or \$25 less
per hour under this approach. Even \$1.00 per hour would be thousands of times
more than the operating cost for this system, so I didn't mind too much.

## The Turn

It took another day or two before I found the problem. Something felt wrong. At
first I thought I was about to remember some system that already exists and
makes this idea moot. I have a habit of not finding those systems until after I
invest time and effort into an idea. But that wasn't it.

Oh, I know what the problem is. **This idea is immoral.**

I realized that I was proposing to extract value ("savings") from the
transcriptionists themselves. By packing more information into a shorter
duration, I was seeking to make their jobs harder for no extra compensation.
Many people who work transcription are poor and are trying to get by. Often the
companies they work with treat them as contractors, not employees, so they don't
get any benefits. I couldn't imagine building a business around exploiting these
people.

The math supports this view. At \$1.25 per recorded minute[4], the client pays
\$75 per hour. The industry standard is a four-to-one ratio of work time to
finished audio output. That makes \$18.75 per working hour. I don't know what
fraction companies take for themselves, but I suspect it's enough that the money
that a transcriptionist sees is less than a living wage in most places. And
again, that's before accounting for benefits. My system would take as much as
\$4.70 from that \$18.75.

For a time, I thought maybe there was some way I could make the system only work
with machines. Integrating and reselling machine transcriptions of "minified"
audio would work, except that it's against most Terms of Service agreements.
There also isn't any way I can see to make audio readable to machines but
unreadable to humans for this purpose. The nature of automatic transcription
systems is that they approximate humans. Any difference between the two is a
flaw in the machine, by definition. It's a nonstarter.

That's when the idea died. I was disappointed, but I was also proud. That was
the first time I can recall that one of my ideas has failed due to my own
morality. It wasn't technically infeasible or beyond my level of knowledge or
skill. It had a market and clear value to customers. But in the end it was its
failure to meet my moral criteria that killed it.

And if you're thinking of building this yourself to do what I didn't, then
here's another idea: don't.

[^1]: There are also hybrid approaches where a machine makes trained guesses. Then, it passes its low-confidence guesses to a human for correction.

[^2]: Speech as performance art relies on silence, and removing it would reduce the art's effect. But that's not what I'm talking about here.

[^3]: We can intuit that this will work by observing that some podcast listeners use players that speed up their shows, and some even remove silence. If casual listeners can understand the audio with these changes, then a transcription system should be able to as well.

[^4]: Some companies charge more or less, and many charge according to audio quality and other factors. This was the lowest price I saw on Rev, which seems to be among the largest in this space. Even a factor of two here doesn't make much difference.

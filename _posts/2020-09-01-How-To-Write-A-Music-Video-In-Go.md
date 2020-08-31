---
layout: post
title: How To Write A Music Video In Go
---

TL;DR: My band [ENAE](https://fb.com/enaehq) has released a first single, called [The System](https://www.youtube.com/watch?v=-_-2EpUqb9g). Song is 100% DIY (from writing to recording to mixing to producing to releasing) and the music video is written 100% in Go!

<a name="top">

Table of Contents:

* [Intro](#Intro)
* [Why would you program a music video?](#Why)
* [Where to start?](#Where)
* [Getting the sound](#Sound)
* [Getting the first characters to print](#Print)
* [Rhythm](#Rhythm)
* [Binary solo](#Binary)
* [Last scene](#Last)
* [Where to go from now?](#Where)

[^Top](#top)

<a name="Intro"/>
## Intro

I had always a soft spot for interesting movies. Of course, I like the good story as much as the next guy, but the visual aspect can make me fall in love with a movie. I won't even count how many times I've watched Guy Richie's Sherlock Holmes (both movies actually), What We Do In The Shadows, Blade Runner or anything by Quentin Tarantino. 

One thing that glues all of these movies together is that they do something really fresh from the very beginning - apart of the great story, they push the limit of visual aspect.

And I wanted to do the same with first [ENAE](https://fb.com/enaehq) single. I'm a DIY guy and the visual aspect of things isn't my strongest suit so, being a dev, what can I do?

Well, I can program the nerdiest music video you have seen so far.

[^Top](#top)

<a name="Why"/>
## Why would you program a music video?

Because I can.

No, really, this is the major thing why I've done it. I've thought that going nerdy and seeing how far I can push my knowledge about some stuff will be a fun project. Also I needed an excuse to brush off my sight reading and music transcription skills!

[^Top](#top)

<a name="Where"/>
## Where to start?

There's three parts to getting the song ready to be programmed:

* First, sheet notation of your song. Helps with the timings and you don't have to do the math yourself;
* Second, lyrics. Basically, what I've made is a more complicated lyrics video;
* Third, final mix version. As the timings might change, you don't want to redo the whole video because your mix has changed.

I've started mocking the video a long before we've finished the final mix, but I've started actual programming literally the day we've agreed on the mix.

[^Top](#top)

<a name="Sound"/>
## Getting the sound

Getting the sound was actually the easiest bit - I've used [Beep by Faiface](github.com/faiface/beep) in it's most raw form:

**player.go**
```go
package main

import (
  "log"
  "os"
  "time"

  "github.com/faiface/beep"
  "github.com/faiface/beep/mp3"
  "github.com/faiface/beep/speaker"
)

func play(done chan bool) {
  // Prepare the song to play
  f, err := os.Open("./bounce-test.mp3")
  if err != nil {
    log.Fatal(err)
  }

  streamer, format, err := mp3.Decode(f)
  if err != nil {
    log.Fatal(err)
  }
  defer streamer.Close()

  speaker.Init(format.SampleRate, format.SampleRate.N(time.Second/10))

  speaker.Play(beep.Seq(streamer, beep.Callback(func() {
    done <- true
  })))

  <-done
}
```

`done` is the channel I use to notify the main goroutine that play/print has finished.

[^Top](#top)

<a name="Print"/>
## Getting the first characters to print

[^Top](#top)

<a name="Rhythm"/>
## Rhythm

[^Top](#top)

<a name="Binary"/>
## Binary solo

[^Top](#top)

<a name="Last"/>
## Last scene

[^Top](#top)

<a name="Where"/>
## Where to go from now?

[^Top](#top)

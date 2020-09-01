---
layout: post
title: How To Write A Music Video In Go
---

TL;DR: My band <a href="https://fb.com/enaehq" target="_blank">ENAE</a> has released a first single, called <a href="https://www.youtube.com/watch?v=-_-2EpUqb9g" target="_blank">The System</a>. Song is 100% DIY (from writing to recording to mixing to producing to releasing) and the music video is written 100% in Go!

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

<a name="Intro"/>
## Intro

I had always a soft spot for interesting movies. Of course, I like the good story as much as the next guy, but the visual aspect can make me fall in love with a movie. I won't even count how many times I've watched Guy Richie's Sherlock Holmes (both movies actually), What We Do In The Shadows, Blade Runner or anything by Quentin Tarantino. 

One thing that glues all of these movies together is that they do something really fresh from the very beginning - apart of the great story, they push the limit of visual aspect.

And I wanted to do the same with first <a href="https://fb.com/enaehq" target="_blank">ENAE</a> single. I'm a DIY guy and the visual aspect of things isn't my strongest suit so, being a dev, what can I do?

Well, I can program the nerdiest music video you have seen so far.

<a name="Why"/>
## Why would you program a music video?

Because I can.

No, really, this is the major thing why I've done it. I've thought that going nerdy and seeing how far I can push my knowledge about some stuff will be a fun project. Also I needed an excuse to brush off my sight reading and music transcription skills!

Before we go any further, I recommend watching <a href="https://www.youtube.com/watch?v=-_-2EpUqb9g" target="_blank">The Video</a>. so we'll set the playing field - I'll refer to it a few times!

[Go to Top](#top)

<a name="Where"/>
## Where to start?

There's three parts to getting the song ready to be programmed:

* First, sheet notation of your song. Helps with the timings and you don't have to do the math yourself;
* Second, lyrics. Basically, what I've made is a more complicated lyrics video;
* Third, final mix version. As the timings might change, you don't want to redo the whole video because your mix has changed.

I've started mocking the video a long before we've finished the final mix, but I've started actual programming literally the day we've agreed on the mix.

[Go to Top](#top)

<a name="Sound"/>
## Getting the sound

Getting the sound was actually the easiest bit - I've used <a href="github.com/faiface/beep" target="_blank">Beep by Faiface</a> in it's most raw form:

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
  f, err := os.Open("./enae-the_system.mp3")
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

`done` is the channel I use to notify the main goroutine that play/print has finished. As it's a music video, high-quality mp3 file is sufficient enough to be lightweight and good sounding.

[Go to Top](#top)

<a name="Print"/>
## Getting the first characters to print

The main file, that deals with all the things to print is, unsurprisingly, `printer.go`. Let's break it down:

```go
var (
  // notes
  fullnote         int = 1678
  halfnote         int = 839
  quaternote       int = 420
  eightnote        int = 210
  sixteenthnote    int = 105
  thirtysecondnote int = 52
  sixtyfourthnote  int = 26
)
```

In order to be able to print stuff properly, you need to get a tempo of the song and the duration of each note in milliseconds. The formula to do that is really simple:

> 60000 / BPM = one beat in millisecond

BPM (Beats per minute) is the song tempo, one beat refers to a quater note. So, in case of *The System*, where the BPM is 143, it's:

> 60000 / 143 = 420 (rounded up)

This way, I've calculated all necessary note durations that I'll need to print.

Moving on:

```go
type Sized struct {
  width  int
  height int
}

func printer(done chan bool) {
  s := &Sized{}
  s.width, s.height = checkTerminalSize()
  // intro
  intro()
  // verse 1
  verse1()
  // chorus 1
  chorusBig1()
  chorusBig1()
  overAndOver()
  chorusBig1()
  // verse 2
  verse2()
  // chorus 2 - ends faster and starts loading bar
  chorusBig1()
  chorusBig1()
  overAndOver()
  chorusSmall1()
  // solo
  s.solo()
  // chorus 3 - can't feel, can see...
  s.lastChorus()
  <-done
}
```

We'll touch on `Sized` struct a while later; for now, you can see how the song was built.

I've divided the chorus into 4 small chunks (in case I want to change only a part of it). `verse1` and `verse2` are a tiny bit different in the approach - one is typewriting (printing one character per x milliseconds), second one is Star-Wars-esque "Text is going up" using frames.

Before we go into the rhythm part of the music video, there are three very important functions in `printer.go`:

```go
func cleanDisplay() {
  fmt.Printf("\x1b[2J")
  moveCursor(0, 0)
}

func moveCursor(row, col int) {
  fmt.Printf("\x1b[%d;%df", row+1, col+1)
}

func noteRest(note int) {
  time.Sleep(time.Millisecond * time.Duration(note))
}
```

Here we delve into the nitty-gritty of the whole system - [Ansi escape codes](https://en.wikipedia.org/wiki/ANSI_escape_code). In case of my music video, I've used a few:

in `printer.go`:

* `\x1b[2J` - all escape codes starts with `\x1b`, which is ASCII representation of ESC. `[2J` means to clear the whole display; 
* `\x1b[%d;%df` - `[%d;%df` moves the cursor to a given position

> We'll talk about noteRest in a second!

in `colours.go`:

(First, let's bring the functions here)

```go
func resetColourChanges() {
  fmt.Print("\x1b[0m")
}
```

as the function name says, it reset colour changes to default;

```go
func colourText(colour int, bright bool) {
  if bright {
    fmt.Printf("\x1b[9%vm", colour)
  } else {
    fmt.Printf("\x1b[3%vm", colour)
  }
}
```

`colourText` is a wrapper around printing from basic palette of 8 colours (black, red, green, yellow, blue, magenta, cyan and white) in bright (`[9`) variant or in normal - let's call it plain (`[3`);

```go
func colourTextRGB(red, green, blue int) {
  fmt.Printf("\x1b[38;2;%v;%v;%vm", red, green, blue)
}
```

This is the function I ended up using - I've found a way to print the colours using RGB values - the loading bar you see before the solo starts is done using this magical thing!

[Go to Top](#top)

<a name="Rhythm"/>
## Rhythm

Remember `noteRest`? It's nothing else than a wrapper around `time.Sleep`. To print time-based frames, I used `printInMicroseconds`: 

```go
func printInMicroseconds(s string, spd int) {
  speed := spd * 1000
  freq := (float64(speed) / float64(len(s)))
  for i := 0; i < len(s); i++ {
    fmt.Printf("%v", string(s[i]))
    time.Sleep(time.Microsecond * time.Duration(freq))
  }
}
```

Why *Microseconds* instead of *Milliseconds* you ask? The reason for that is sometimes the lyrics aren't really divisible by the millisecond duration of the note in a nice fashion. That's why, I'm converting milliseconds to microseconds and then printing. Check `verse1.go`:

```go
...
func verse1() {
  printInMicroseconds(verse1_1_1, halfnote+quaternote) // 3/1
  printInMicroseconds(verse1_1_2, quaternote)          // 1/1
...
}
```

Sometimes some part of the lyrics will have to stay longer than pre-defined note duration. Because notes in the code are `int`, we can add them and then the function takes care of it!

And now,
[Go to Top](#top)

<a name="Binary"/>
## Binary solo

This is the part, I think, I'm most proud of. The function:

```go
func (s *Sized) printNoteInBinary(note string, speed int) {
  var toBinary string
  for _, c := range note {
    toBinary += fmt.Sprintf("%b ", c)
  }
  freq := (float64(speed) / float64(len(toBinary))) // to microseconds
  for _, b := range toBinary {
    fmt.Printf("%c", b)
    time.Sleep(time.Millisecond * time.Duration(freq))
  }
  fmt.Println()
}
```

The function itself is nothing crazy, but the approach to printing is:

```go
...
// 1st bar
s.printNoteInBinary("D", fullnote)
s.printNoteInBinary("F", fullnote)
s.printNoteInBinary("G", halfnote)
s.printNoteInBinary("A", halfnote)
s.printNoteInBinary("C", fullnote)
cleanDisplay()
// 2nd bar
noteRest(eightnote)
s.printNoteInBinary("A", sixteenthnote)
s.printNoteInBinary("C", sixteenthnote)
s.printNoteInBinary("D", eightnote)
s.printNoteInBinary("G", quaternote)
...
```

Remember at the beginning, I've told that we'd need a sheet music? Without that, programming this solo would be extremely painful. Each note gets converted to a binary representation and then gets printed within the note duration.

> You might notice, that there's a pointer to `Sized` here - reason for that is, I've iterated over multiple versions of the solo and thought the one now is the best looking one. In case someone would like to print it anywhere else on the screen, they can, because they know the terminal size already!

[Go to Top](#top)

<a name="Last"/>
## Last scene

The last scene is an interesting one, because there's a very small chance that it'll look the same ever. 

Each time you run this music video in the terminal, it'll be different, because the "frame cleanup" is truly randomized. I've spent a while thinking about how to pull this off and went with this:

* I've prepared nice ASCII art of the last chorus
* Then I've formatted it
* Populated the display with `:`
* The randomly destroyed what I need

Sounds easy? Once I had figured it out, it actually was! So, let's take a closer look at the functions:

> All functions are in chorus_last.go

```go
func (s *Sized) formatLine(str string) string {
  add := int(math.RoundToEven((float64(s.width) - float64(len(str))) / 2))
  var pre, post string
  for i := 0; i < add; i++ {
    pre += ":"
    post += ":"
  }
  f := pre + str + post
  if len(f) != s.width {
    f = f[:len(f)-1]
  }

  return f
}
```

As each display will be different, the art needs to be formatted - which basically means adding `:` at the beginning and the end until the whole width of the display is used.

```go
func (s *Sized) generateLine(char string) string {
  var str string
  for i := 0; i < s.width; i++ {
    str += char
  }
  return str
}

func (s *Sized) generateDisplay(char string) {
  var strArr []string
  for i := 0; i < s.height; i++ {
    str := s.generateLine(char)
    strArr = append(strArr, str)
  }
  temp := strings.Join(strArr[:], "\n")
  printInMicroseconds(temp, eightnote)
}
```

Then, we need something to populate the whole display with `:` - which means generating some lines and then creating an array which will be of full terminal dimension.

And now, the last behemoth:

```go
func (s *Sized) lastChorus() {
  cleanDisplay()
  s.generateDisplay(":")
  maxHeight := (s.height - 32) / 2
  var (
    iter int
  )
  noteRest(eightnote)
  moveCursor(maxHeight, 0)
  for i := 0; i < len(banners); i++ {
    moveCursor(maxHeight+iter, 0)
    fmt.Println(s.formatLine(banners[i]))
    if i == (len(banners)-1)/2 {
      noteRest(eightnote)
      iter++
      continue
    }
    noteRest(thirtysecondnote)
    iter++
  }
  noteRest(fullnote)

  rest := 5
  maxMilliseconds := int(math.RoundToEven(float64(16*fullnote) / float64(rest)))

  source := rand.NewSource(time.Now().UnixNano())
  for j := 0; j < maxMilliseconds; j++ {
    randban := rand.New(source).Intn(len(banners))
    banner := banners[randban]
    switch randban {
    case 8, 9, 10, 11, 12, 13, 14, 15:
      // want to get "your"
      mid := len(banner) / 2
      randpos := rand.New(source).Intn(mid) + mid + 1
      changedBanner := banner[:randpos-1] + ":" + banner[randpos:]
      moveCursor(maxHeight+randban, 0)
      fmt.Println(s.formatLine(changedBanner))
      banners[randban] = changedBanner
    case 24, 25, 26, 27, 28, 29, 30, 31:
      // we're leaving the "face" out, thus middle of the banner
      mid := len(banner) / 2
      randpos := rand.New(source).Intn(mid) + 1
      changedBanner := banner[:randpos-1] + ":" + banner[randpos:]
      moveCursor(maxHeight+randban, 0)
      fmt.Println(s.formatLine(changedBanner))
      banners[randban] = changedBanner
    default:
      // we don't want 0 here
      randpos := rand.New(source).Intn(len(banner)-1) + 1
      changedBanner := banner[:randpos-1] + ":" + banner[randpos:]
      moveCursor(maxHeight+randban, 0)
      fmt.Println(s.formatLine(changedBanner))
      banners[randban] = changedBanner
  }
  noteRest(rest)
  }
}
```

So, there's the magic to the system - I've used the functions I've prepped before and created the whole display. Next, I need to calculate how fast I can randomize the lines change - basically, the code knows which lines to find and where to start the beautiful dance of destruction.

Each time it takes only one step and then rests for 5 milliseconds. This way, I'm sure it'll destroy a lot of stuff, but not all.

[Go to Top](#top)

<a name="Where"/>
## Where to go from now?

Glad to see you here, at the end of this note!

If I would do it again, what I would change:

* Cast-in-stone storyboard - I haven't had that as a lot of things here was just plain experimentation
* Even more modular code - what if I want to have Star-Wars-esque solo part?
* Less noise in the code - a lot of stuff I've left off because I wasn't sure if I will use them again. It's more of a wireframe for the next videos now.

Thank you for reading! Give us a <a href="https://www.youtube.com/watch?v=-_-2EpUqb9g" target="_blank">like</a> or <a href="https://www.youtube.com/channel/UCKjavmoQAboXOBpPEBp5UHw?sub_confirmation=1" target="_blank">subscribe</a> or come to <a href="https://fb.com/enaehq" target="_blank">facebook</a> or - even better - do all of these things!

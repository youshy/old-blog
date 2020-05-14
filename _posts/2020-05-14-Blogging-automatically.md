---
layout: post
title: Blogging automatically
---

As you probably can see, I'm trying to take this blog seriously, adding the long-awaited (by me) dark theme, adding the [media](https://akondas.com/media) tab, where you can find all non-coding stuff I've done recently and aiming to write more posts in general. But how do I write them in the first place?

## Enter NJU

[Nju](https://github.com/youshy/nju) is a CLI I wrote almost 8 months ago, when I've started this blog, to help me with automatic the *very tedious* process of creating new posts. 

This page is based on [Jekyll](https://jekyllrb.com/), which is a very nice - and GitHub powered - engine to host your own simple blogs. Written in Ruby, it allows to write blogs using your Markdown.md files and Git. There are a few things to keep in mind though:

* Posts must follow `year-month-day-title.md` format
* Posts must have [Front Matter](https://jekyllrb.com/docs/front-matter/) block as the first thing in the file
* Well, you have to manually create, write and commit your files

I can see more than one way to make my life simpler... Let's go!

## Why CLI?

Because command-line interfaces makes your life easier. And if you're nerd like me, and don't like leaving your cozy terminal, that's even better. I won't go over the whole package here, I'll just do the small highlights and general overview.

## Following format

First thing we need to do, is to get the correct file name for our blog to run:

```go
t := time.Now()
year := strconv.Itoa(t.Year())
monthInt := int(t.Month())
dayInt := t.Day()
var month, day string

if monthInt < 10 {
  month = "0" + strconv.Itoa(monthInt)
} else {
  month = strconv.Itoa(monthInt)
}

if dayInt < 10 {
  day = "0" + strconv.Itoa(dayInt)
} else {
  day = strconv.Itoa(dayInt)
}

fileNameString := strings.ReplaceAll(title, " ", "-") + ".md"
fileName := year + "-" + month + "-" + day + "-" + fileNameString
```

As you can see, we're getting the current time, extracting **year**, **month** and **day** and concatenating them with the post title we'll pass later in the CLI.

## Adding the block

That's even easier:

```go
header := "---\nlayout: post\ntitle: " + title + "\n---\n\n"
```

But, this is where the real fun begins.

## Wrap it all together!

(Tried to record my screen here and export it to GIF, might write a Go package to do that for me...)

My workflow is really easy: I type `nju post "<name>"` from whenever I am - I don't have to change any directory whatsoever, Nju works from EEEEVVEEERYYYYWWWHEEEEREEEE, then type what I want, save it and press `y` to upload it to my blog! Easy?

Not so much. Bear in mind, I'm an avid fan of Vim, so I tend to do almost everything in Vim. Therefore, when I type `nju post "<name>"`, this commands opens up Vim with pre-set header and ready for me to type. That happens because of these lines:

```go
cmd := exec.Command("vim", filepath.Join(blogDir, filepath.Base(fileName)))
cmd.Stdin = os.Stdin
cmd.Stdout = os.Stdout
cmd.Run()
```

I'm routing my **Stdin** and **Stdout** streams to Vim, that's why the header is already set!

Then, when I save and exit Vim, my terminal asks me `Is that post finished? Do you want to commit? (y/n)`. This very simple check lets me write posts throughout a few days or, if it's ready, to commit it automatically:

```go
fmt.Printf("Let's go with this post then!\n")
add := exec.Command("git", "add", fileName)
add.Dir = blogDir
add.Run()

commitName := "New Post: " + title
commit := exec.Command("git", "commit", "-m", commitName)
commit.Dir = blogDir
commit.Run()

push := exec.Command("git", "push")
push.Dir = blogDir
push.Run()
```

What this is - `git add <filename>`, `git commit -m "New Post: <title>"` and `git push` - but done with Go!

Probably this could be done nicer and cleaner but bear in mind, I haven't played with this app in a while now - it works perfectly for my needs!

## Licence to CLI

I've added my binary as an alias in my `.zshrc` config, so then I can use this app from whenever I am - I can quicly switch context when working if I want to write something without overthinking it:

```go
f, err := os.Create(filepath.Join(blogDir, filepath.Base(fileName)))
```

This line makes sure that my streams are in the right place. `blogDir` is a path to my blog repo on my Mac.

## Wrapping up

Literally every single post I've written here has been done using this CLI. Even the Hello World was my testing ground for this package. What I would (and probably will) change in the next few months:

* Show unfinished posts
* Small notes/gists
* Automatically update date with posts that are written over a few days
* Automatically post a link on Twitter and LinkedIn that I've posted a new thing

All in all, I'm more than happy with this package. You might think that I'm overthinking it and should just navigate to the directory and create everything as anyone else would but that's my game. I like doing 150% work once and then benefit from that over time. Not saying it's perfect, but it works for me! Maybe it'd work for you as well?

---

As you've probably noticed, I've finally bought a domain, so from now on you can find this blog by typing `akondas.com` into your browser (SUPER STOKED ABOUT THIS :D). Also I've added a [media](https://akondas.com/media/) tab as I've mentioned in the beginning!

More good things to come here! Will definitely start writing more about the packages I've written and, most probably, write more packages. There's so much to automate...

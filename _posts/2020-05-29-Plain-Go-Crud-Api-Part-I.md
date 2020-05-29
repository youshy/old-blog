---
layout: post
title: Plain-Go-Crud-Api-Part-I
---

First of all - thanks to all who has attended the playground! We're trying to find a format that will benefit us all in these new times, and it makes the work 100x better knowing, that what we've prepared works for you! If you want to watch the playground again, [check it on YouTube!](https://www.youtube.com/watch?v=JpznEfXcoe0)

The playground assumed, more or less, that you have a full control over your development environment and you can choose with what you want to work.
But what if you work in a company, that has a strict rules over which packages can be used to write your code? I have been in a situation like this a few times, but here I come to save you from this trouble!

This article will be the first in a part of articles about how to build a Go CRUD API with Postgres integration using LITERALLY one package from outside world - and that's way easier to pass through the management. Let's go!

This article is best consumed if you attended our last [DevOps Playground](https://www.meetup.com/DevOpsPlayground/events/270672982/) about making Ready-To-Deploy Golang CRUD Api. If you need a refresher, click [HERE](https://github.com/youshy/Hands-On-Ready-To-Deploy-Golang-CRUD-API) and then run both, side by side to see how the approach differs. Or wait till the last part where I'll go almost line-by-line and compare both approaches.

---

TL;DR: Full code available [HERE](https://github.com/youshy/plain-go-crud) - just clone the repo if you want to follow along or fork it and use in your company/for your own fun!

---

## Initial prep

I assume, that if you're here, you know what Go is and you have it installed on your machine. If not, then:

* Go (or known also as Golang) is a programming language created at Google by Rob Pike, Robert Griesemer and Ken Thompson. Fast, garbage collected, memory safe and super fun to use!
* Get Go [HERE](https://golang.org)
* Check and learn more about Go [HERE](https://tour.golang.org)

* This tutorial will require Postgres setup which won't be covered until the end of the series. Please check if you have Postgres installed locally or set up an Amazon RDS (or follow along in our journey!)

Once we all got this, let's create a folder in our work directory:

```
mkdir plain-go-crud && cd plain-go-crud
```

As we have Go installed, we can set up **Go Modules** in our directory. Go Modules is the way how Go manages it's dependencies:

```
go mod init plain-go-crud
```

You should notice, that this has created a `go.mod` file. We don't have to worry about our dependencies anymore! Also, in our case, this will be a rather empty file...

When I was learning programming, I really didn't like the tutorials which were jumping through the files without the initial scaffolding prior. So, we won't do that here. Let's create a few files:

```
touch main.go server.go broker.go
```

`main.go` will be our entry file, `server.go` as the name says, will be our server logic and `broker.go` will contain everything we need in order to use the database.

## Set Up Entry

Our goal is to create an API which we would be able to deploy immediately. For the Postgres connection, we will use environment keys. We need to check if the application has access to them and if they are set up in a way we need them. Let's write some code:

**main.go**
```go
package main

import (
  "log"
  "os"
)
```

The beginning of our file defines the package in which we are in - if we would build a modular app, or a mesh of micro-services, we could create different packages for different parts of our application logic. In our case, that would only ramp up the complexity of the application. **Go** compiler checks the `package` name when compiling. If it's `main` it knows that it has to generate the executable we'll be able to use.

`import` imports the packages necessary for this file to compile - in this case is the `log` package which provides an easy way to log to `stdout` and `os` which will allow us to check environment keys.

**main.go**
```go
func main() {
	a := App{}
	a.Initialize()
	a.Run(":9000")
}
```

`func main()` is our main function in the application - when Go compiles it's code, it looks for this function to allow the code to execute. Although we don't have the `App`, `Initialize` and `Run` logic yet, we can add it here. `App` will create an object, that will hold all the server logic, `Initialize` will set up everything we need for the app to run, and `Run` - well, runs the app on the port we provide!

**main.go**
```go
func init() {
	if ok := os.Getenv("PG_USERNAME"); ok == "" {
		log.Fatalln("PG_USERNAME not specified")
	}
	if ok := os.Getenv("PG_PASSWORD"); ok == "" {
		log.Fatalln("PG_PASSWORD not specified")
	}
	if ok := os.Getenv("PG_DB_NAME"); ok == "" {
		log.Fatalln("PG_DB_NAME not specified")
	}
	if ok := os.Getenv("PG_DB_HOST"); ok == "" {
		log.Fatalln("PG_DB_HOST_ not specified")
	}
}
```

`func init()` runs before the whole application does - before the app starts, in this few milliseconds before the code gets to `main()`, Go allows us to do things prior to that. In our case, we are checking the four keys we will need for Postgres to run in our app.

Let's take one bit and understand what is going on here:

**main.go**
```go
if ok := os.Getenv("PG_USERNAME"); ok == "" {
  log.Fatalln("PG_USERNAME not specified")
}
```

`if ok := ` initialized a variable `ok`; `os.Getenv` is the method that checks the variable - in this case `PG_USERNAME`. `ok == ""` checks if the variable is empty or not; we're doing that by comparing the `ok` to an empty string. If it's empty, we print it to the stdout and call `os.Exit(1)` - all neatly hidden in `log.Fatalln`.

This will be the last time we go into this file so save it and let's move on. You've just written your first Go code!

## What we've done so far?

Although that doesn't seem like much, we have:

* Created a Go Module that will take care of our dependencies;
* Wrote a skeleton for our server;
* Wrote checks for env keys we'll use to connect to the database.

## What's next?

In the next part we will deal with setting up the server and it's handlers. If you have any more questions, grab me @ [Twitter](twitter.com/arturkondas) and let's talk!


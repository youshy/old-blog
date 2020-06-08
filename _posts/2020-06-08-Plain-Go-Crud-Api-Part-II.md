---
layout: post
title: Plain Go Crud Api Part II
---

[Part I](https://akondas.com/Plain-Go-Crud-Api-Part-I/) | [Devops Playground Youtube](https://www.youtube.com/watch?v=JpznEfXcoe0)

## Server Logic

I won't lie to you, the code you will see here won't be entirely beautiful. That's the purpose of this tutorial - our [Playground](https://www.youtube.com/watch?v=JpznEfXcoe0) has covered how to build a Production-Grade API you can easily use in your daily work; whereas this one is more of a PoC you can use to show that Go is really nice.

The code architecture will stay mostly the same.

Let's cut straight to the chase and set up **server.go**:

**server.go**
```go
package main

import (
	"database/sql"
	"encoding/json"
	"fmt"
	"log"
	"net/http"
	"os"

	_ "github.com/lib/pq"
)
```

Same as in **main.go**, we do the initial prep and import the necessary packages. Last line is the most interesting one:

**server.go**
```go
_ "github.com/lib/pq"
```

This is *THE ONLY* external library we use in our code. `pg` allows us to use Postgres connection and `_` in front means that this is "empty import" - we need our code to understand that it's there, not actually to use it. I know, it's a bit odd but trust me - that's the only odd thing here!

**server.go**
```go
type App struct {
  Db *sql.DB
}
```

I'm not a fan of using global variables in my code. That's why I'm creating a **struct** that will hold all the necessary variables I will use. In our case, it's the pointer to `DB` type in `sql` package.

You are more than welcome to just do a global variable here and save some words in your code.

## Initialize server

Let's set up a function to initialize our server setup:

**server.go**
```go
func (a *App) Initialize() {
	PgUsername := os.Getenv("PG_USERNAME")
	PgPassword := os.Getenv("PG_PASSWORD")
	PgDbName := os.Getenv("PG_DB_NAME")
	PgDbHost := os.Getenv("PG_DB_HOST")

	connect := fmt.Sprintf("dbname=%s user=%s password=%s host=%s sslmode=disable", PgDbName, PgUsername, PgPassword, PgDbHost)
	psqlDb, err := sql.Open("postgres", connect)
  if err != nil {
    log.Fatal(err)
  }
  a.Db = psqlDb
```

`Initialize()` function will set up the db connection for us and fail if the connection fails. Next bit is to actually register our handlers.

Same as in our Playground, I'll set up handler routes with prefixes. In `Initialize()` add:

**server.go**
```go
  prefix := "/api"

  http.HandleFunc(prefix+"/posts", a.GetAllPosts)
  http.HandleFunc(prefix+"/post", a.Handler)
}
```

## Prepare to run

There's two more things we need to do before we'll move to handlers - we need to set up a function for running the server and the same, nice helper function for handling JSON response as in Playground code:

**server.go**
```go
func (a *App) Run(addr string) {
	log.Printf("Server is listening on %v", addr)
	log.Fatal(http.ListenAndServe(addr, nil))
}

func JSONResponse(w http.ResponseWriter, code int, output interface{}) {
	response, _ := json.Marshal(output)
	w.Header().Set("Content-Type", "application/json")
	w.WriteHeader(code)
	w.Write(response)
}
```

The main difference here is the length of `Run` function which now is rather simple and concise!

## What's next?

In the next - and last part of this tutorial series - post, we'll build the handlers and prepare our API for liftoff! If you want to check the full code - check [the GitHub repo here](https://github.com/youshy/plain-go-crud)!

There will be one extra post where we will compare the full blown API to this plain one, we'll talk about downsides of our approach here and, maybe, some advantages of it!

Also, this month we're cooking something really special for you on Devops Playground! Stay tuned!

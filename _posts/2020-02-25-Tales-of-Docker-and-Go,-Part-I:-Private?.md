---
layout: post
title: Tales of Docker and Go, Part I: Private?
---

Yes, yes, I know, this is not a proper blog if I don't post often. But hey, that's the name of the game, when you're working on fun stuff!

Talking about fun stuff - have you ever tried to dockerize your Go app? Probably it's easy, there's a ton of Medium posts on how to do that. But what to do when **one of your packages is behind the private organisation**?

## How to dockerize an app which uses private libraries?

Well, that was a thing, that took me a few days to figure out. I'm not a Docker master, and I'm more than 100% sure, that it can be done sexier than this. Ok, let's go!

```dockerfile
# Part I
FROM alpine/git AS prebuild
WORKDIR  /go/src/github.com/<package>
ARG GIT_USERNAME
ARG GIT_PASSWORD
RUN git clone https://${GIT_USERNAME}:${GIT_PASSWORD}@your.private.git/and/amazing/package

# Part II
FROM golang:1.13.8-alpine
RUN apk add git
COPY --from=prebuild /go/src/github.com /go/src/github.com
WORKDIR /app
COPY . .
RUN go get -d -v
RUN go build -o your-app .
EXPOSE 6661

CMD ["./your-app"]
```

So, as you probably can see, I'm not using *go modules*. Well, it's my private thing (had more problems than solved issues with them, so until they're not in better shape...) so I'm going with oldschool approach and doing `go get` myself. Let's dive into separate parts then!

## Part I - Get the private repo

```dockerfile
FROM alpine/git AS prebuild
WORKDIR  /go/src/github.com/ecs-digital/Smart-Explorer
ARG GIT_USERNAME
ARG GIT_PASSWORD
RUN git clone https://${GIT_USERNAME}:${GIT_PASSWORD}@github.ecs-digital.co.uk/ECSD/ExplorationEngine
WORKDIR /go/src/github.com/jinzhu
RUN git clone -b master https://github.com/jinzhu/gorm.git
```

First bit is rather easy - we're using the lightest container that allows us to use `git`.

Now we need to figure out how we can authorize our call to GitHub to get the repo. We could hardcode our `GIT_USERNAME` and `GIT_PASSWORD` but that's a proper security breach and we don't like it. So, we're passing the `username`-`password` combo as parameters during the build and by the power of multi-stage build, we're hiding all the keys in the first stage - the only visible thing when doing `docker history IMAGE_ID` would be the second part, not the bit when we authorize ourselves!

## Part II - Do the rest

```dockerfile
FROM golang:1.13.8-alpine
RUN apk add git
COPY --from=prebuild /go/src/github.com /go/src/github.com
WORKDIR /app
COPY . .
RUN go get -d -v
RUN go build -o your-app .
EXPOSE 6661

CMD ["./your-app"]
```

The rest is rather standard - get `git` in our container to allow `go get` to work, we're copying pre-stage build (look at the `prebuild` tag) and building our app! Simple as that!

## Is it worth it?

Well, yes. If you're working in an organisation that has it's own private repos, this might be your salvation (until `go modules` will work it out and allow us to authenticate...).

If you have any questions, or you maybe see something fixable here - get me on Twitter @arturkondas - I'm more than hapy to help and to learn new stuff!

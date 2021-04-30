---
layout: post
title: Tales of Docker and Go Part II
---

Previous parts: [Part I](https://akondas.com/blog/Tales-of-Docker-and-Go,-Part-I-Private)

## How to use Docker and Go Modules with private repos?

Since our last talk about using private repositories with Go Modules, I've been playing with setting up Docker images for modules-based apps. As you might remember, I wasn't a fan of Go Mod but that has changed slightly!

The approach below will be based on Bitbucket, but it should work out-of-the-box for any version control system.

```docker
FROM golang:1.14.2-alpine AS app

ENV GOPRIVATE=bitbucket.org
ENV GO111MODULE=on

ARG GIT_USERNAME
ARG GIT_PASSWORD

RUN apk add git
RUN apk add openssh
RUN apk add mercurial

RUN git config --global url."https://${GIT_USERNAME}:${GIT_PASSWORD}@bitbucket.org/".insteadOf "https://bitbucket.org/"
WORKDIR /app
COPY . .

RUN go get -d -v
RUN go build -o ./myawesomeapp .

EXPOSE 6161
CMD ["./myawesomeapp"]
```

### But, what has changed?

The major change is the approach to Dockerizing the application - previous one, without modules, was using multi-stage builds to fetch the initial private repos we needed. This one doesn't have to - but if you want to be extra safe with the username and password, it's still a very good idea to do so.

Bear in mind, `mercurial` is currently being phased out until [July 1st, 2020](https://bitbucket.org/blog/sunsetting-mercurial-support-in-bitbucket) - after that date, you can simplify that docker image.



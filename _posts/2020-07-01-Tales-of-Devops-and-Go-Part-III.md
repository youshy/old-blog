---
layout: post
title: Tales of Devops and Go Part III
---

Previous parts:
* [Part I](https://akondas.com/blog/Tales-of-Docker-and-Go,-Part-I-Private)
* [Part II](https://akondas.com/blog/Tales-of-Docker-and-Go-Part-II)

As you've probably figured out from the title, I've renamed my small series `Tales of Docker and Go` to `Tales of Devops and Go`. Why is that? As I'm branching out to setting up more stuff from ground up, I think that going with more **DevOps** route will be better than narrowing ourselves down to Docker only.

That said, let's go!

## How to set up your Go repos with CircleCI and Sonarcloud

Lately, I've been playing with a [SonarCloud](https://sonarcloud.io) - recommended by one of my friends, the service improves your quality of code by constantly checking the bugs, code duplicates, anything you might ever oversee while coding!

But what if your software (again) uses private Go modules? The error you'll see more than often will be:

```
ERROR: Repository not found.

fatal: Could not read from remote repository.
```

What to do now?

## Use the same tactic as with your docker images

Let's check the yaml now:

```yaml
version: 2.1

orbs:
  sonarcloud: sonarsource/sonarcloud@1.0.1

jobs:
  build:
    docker:
      - image: circleci/golang:1.13

    working_directory: /go/src/github.com/<ORGANISATION>/<REPO_NAME>
    steps:
      - checkout

      # specify any bash command here prefixed with `run: `
      - run:
          name: Set private repo
          command: go env GOPRIVATE=<Set the private repo>
      - run:
          name: Pass the env keys for repo setup
          command: git config --global url."https://${GIT_USERNAME}:${GIT_PASSWORD}@<repo path>".insteadOf "<repo path>"
      - run: go get -v -d ./...
      - run: go test -v ./... -coverprofile=coverage.out
      - store_test_results:
          path: coverage.out
      - store_artifacts:
          path: coverage.out
      - sonarcloud/scan

workflows:
  version: 2
  build:
    jobs:
      - build:
          context: SonarCloud
```

Using this `config.yml` file for your `.circleci` setup will do a few things:

* First, we're pointing to `sonarcloud` to finish our build with Sonarcloud in mind
* As this `config.yml` gets `circleci` branded docker image, we need to do some setup:
  * set up a `GOPRIVATE` env variable with the private repo
  * pass the `username` and `password` for the `git` config to authenticate `go get` (you can use oAuth token here!)
  * run our tests and print the coverage
* At the end, we point back to `SonarCloud` to get the result to it!

Seems like a bit of work, but with that setup, **SonarCloud** does a lot of work for us!

Bear in mind - `GIT_PASSWORD` and `GIT_USERNAME` (or the oAuth token) has to be saved as environmental variable in CircleCI!

## What's next?

In the next part we'll check the setup of SonarCloud to make it all work together!

And if you want to learn why Go is useful when you're doing DevOps, check out my blog post on [ECS website](https://ecs.co.uk/resources/why-devops-engineers-should-know-go/) - this is what we do daily. And we do it good! (Post also written by yours truly)

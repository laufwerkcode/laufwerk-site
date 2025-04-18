---
title: Running your GitHub Actions locally
date: 2025-04-18
toc: true
mermaid: true
categories: [""]
tags: ["devops", ""]
---

GitHub Actions are a great CI/CD tool to test, build and deploy code from your GitHub repo. Getting started with GitHub Actions or troubleshooting Actions can become quite time and resource consuming when you run the actions on GitHub. Luckily there is a way to test and even run you GitHub Actions locally with the help of Docker! This makes it easy, quick and a bit easier do write and test new Actions.

Before you get started. This post is based on macOS, but should work on any system that can run Docker, and it assumes you have some experience with GitHub Actions. Totally new to Actions? Checkout [GitHub's Quickstart getting started page](https://docs.github.com/en/actions/writing-workflows/quickstart).

# My use case

I use GitHub Actions to build custom firmware. This usally involes a lot of tweaking, building and red pipelines. It als takes a bit each run (~2-3 minutes) so a running a pipeline can cost a lot of time (5 runs can add up to 20 minutes including que time). 

# Getting ACT

Installing `act` on macOS is straight forward using `brew`:

```bash
$ brew install act
```

`act` will use `docker` to run you workflow file. For this we can specify the which socket it should use with the `--container-daemon-socket` option.

To find out the availible sockets on you machine, use:

```bash
$ docker context ls

NAME       DESCRIPTION                               DOCKER ENDPOINT                                       ERROR
colima *   colima                                    unix:///Users/USERNAME/.colima/default/docker.sock
default    Current DOCKER_HOST based configuration   unix:///var/run/docker.sock
```

Then, to use it use:

```bash
$ act \
--container-daemon-socket unix:///Users/USERNAME/.colima/default/docker.sock
```

## Setting the Docker Daemon for ACT via ENV

You can also set the default daemon socket via:

```bash
$ export DOCKER_HOST=$HOME/.colima/default/docker.socket
```

# Quickstart

All of the `workflows` and supporting files from this blog can be found at:

Get started by clonding the [act-example](https://github.com/laufwerkcode/act-example) repo

```bash
$ git clone git@github.com:laufwerkcode/act-example.git
```

# Setting up a simple workflow

If you did't clone the repository (or it's gone in the future) follow these steps:

Let's init a new `git` repo and add this verry simple `.github/workflows/hello.yaml` workflow file under `.git`:

1. `mkdir act_demo && cd act_demo`
2. `git init`
3. `mkdir -p .github/workflows && touch .github/workflows/hello.yml`
4. Put the code below in the `.github/workflows/hello.yml` file

```yaml
---
name: hello

on:
  push:
    branches:
      - main
  workflow_dispatch:  # Allows manual trigger from GitHub UI

jobs:
  show-hello:
    runs-on: ubuntu-latest
    
    steps:
    - name: Just say hallo!
      run: |
        echo "Hello"
```

Now let's see our action with:

```bash
$ act --list

Stage  Job ID         Job name       Workflow name       Workflow file      Events
0      save-datetime  save-datetime  Save DateTime       artifiact.yml
0      show-env       show-env       env                 env.yml
0      build          build          Dependency Example  graph-example.yml
0      show-hello     show-hello     hello               hello.yml          push,workflow_dispatch
0      show-secret    show-secret    secret              secrets.yml
1      test           test           Dependency Example  graph-example.yml
1      lint           lint           Dependency Example  graph-example.yml
2      deploy         deploy         Dependency Example  graph-example.yml
```

And let's run it:

```bash
$ act --workflows .github/workflows/hello.yml --job show-hello

...
[hello/echo]   🐳  docker exec cmd=[bash -e /var/run/act/workflow/1] user= workdir=
| Hello
[hello/echo]   ✅  Success - Main Echo hello and optional variable [62.54675ms]
...
[hello/echo]   ✅  Success - Complete job
[hello/echo] 🏁  Job succeeded
```

The verry simple `| Hello` is our output.

## A note on workflow selection

As you can see in this first example, we use the `--workflows` and `--jobs` to select specifiek workflows and jobs from them. We do this because if we just run `act` it will run every `workflow` that is triggered with `on: push`

# Using secrets, vars en other stuff

Now we can get to testing the local workflows with some variables, secrets and let's have a look at saving some output.

## Using Enviroment Variables

Let's start simple and have a look at using and passing `ENV` vars to our workflow. We can use ENV's in our `act` command or load them in via a file

We will use the following workflow to test this:

```yaml
---
name: env

jobs:
  show-env:
    runs-on: ubuntu-latest
    steps:
      - name: Show the ENV var
        run: echo "The ENV var is ${{ env.envi }}"
```

### ENV using the cli

We can now run our workflow with:

```bash
$ act --workflows .github/workflows/env.yml --job show --env envi=something
...
[env/show] ⭐ Run Main Show the ENV var
...
[env/show]   🐳  docker exec cmd=[bash -e /var/run/act/workflow/0] user= workdir=
| The ENV var is something
```
### ENV using file

```bash
$ act --workflows .github/workflows/env.yml --job show --env-file .env
```

## Using Secrets

> [!danger]
> This is a great example of the insecurity and danger of using secrets in GitHub. You can (if you have access to the workflow pipeline, aka you can update the workflow) echo the content of secrets 

Secrets follow the same pattern but using `--secrets` or  `--secrets-file`

We can use the following workflow to test this:

```yaml
---
name: secret

jobs:
  show-secret:
    runs-on: ubuntu-latest
    steps:
      - name: Print Secret (Base64 encoded for safety)
        run: |
          echo "${{ secrets.secreti }}" | base64
```

### Secrets using cli

```bash
$ act --workflows .github/workflows/secrets.yml --job show-secret --secret secreti=007
```

### Secrets using file

```bash
$ act --workflows .github/workflows/secrets.yml --job show-secret --secret-file .secret
```

# Exporting data

Somethimes your workflow results in a file. When I build a keyboard firmware I end up with a `firmware.zip`.

In this example we will save the current `datetime` to a file and save that file locally.

```yaml
---
name: Save DateTime

on:

jobs:
  save-datetime:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout (required for act's artifact support)
        uses: actions/checkout@v4

      - name: Get current datetime and save to file
        run: |
          ISO_TIME=$(date -Iseconds)
          echo $ISO_TIME > datetime.txt
          cat datetime.txt

      - name: Upload datetime.txt as artifact
        uses: actions/upload-artifact@v4
        with:
          name: datetime-artifact
          path: datetime.txt
```

We will run this with the following command:

```bash
$ act --workflows .github/workflows/artifiact.yml --job save-datetime --artifact-server-path ./output
```

This will save the output of our workflow like this:

```bash
$ tree
.
└── output
    └── 1
        └── datetime-artifact
            └── datetime-artifact.zip
```

# Some other cool act's


`act` has got some more moves worth mentioning

## Triggering workflows with events

Most Workflows in GitHub are triggered by an "event" (such as a `push`) to the repository. Our `hello` workflow for example is triggered by a `push` to `main`. We can emulate this behauvior with `act` like so:

```bash
$ act push
```

## Creating overviews

You can create overviews on how different jobs relate to each other with the `--graph` feature:

```bash
$ act --graph
```

To use this on a specifiek workflow use:

```bash
$ act --graph --workflows .github/workflows/graph-example.yml
...
     ╭───────╮
     │ build │
     ╰───────╯
        ⬇
 ╭──────╮ ╭──────╮
 │ test │ │ lint │
 ╰──────╯ ╰──────╯
        ⬇
    ╭────────╮
    │ deploy │
    ╰────────╯

```

That's all! `act` responsible!

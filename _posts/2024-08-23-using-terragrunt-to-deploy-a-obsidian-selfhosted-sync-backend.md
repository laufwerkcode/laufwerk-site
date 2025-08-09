---
title: Using terragrunt to create a selfhosted Obsidian Sync backend
date: 2024-08-23
toc: true
mermaid: true
categories: ["Infrastructure as Code"]
tags: ["devops"]
---

I have been playing around with Self hosting my [Obsidian](https://obsidian.md/) vaults. I pay for [Obsidian sync](https://obsidian.md/sync) for my main vault right now, but I started a new job, and I'm looking into working with multiple vaults to keep things tidy. Now, Obsidian sync is a great working solution to sync your data, but there are other options out there, like [Selfhosted obsidian-livesync](https://github.com/vrtmrz/obsidian-livesync) (the one we are looking at here), [Remote Save](https://github.com/remotely-save/remotely-save) and syncing your vault via [SyncThing](https://syncthing.net/).

I opted to try out obsidian-livesync via a self hosted database because I also want my vault to be available on iOS devices.

# Setting up the stuff

obsidian-livesync can use different backends for syncing (some of which are in beta at the time of writing this) but I chose to go for the CoucheDB route. I decided I wanted to run it in Docker, and because I wanted to test it out first on my machine and then in the home lab I wrote it up in Terraform and Terragrunt. That's a bit overkill for a simple container, but it's the tool set I use.

All the setup is also in the [README](https://github.com/laufwerkcode/obsidian-selfhosted-sync/blob/main/README.md) of this [repo](https://github.com/laufwerkcode/obsidian-selfhosted-sync)

# Setting up the container

Credits to [Alex Jegtnes and his blogpost about it](https://jegtnes.com/blog/self-hosting-obsidian-sync-for-ios-android-mac-and-windows-with-couchdb/), this setup is based on his blogpost. This is basically just a "Took that how-to and put it into Infra as Code" blogpost. I also choose to leave out the reverse proxy because I have other ways of securely exposing the container to my machines in the homelab.

We will first need a container with a custom config for our CoucheDB deployment. Clone the repo and let's get started.

```bash
$ git clone git@github.com:KingOfSpades/coucheDBviaTerragrunt.git
```

We built this container from our repo using:

```bash
$ cd coucheDBviaTerragrunt
$ docker build ./CoucheDBContainer -t custom_obsidian_sync:v1.0
```

Let's inspect if we did this correctly:

```bash
$ docker history custom_obsidian_sync:v1.0
IMAGE          CREATED              CREATED BY                                      SIZE      COMMENT
47074646b3f3   About a minute ago   EXPOSE map[5984/tcp:{}]                         0B        buildkit.dockerfile.v0
<missing>      About a minute ago   COPY local.ini /opt/couchdb/etc/local.ini # …   515B      buildkit.dockerfile.v0
<missing>      8 months ago         CMD ["/opt/couchdb/bin/couchdb"]                0B        buildkit.dockerfile.v0
<missing>      8 months ago         EXPOSE map[4369/tcp:{} 5984/tcp:{} 9100/tcp:…   0B        buildkit.dockerfile.v0
<missing>      8 months ago         VOLUME [/opt/couchdb/data]                      0B        buildkit.dockerfile.v0
...
```

# The Terraform

Basically just a module with 2 inputs and two outputs. You supply it with a `couchdb_user` and a `couchdb_password`. It will output the URL you can visit the installation on.

**Tip**: Don't forget to check the correct socket for your Docker runtime in `sync-module/prod/main.tf:12`. You can find this by running `docker context ls`
{:.info}

# The Terragrunt

Now with terragrunt we apply our module a little bit easier. We could even add the variables to the `terragrunt.hcl` file for easier deployment, but let's not add our credentials to our code hm'k?

Deploy your code with

```bash
$ terragrunt apply
```

Supply a **PASSWORD** and **USERNAME** on the CLI and you should be ok to go,

After deployment, you should be able to access your CoucheDB instance on: [http://localhost:5984/_utils/](http://localhost:5984/_utils/)

# Setting up sync

Setting up sync can be done by following the instructions provided by [obsidian-livesync](https://github.com/vrtmrz/obsidian-livesync)

# Closing thoughts

With `s3` as an end-point being in beta right now, I might also give that a try. I had mixed results in the last 4 weeks I have been testing the selfhosted obsidian-livesync and I'm a bit weary about putting my trust in it. I might just pay extra to sync another vault or go mono vault again.

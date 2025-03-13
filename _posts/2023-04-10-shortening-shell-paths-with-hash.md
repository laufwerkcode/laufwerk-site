---
title:  "Shortening shell paths with hash"
date: 2023-04-10
toc: true
mermaid: true
categories: [Commandline]
tags: [cli, macos, linux, iterm2] # TAG names should always be lowercase
---

If you're like most developers, DevOps engineers, or IT professionals, you probably spend a lot of time in your [shell of choice](https://xkcd.com/686/). And if you're like the typical user, you're probably always deep in the filesystem. This can get confusing quickly, and there's only so much space available to display your working path (unless you invest in an ultrawide monitor, but even those have limits).

So in this post we will be looking at `hash` to shorten some of those paths's.

# So what's this all about

In this example, we will use the path `/home/user/baseDir` as an example with 3 subdir's setup. Let's assume we have this setup:

```bash
baseDir
  ∟ Work
  ∟ Personal
  ∟ Downloads
```

Now, if you're in any of these folders, your shell path will look like this:

```bash
$ /home/user/baseDir/Work
```

That's pretty readable, but if you're working in a project, and then a directory, and the subfolder of that directory things might quickly look like this:

```bash
$ /home/user/baseDir/Work/BigImportantProject/FolderWithPictures/PicturesOfParty
```

Now that's a long dir.

We will use `hash` to set up more readable paths for these three directories. Then, I will demonstrate some additional options this enables.

## What is hash used for?

Well!

> The /usr/bin/hash utility affects the way the current shell environment remembers the locations of utilities found. Depending on the arguments specified, it adds utility locations to its list of remembered locations or it purges the contents of the list. When no arguments are specified, it reports on the contents of the list. The `-r` option causes the shell to forget all remembered locations.

> Utilities provided as built-ins to the shell are not reported by hash.

-- StackExchange2023 [^Stack]

[^Stack]: https://unix.stackexchange.com/questions/86012/what-is-the-purpose-of-the-hash-command

Thanks [StackExchange](https://unix.stackexchange.com/questions/86012/what-is-the-purpose-of-the-hash-command)!

# Shortening paths

`hash` can be used to shorten path's with the following command:

```bash
hash -d ALIAS="/path/to/folder"
```

For example, we will shorten the path `/home/user/baseDir/Work` to `Work` like this:

```bash
hash -d Work="/home/user/baseDir/Work"
```

This will turn your shell path from `/home/user/baseDir/Work` to `~Work` when you are in that directory.
That's really great. You can now easily see that you're in your "Work" folder, giving you a better understanding of your system's structure.

![](/assets/images/hash/screenshot-ll2B6SA8.gif){:style="display:block; margin-left:auto; margin-right:auto";}
_Example of shortening shell path when entering directory_

But wait! Theres more!

## Permanence

Aliases are only valid for the current session, so if you want to make them permanent you can add  them to your `.bashrc` or `.zshrc`:

```bash
# /home/user/.zshrc
....
hash -d Work="/home/user/baseDir/Work"  
hash -d Personal="/home/user/baseDir/Personal"
hash -d Downloads="/home/user/baseDir/Downloads"
```

## Bonus one: Navigation

Set up aliases to easily navigate to them. For example:

```bash
$ cd ~Work
```

![](/assets/images/hash/screenshot-dk2k2KnR.gif){:style="display:block; margin-left:auto; margin-right:auto";}
_Moving to hashed dir from anywhere by using ~ALIAS_

You can also use tab completion to easily navigate to sub folders.

## Bonus two: Easy copy

ANother thing `hash` aliasses enable is easy copying of files. For example, you can copy a file from any place to your Work directory with:

```bash
$ cp FILE ~Work
```

This makes it easy to copy files from `~Downloads` to `~Work`. You could for example move a downloaded pdf easliy like:

```bash
$ cp ~Downloads/TheExpanseRapport.pdf ~Work
```

And this works from anywhere on your system.

![](/assets/images/hash/screenshot-qGJRnAtH.gif){:style="display:block; margin-left:auto; margin-right:auto";}
_Example of moving a file to a folder that is hashed_

# Conclusion

We can use `hash` to make our terminal usage more clear and organized. If you are like me and work with a pretty rigged folder structure like PARA [^PARA] then you know how nice it is to be able to jump to Projects, Areas and Resources quickly.

[^PARA]: https://www.buildingasecondbrain.com/para
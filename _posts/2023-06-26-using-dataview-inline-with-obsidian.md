---
title: "Using inline Datview with Obsidian"
date: 2023-06-26
toc: true
mermaid: true
categories: [Obsidian]
tags: [macos, ] # TAG names should always be lowercase
---

I have been using [Obsidian](https://obsidian.md) for close to three (3) years now. Like many other users I was drawn to Obsidian for its simple core (everything is a Markdown file [^Markdown]), it's extreme extensibility and it's extreme speed. I use obsidian to store information, concept ideas and even write this blog post you're reading

[^Markdown]: https://daringfireball.net/projects/markdown/

![](/assets/images/20230626162254.png){:style="display:block; margin-left:auto; margin-right:auto";}
_Writing about Obsidian using Obsidian_

In this post I'm sharing some in-line examples of one of the most popular Obsidian plugin's called [Dataview](https://github.com/blacksmithgu/obsidian-dataview). Dataview allow you to Treat your Obsidian Vault as a database which you can query from. Provides a JavaScript API and pipeline-based query language for filtering, sorting, and extracting data from Markdown pages.

A popular use for Dataview is to generate tables of pages. You can see a lot of examples of this on the [Dataview overview page](https://blacksmithgu.github.io/obsidian-dataview/). In this blog, we will be looking at inline queries. By this, I mean query's that go in-between text to render counts or other things. I had great difficulty finding examples of this at first, and when I got it working I vowed to share some examples for anyone out there. But first! The why

# Why in-line queries

I use in-line queries to get count's of pages or links. I use this to show me how many Items I have left in my inbox folder, to count the number of files I have containing the tag `#meeting` and to count how many books I read.

# Setting up

The things you will need to do are:

1. Install Obsidian
2. Install the Dataview plugin
3. Enable Inline Queries in the Dataview settings

To enable inline Queries go to the settings for Dataview. Here, you can enable Inline Queries. My queries didn't work for half an hour before I found this option, so I'm hoping to spare anyone this issue:

![](/assets/images/screenshot-7vhmB4Fo.png){:style="display:block; margin-left:auto; margin-right:auto";}
_Inline Queries settings in the Obsidian Dataview plugin_

# Queries

Now to the good stuff. Here are the queries I use:

## Count all files in a folder

This is a great one if you want to know how many files are in a folder regardless of any other metadata. It also doesn't require any custom Metadata [^Metadata]. We do this with:

[^Metadata]: https://help.obsidian.md/Editing+and+formatting/Metadata

```js
dv.pages('"0 INBOX"').length`
```

In you markdown this could look like:

```js
You have `$= dv.pages('"0 INBOX"').length` in your "0 INBOX" folder
```

Which will render: 

```markdown
You have 10 in your "0 INBOX" folder
```

## Count all files with links to

This works on files that link to another file in Obsidian. In this case, we will be using the link to `[[blog]]` for an example. To get a count of all files that link to the `[[blog]]` we can use:

```js
dv.pages("[[blog]]").length
```

Again, to use it in markdown:

```js
`$= dv.pages("[[blog]]").length`
```

## Count all files with tag

Same for tags:

```js
dv.pages("#blogpost).length
```

```js
`$= dv.pages("#blogpost).length`
```

# Combining queries

Now it doesn't stop there. You can easily combine and filter queries. Here are some examples:

## Count pages with link and tag

This gets you all pages linking to `[[blog]]` with the tag `#blogpost`

```js
`$= dv.pages("[[blog]] and #blogpost")length`
```

## Filtering on metadata

You can also add filters on top. For example, let's get all pages linking to `[[blog]]` with the tag `#blogpost` put that also have a custom metadata fields called `status`. Metdata is outside the scope of this post, but you can [read about it here](https://help.obsidian.md/Editing+and+formatting/Metadata). In example, we will use it to indicate the state of the article:

Let's get a count of all `draft`'s:

```js
`$= dv.pages("[[blog]] and #blogpost").where(p => p.status == "draft").length`
```

And get all our published blog's:

```js
`$= dv.pages("[[blog]] and #blogpost").where(p => p.status == "published").length`
```

# SUM

Inline queries can be used to insert dynamic data in to your Obsidian notes. It's works great in cases where you want to get a count of things in your vault. You could use this to count the books you read, the number of journal entries written or the amount of notes you made regarding a topic.
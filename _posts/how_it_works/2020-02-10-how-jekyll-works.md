---
layout: post
title: 'How Jekyll Works'
summary: "Looking into Jekyll's Source Code"
category: 'hiw'
---

Diving into the source code for any sufficiently complicated library can be a daunting task.

Let's start by digging into commands that we are familiar with - starting with `build`

To find where commands are defined we look to the `exe` folder

We see [Mercenary](https://github.com/jekyll/mercenary) - this is the library that is being used to create a CLI for Jekyll.

We see that commands are being loaded from the `Jekyll::Commands` module - let's look there.

We can find that in `lib/jekyll/commands/` 

In there we see `build.rb`

In there we see a call to `process_site` which then calls `process` in `lib/jekyll/site.rb`

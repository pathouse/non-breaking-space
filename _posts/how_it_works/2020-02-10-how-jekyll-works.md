---
layout: post
title: 'How Jekyll Works'
summary: "Looking into Jekyll's Source Code"
category: 'hiw'
---

Shortly after starting to build this site using Jekyll and beginning to write a post about how this site 
is built using it, I found myself curious about how it actually worked.

I understand the basics of templating languages and static site generators, but I didn't actually know all 
of the things that were happening when I ran `jekyll server` or `jekyll build` so I decided to dig into it,
and in doing so decided to start yet another series of posts on this site that I am calling "How it works".

Often enough during my day to day as a programmer I find myself using libraries where the documentation isn't
quite enough to answer the questions I have about it so I have become accustomed to cracking open the source code and 
digging in to the nuts and bolts of whatever it is that I am working with. This series is going to be a window into 
how I go about doing that. 

My hope is that anyone who reads this will not only come away with a better understanding of how some particular thing works but also
a better understanding of how one might approach the task of trying to read through the source code of a library that 
someone else has written.

Diving into the source code for any sufficiently complicated library can be a daunting task, and for me the key is always finding
some entry point - some place from which to start trying to understand all of the things that it does and how it is put together.

In this case I think a good entry point might be understanding the `build` command since it seems fairly straightforward. I know
that it uses all of the various config, markdown, html, and asset files in order to output HTML and CSS to a directory. I also 
know that another command I use - `jekyll server` - also uses this same functionality, in addition to several other things like
running a local web server and watching for file changes to automatically rebuild the site as I work on it.

The first step for digging into the source code is to open it up in our text editor. We can find the location of the source code
for the particular version that we are using via the `bundle show` command. 

```bash
emacsclient -t $(bundle show jekyll)
```

To find where commands are defined we can look into the `exe` (executable) folder.

There we see a single file `jekyll` so let's open that up.

There's a lot going on but I notice on line 15:

```ruby
Mercenary.program(:jekyll) do |p|
...
end
```

We see [Mercenary](https://github.com/jekyll/mercenary) - this is the library that is being used to create a CLI (command line interface)
for Jekyll.
I've never used this particular library before, but I have built CLI's with [Thor](https://github.com/erikhuda/thor) and this looks very similar.

Down on line 41 we see:

```ruby
Jekyll::Command.subclasses.each { |c| c.init_with_program(p) }
```

So it looks like individual commands within the `jekyll` program - e.g. `jekyll server` and `jekyll build` are being
defined by classes in the `Jekyll::Command` module - so let's go there next. We can find that in `lib/jekyll/commands/` 

In there we see `build.rb` so let's open that up. 

Right away we see this method `init_with_program` that we saw being called earlier:

```ruby
def init_with_program(prog)
  prog.command(:build) do |c|
    c.syntax      "build [options]"
    c.description "Build your site"
    c.alias :b

    add_build_options(c)

    c.action do |_, options|
      options["serving"] = false
      Jekyll::Commands::Build.process(options)
    end
  end
end
```

So this method is defining the command `build` within the program `jekyll` giving us `jekyll build` at the command line.
Most of this method involves defining various parts of this command for the CLI. The interesting part for us is in this `action` block.
There we can see a call to `Jekyll::Commands::Build.process`

This method is defined down on line 25.

```ruby
# Build your jekyll site
# Continuously watch if `watch` is set to true in the config.
def process(options)
  # Adjust verbosity quickly
  Jekyll.logger.adjust_verbosity(options)

  options = configuration_from_options(options)
  site = Jekyll::Site.new(options)

  if options.fetch("skip_initial_build", false)
    Jekyll.logger.warn "Build Warning:", "Skipping the initial build." \
                " This may result in an out-of-date site."
  else
    build(site, options)
  end

  if options.fetch("detach", false)
    Jekyll.logger.info "Auto-regeneration:",
      "disabled when running server detached."
  elsif options.fetch("watch", false)
    watch(site, options)
  else
    Jekyll.logger.info "Auto-regeneration:", "disabled. Use --watch to enable."
  end
end
```

In here we see a couple different things that could happen depending on what options are passed in, but the interesting
work seems to be happening on the line with the call to `build(site, options)` so let's take a look at that method
which is defined below this one.

```ruby
# Build your Jekyll site.
#
# site - the Jekyll::Site instance to build
# options - A Hash of options passed to the command
#
# Returns nothing.
def build(site, options)
  t = Time.now
  source      = options["source"]
  destination = options["destination"]
  incremental = options["incremental"]
  Jekyll.logger.info "Source:", source
  Jekyll.logger.info "Destination:", destination
  Jekyll.logger.info "Incremental build:",
    (incremental ? "enabled" : "disabled. Enable with --incremental")
  Jekyll.logger.info "Generating..."
  process_site(site)
  Jekyll.logger.info "", "done in #{(Time.now - t).round(3)} seconds."
end
```

Again we see a bunch of lines for loggin and option handling until we get to the part that interests us currently which is the line with 
`process_site(site)`

This method isn't defined in here, but we can use [`ag`](https://github.com/ggreer/the_silver_searcher) to search this directory
for it's definition by looking for `def process_site`

And with that we find it in `lib/jekyll/command.rb` so let's go there next.

```ruby
# Run Site#process and catch errors
#
# site - the Jekyll::Site object
#
# Returns nothing
def process_site(site)
  site.process
rescue Jekyll::Errors::FatalException => e
  Jekyll.logger.error "ERROR:", "YOUR SITE COULD NOT BE BUILT:"
  Jekyll.logger.error "", "------------------------------------"
  Jekyll.logger.error "", e.message
  exit(1)
end
```

So here we can see that this simply calls out to `site.process` and does some error handling.
We know from earlier documentation and code that `site` here is an instance of the class `Jekyll::Site` so we'll head there next.

```ruby
# Public: Read, process, and write this Site to output.
#
# Returns nothing.
def process
  reset
  read
  generate
  render
  cleanup
  write
  print_stats if config["profile"]
end
```

And now we are getting somewhere. This is starting to look like what I was expecting to find.





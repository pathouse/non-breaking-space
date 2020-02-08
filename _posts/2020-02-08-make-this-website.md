---
layout: post
title: 'Make this website'
image: ''
comments: false
categories:
summary: 'How this website is made'
---

# What Makes a Website

HTML + CSS

# Why Use a Static Site Generator?

Remove a lot of the tedium of doing the same thing over an over by hand.

# What Static Site Generator are We Using?

[Jekyll](https://jekyllrb.com/)

This site is going to talk a lot about the [Ruby](https://www.ruby-lang.org/en/) programming language
so it is only fitting that our static site generator is written in Ruby.

# Walkthrough

1. Follow the installation instructions on the Jekyll site.
1. Initialize [git](https://git-scm.com/book/en/v2/Getting-Started-About-Version-Control) repository
```
git init
git add -A
git commit -am 'Start new jekyll site'
```
1. Update the _config.yml file with info about the site.
  + title, email, description, url, github_username
  + remove twitter_username
  + remove [minima theme](https://github.com/jekyll/minima)
  + add [jekyll-seo-tag](https://github.com/jekyll/jekyll-seo-tag) to plugins
1. Copy all of the assets from the starter theme plugin (minima) into our project.
See [Overriding theme defaults](https://jekyllrb.com/docs/themes/#overriding-theme-defaults)
In the terminal:
```
cp -r $(bundle show minima)/_layouts
cp -r $(bundle show minima)/_includes
cp -r $(bundle show minima)/_sass
cp -r $(bundle show minima)/assets
```
1. Start updating theme defaults
  1. I'm not going to use Google Analytics
    + remove lines 8-10 in `_includes/head.html`
    + `rm _includes/google-analytics.html`
  1. I'm not going to use Disqus Comments
    + Remove lines 33-35 in `_layouts/post.html`
    + `rm _includes/disqus_comments.html`
  1. The only Social features I am going to use are Github and Youtube
    + `rm _includes/icon-twitter.html _includes/icon-twitter.svg`
    + Remove all of the lines that aren't about Github, Youtue, or RSS in `_includes/social.html`
    + Ditto for all svg icons in `assets/minima-social-icons.svg`
1. Make sure none of our changes broke anything by running `bundle exec jekyll serve` and visiting the site on `localhost:4000`
1. Commit changes to git
```
git add -A
git commit -am 'Copy and customize Minima theme'
```

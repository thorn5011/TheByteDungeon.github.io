---
layout: post
title: "Understanding GitHub Pages and Jekyll"
date: 2024-08-24
categories: tech
---


GitHub Pages allows you to host websites directly from your GitHub repository. Simple as pie? :heart: :computer:

## Jekyll themes?

Let's explore the *supported* [themes](https://pages.github.com/themes/).

Some favorites:
- [`hacker`](https://pages-themes.github.io/hacker/) theme seems good for obvious reasons :star:
- [`midnight`](https://pages-themes.github.io/midnight/) is a contender
- [`minima`](https://jekyll.github.io/minima/) with some nice *themes*
- [`cayman`](https://pages-themes.github.io/cayman/) seems smooth

# Configuration[](../_layout/blog_post.html)

We just update the `theme` in the [_config.yml](../_config.yml) file.  
`theme: jekyll-theme-chirpy`

## plugins

We can update the [_config.yml](../_config.yml) to include new features using plugins. We can then support [GitHub emojis](https://gist.github.com/rxaviers/7360908) with `jemoji`. :metal:

## Layouts

Default layout (ex. `layout: post`) can be found [here](https://github.com/pages-themes/hacker/tree/master/_layouts). You can create your own [like this](../_layout/blog_post.html).
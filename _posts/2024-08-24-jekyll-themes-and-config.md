---
layout: newpost
title: "Understanding GitHub Pages and Jekyll"
date: 2024-08-24
categories: [tech]
tags: [jekyll,github pages]
---


GitHub Pages allows you to host websites directly from your GitHub repository. Simple as pie? :heart: :computer:

## Jekyll themes?

Let's explore the *supported* [themes](https://pages.github.com/themes/).

Some favorites:
- [`hacker`](https://pages-themes.github.io/hacker/) theme seems good for obvious reasons :star:
- [`midnight`](https://pages-themes.github.io/midnight/) is a contender
- [`minima`](https://jekyll.github.io/minima/) with some nice *themes*
- [`cayman`](https://pages-themes.github.io/cayman/) seems smooth

# Configuration

We just update the `theme` in the [_config.yml](../_config.yml) file.  
`theme: jekyll-theme-chirpy`

## plugins

We can update the [_config.yml](../_config.yml) to include new features using plugins. We can then support [GitHub emojis](https://gist.github.com/rxaviers/7360908) with `jemoji`. :metal:

## Layouts

Default layout (ex. `layout: post`) can be found [here](https://github.com/pages-themes/hacker/tree/master/_layouts). You can create your own [like this](../_layout/newpost.html).

## File structure *(added 1/9)*

Reading the [docs](https://jekyllrb.com/docs/structure/), I apparently need to change the file structure of my posts:

```txt
├── _posts
│   ├── 2007-10-29-why-every-programmer-should-play-nethack.md
│   └── 2009-04-26-barcamp-boston-4-roundup.md
```

## Running Jekyll locally

Then we use [the GitHub pages guide](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/testing-your-github-pages-site-locally-with-jekyll) to get Jekyll up and running:

1. Ruby is already installed in my WSL setup.
    - I had an old version of ruby so used [ruby-install](https://github.com/postmodern/ruby-install#readme) to install a newer version.
    - Ruby path `/home/vthoren_u/.rubies/ruby-3.3.4/bin/ruby`
    ```sh
    echo '# Install Ruby Gems to ~/gems' >> ~/.zshrc
    echo 'export GEM_HOME="$HOME/gems"' >> ~/.zshrc
    echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.zshrc
    alias gem="/home/vthoren_u/.rubies/ruby-3.3.4/bin/gem"
    source ~/.zshrc
    ```
2. Installing bundle and jekyll `gem install jekyll bundler`
3. Fixing the Gemfile with the version of github-pages
4. Install dependencies `bundle install`
5. Start jekyll `bundle exec jekyll serve`
6. Test! [http://127.0.0.1:4000/](http://127.0.0.1:4000/)


## Continue to develop the site

The post from [aleksandrhovhannisyan.com](https://www.aleksandrhovhannisyan.com/blog/getting-started-with-jekyll-and-github-pages) is really good at getting started!
---
layout: newpost
title: "Searching command history with hstr"
date: 2024-09-26
categories: [tech]
tags: [command, hstr, history]
---

Have you ever been trying to find command you've use before, only to find all incorreclty types commands? Does this seem familiar? `history | foobar` or `ctrl+r`+`foobar` `barfoor` `arfoo` etc.

Look no further [`hstr`](https://dvorka.github.io/hstr/) is there to save the day (notice the niiiice theme?)! :star:

- [Install and use](#install-and-use)
- [Conclusion](#conclusion)

---

# Install and use

Install with:  
`sudo apt install hstr`

Now configure `hstr`:  
**bash**  
```sh
hstr --show-configuration >> .bashrc
source ~/.bashrc
```
**zsh**  
```sh
hstr --show-zsh-configuration >> .zshrc
source ~/.zshrc`
```

`ctrl+r` and viola!

![hstr](/assets/images/blogs/hstr.gif)

---

# Conclusion

Easy as pie, searching through the command history was made that much simpler with such an easy installation! :pie:
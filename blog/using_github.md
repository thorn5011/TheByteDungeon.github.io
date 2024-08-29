---
layout: newpost
title: "Using GitHub on Raspberry Pi"
date: 2024-08-29
categories: tech
---

Each time I setup a new box, be it a new installation of a Raspberry or be it a fresh VM, I need to google a bunch to times to grab commands and a new GitHub dev key to authenticate. Well, this time I'll try to document the process so new time might be slightly less painstaking. Let's get started!

# Tools needed

- [`gh`](https://github.com/cli/cli?tab=readme-ov-file#linux--bsd) - use one-liner to install with `apt`. This is **not** the same as `git` which is an easy mistake for us n00bs to make. 
- [`git`](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) - often pre-installed on Debian-based distributions, like with Raspbian.

# How to login with gh

1. Paste `BROWSER=false gh auth login` (since I want to access GitHub from my desktop, I'll use `BROWSER=false`)
   ![github_setup2](../images/blogs/github_setup2.png)
2. Access [https://github.com/login/device](https://github.com/login/device) from desktop
3. Use the one-time code
4. Allow access and verify with MFA
![github_setup1](../images/blogs/github_setup1.png)
1. Success!
![github_setup3](../images/blogs/github_setup3.png)

But wait, what was that, "..credentials saved in **plain text**"? Perhaps that is not a surprise once we consider how any authentication schema work but I like that it is clearly stated.

Here is the culprit -> `cat $HOME/.config/gh/hosts.yml`
```sh
github.com:
    users:
        thorn5011:
            oauth_token: gho_TOKEN
    git_protocol: https
    user: thorn5011
    oauth_token: gho_TOKEN
```


# Results

Clone wit ease `gh repo clone thorn5011/repo_name`, now I can continue to clone, push, pull and all those wonderful actions without a need to re-authenticate! 

```sh
$ git add lol123
$ git push
Username for 'https://github.com':
```
***record scratch*** wait what?! Remeber how `gh` and `git` ain't the same? Ahh, yes..

- `GitHub`: a web-based platform for hosting a collaborating. Owned by Microsoft
- `Git`: an open-source tool for version control owned by the Linux foundation and developed by the G.O.A.T. Linus Torvalds.

That means `gh` is a tool to interact with GitHub, while `git` can be used to many other vendors as long as the support git for version control. BUT we can actually use gh to help use setup git - `gh auth git-setup` ![micdrop](../images/micdrop.gif){:height="36px" width="36px"}



# Conclusion

We can use `gh` for cloning repos, creating issues, PR etc. For pushing and pulling we still need `git`. But we can also get help from gh  to set up git for further use with:

`gh auth git-setup`
---
layout: newpost
title: "Maxing out the CPU"
date: 2024-08-26
categories: tech
---

After the computers wakes up after a deep slumber, i.e. post hiberation the CPU seems to spike. The `vmmem` process is always the guilty party..
The process is used by Hyper-V to deal with the CPU and memory of the virtual machines, and in my case WSL (Windows Subsystem for Linux).

![cpu](../images/blogs/vmmem.png)

This make all WSL sessions hang and the behaviour does not stop until we forcefully suffocate the `vmmem` process. :zzz:

Let's face it, locating the service in the GUI, or using a privileged admin session can be quite boring, especially when I keep forgetting the name. There are plenty of ways to get the job done, but here's my go-to method for achieving sweet, sweet tranquility.

I've got a shortcut file on the Desktop which simply kills the `wslservice`:

```ps1
C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe taskkill /f /im wslservice.exe
```

Once I've executed this as **admin** we are back at a 2% CPU usage, aaaah good times :tropical_drink:


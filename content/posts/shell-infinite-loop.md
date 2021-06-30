---
title: "shell one-liner for infinite loop"
author: ["Dhananjay"]
date: 2021-06-30T13:33:00+05:30
tags: ["shell", "sh", "bash", "zsh", "loop", "utility"]
categories: ["linux"]
draft: false
weight: 2002
---

This is for a quick reference (copy-pasta).


## While loop {#while-loop}

This is compatible with all POSIX complient shells.

```shell
while true; do date; sleep 5; done
```


## For loop {#for-loop}

If you don't need to support `sh`

```shell
for ((;;)) { date ; sleep 5 ; }
```

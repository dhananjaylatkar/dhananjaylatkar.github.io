---
title: "awk command in Linux"
author: ["Dhananjay"]
date: 2021-07-29T23:51:00+05:30
tags: ["awk", "cli", "utility", "tutorial"]
categories: ["linux"]
draft: false
weight: 2004
---

## Introduction {#introduction}

`awk` is pattern-directed scanning and processing language. It is useful for doing quick filtering of other commands.
This article will show you basic usage of `awk` and how it can be integrated in daily workflow.


## Grabbing column(s) from text {#grabbing-column--s--from-text}

In this example we'll grab column(s) from `ps` command.


### `ps` command {#ps-command}

`ps` output has 4 columns.

```shell
❯ ps
PID TTY           TIME CMD
608 ttys000    0:00.46 -zsh
613 ttys000    0:00.01 -zsh
797 ttys000    0:00.00 -zsh
798 ttys000    0:01.11 -zsh
```


### Print one column {#print-one-column}

Let's say, you want to print only PIDs from `ps` output.

`$1` below signifies position of column (Yes, numbering starts with 1). `$0` prints all columns.

```shell
❯ ps | awk '{print $1}'
PID
608
613
797
798
```


### Print multiple columns {#print-multiple-columns}

We usually want to see PID and corresponding commnad side by side.

You can print multiple columns by adding more args to `print`.

```shell
❯ ps | awk '{print $1, $4}'
PID CMD
608 -zsh
613 -zsh
797 -zsh
798 -zsh
```


### Print last column {#print-last-column}

Nobody wants to count all columns to print last one. Luckily `awk` has a way to do that.

```shell
❯ ps | awk '{print $NF}'
CMD
-zsh
-zsh
-zsh
-zsh
```


## Field separator {#field-separator}

By default `awk` uses whitespace as separator. It can be changed using `-F` arg.


### A file separated by `:` {#a-file-separated-by}

`/etc/passwd` has columns separated by `:`

```shell
❯ cat /etc/passwd
root:x:0:0:root:/root:/usr/bin/zsh
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
...
```


### Get all users on system {#get-all-users-on-system}

From previous output we can see that first column contains username. So, print first column and use `:` as a separator.

```shell
❯ awk -F ":" '{print $1}' /etc/passwd
root
daemon
bin
sys
sync
games
man
lp
mail
news
...
```


### Add custom separator before printing {#add-custom-separator-before-printing}

Let's print user and it's default shell but, add a sensible separator before printing. Having a single space as separator is hard to read. We'll use `\t`.

```shell
❯ awk 'BEGIN{FS=":"; OFS="\t"} {print $1, $NF}' /etc/passwd
root    /usr/bin/zsh
daemon  /usr/sbin/nologin
bin     /usr/sbin/nologin
sys     /usr/sbin/nologin
sync    /bin/sync
games   /usr/sbin/nologin
man     /usr/sbin/nologin
lp      /usr/sbin/nologin
mail    /usr/sbin/nologin
news    /usr/sbin/nologin
...
```


## Pattern matching {#pattern-matching}

Pattern should be between two `/` s (like `/<pattern>/`).

Print all usernames which start with letter `d` and their respective shells.

```shell
❯ awk -F ":" '/^d/ {print $1, $NF}' /etc/passwd
daemon /usr/sbin/nologin
dnsmasq /usr/sbin/nologin
dhananjay /usr/bin/zsh
```


## Some more goodies {#some-more-goodies}


### `BEGIN` and `END` rules {#begin-and-end-rules}

A BEGIN rule is executed once before any text processing starts. In fact, it’s executed before awk even reads any text.

An END rule is executed after all processing has completed. You can have multiple BEGIN and END rules, and they’ll execute in order.

```shell
❯ awk -F ":" 'BEGIN {print "User shells\n============"} {print $1 "\t" $NF}' /etc/passwd | head
User shells
============
root    /usr/bin/zsh
daemon  /usr/sbin/nologin
bin     /usr/sbin/nologin
sys     /usr/sbin/nologin
sync    /bin/sync
games   /usr/sbin/nologin
man     /usr/sbin/nologin
lp      /usr/sbin/nologin
```


### Arithmatic {#arithmatic}

```shell
❯ df -h | awk '{print $2 "\t" $3+$4}'
Size    0
3.9G    3.9
793M    793.5
689G    661
3.9G    3.9
5.0M    5
3.9G    3.9
```


### Lenght of string {#lenght-of-string}

```shell
❯ df -h | awk 'length($1) > 5'
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1      689G  132G  529G  20% /
/dev/loop3       56M   56M     0 100% /snap/core18/2066
/dev/loop5      163M  163M     0 100% /snap/gnome-3-28-1804/145
/dev/loop7      219M  219M     0 100% /snap/gnome-3-34-1804/66
...
```


### `if` condition {#if-condition}

Print all PIDs owned by your user.

```shell
❯ ps aux | awk '{ if($1 == "dhananj+") print $2}' | head
2283
2292
...
```


### Some built-in functions {#some-built-in-functions}

-   Square root

<!--listend-->

```shell
❯ awk 'BEGIN { print sqrt(25)}'
```

-   arctangent

<!--listend-->

```shell
❯ awk 'BEGIN {print atan2(0, -1)}'
```

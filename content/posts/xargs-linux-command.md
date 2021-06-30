---
title: "xargs linux command"
author: ["Dhananjay"]
date: 2021-06-30T23:13:00+05:30
tags: ["xargs", "cli", "utility", "tutorial"]
categories: ["linux"]
draft: false
weight: 2003
---

The xargs utility reads space, tab, newline and end-of-file delimited strings from the standard input and executes utility with the strings as arguments.


## Why xargs? {#why-xargs}

Some commands like `grep` can accept input from `stdin`, but others can't, this is place where xargs came into picture.

In layman's terms, output of one command can be transfed to another command as a arguments. Piping outputs to some programs work (like `grep`, `awk`, `sort`, `uniq`, etc) but, it doesn't work always (e.g. `kill`, `mkdir`, `touch`, etc).


## Examples {#examples}

Examples are always a best way to learn about CLI programs. Let's dive into it.


### Simple use case {#simple-use-case}

Make folder using output from other command. If nothing is given it'll pass input to `echo`.

```shell
$ echo "my-direcory" | xargs mkdir
```


### -t switch {#t-switch}

-   `-t` switch will show what `xargs` is doing.

<!--listend-->

```shell
$ echo "my-directory-1 my-directory-2" | xargs -t mkdir
mkdir my-directory-1 my-directory-2
```

-   Pass outputs one by one.

<!--listend-->

```shell
$ echo "my-directory-1 my-directory-2" | xargs -t -n 1 mkdir
mkdir my-directory-1
mkdir my-directory-2
```


### -0 switch {#0-switch}

If input items are terminated by null character instead of white spaces.

```shell
$ echo "file1\0file2\0" | xargs -0 touch
```


### Manupulate input before passing to program {#manupulate-input-before-passing-to-program}

`-I` can be used to do this.
Let's say you want to bulk rename all files in a folder.

```shell
$ ls | xargs -I @@ mv @@ @@.sh
```

`@@` used in above example is just a variable, you can use any other symbols.


### Wait before executing next command {#wait-before-executing-next-command}

`-P` allows to do this. You can specify number of parellel executions.

Below command executes `echo` on each `1,2,3,4` one by one with 1 sec sleep in between.
Number after `-P` is used to specify number of parallel executions.

```shell
$ echo "1 2 3 4" | xargs -n 1 -P 1 bash -c 'sleep 1; echo $0'
```

Let's say, you want to execute 2 commands simultaneity and wait 1 sec before next 2.

```shell
$ echo "1 2 3 4" | xargs -n 1 -P 2 bash -c 'sleep 1; echo $0'
```

---
title: "Vim Cheatsheet"
date: 2022-05-23T23:23:56+05:30
tags: ["vim", "cheatsheet", "neovim"]
categories: ["editor"]
draft: false
---

## Introduction
This cheatsheet contains must know vim tips and tricks.

## Navigation

### Horizontal Navigation

- `h, j, k, l` : left, down, up, right
- `w` : next word
- `b` : prev (back) word
- `0` : Beginning of line
- `^` : First char of line
- `$` : End of line
- `g_`: Last char on line
- `f<char>` : Search for char in current line (`;` to repeate)
- `F<char>` : Search backward for char in current line (`;` to repeate)
- `t<char>` : Search until the char in current line (`;` to repeate)
- `T<char>` : Search backward until the char in current line (`;` to repeate)

### Vertical Navigation

- `gg`: First Line
- `G` : Last Line
- `H` : Move cursor to Top
- `M` : Move cursor to Middle
- `L` : Move cursor to Bottom
- `zz`: Move line to Middle
- `zt`: Move line to Top
- `zb`: Move line to Bottom
- `C-y` : Scroll up by one line
- `C-e` : Scroll down by one line
- `C-u` : Scroll up by half page
- `C-d` : Scroll down by half page
- `C-b` : Scroll up by full page
- `C-f` : Scroll down by full page
- `*` : search forward for word under cursor
- `#` : search backward for word under cursor
- `%` : jump to matching parentheses

## Buffers

- `:ls` : List all Buffers
- `:b<num>` : Goto `<num>` buffer
- `:bnext` : Goto next buffer
- `:bprev` : Goto prev buffer
- `C-^`: Goto alternate buffer

## Window Navigation

- `C-w`: Start "window mode"
- `C-w`+`v`: Vertical Split
- `C-w`+`s`: Horizontal Split
- `C-w`+`w`: Other Window
- `C-w`+`p`: Previous Window
- `C-w`+`q`: Close Window

## Marks

- `ma` : Set mark (stores it in reg `a`)
- `'a` : Goto the first char of line of mark `a`
- \`a  : Goto the mark `a`
- `['` : Goto next mark (does not wrap)
- `]'` : Goto prev mark (does not wrap)
- `mA` : Capital letter marks work even from different file
- `:marks` : See all marks
- `:delmarks a,A` : Delete marks `a` and `A`
- `:delmarks!` : Delete all lowercase marks

## Registers

### Copy/Delete/Paste

- `"ayw` : Copy word to `a` register
- `"adw` : Delete/Cut word to `a` register
- `"ap`  : Paste contents of `a` register

## Macros

- `q<reg>` : Start recording macro in `<reg>`. `q` to stop recording
- `@<reg>` : Run macro from `<reg>`
- Made a mistake in long macro and want to re-record it?
  - Paste it in buffer, edit it and yank back in `<reg>`

## Insert Mode

### Repeating chars

`C-o 10i # <ESC>`

### Paste

- `C-r` and register to insert text from register
    e.g. `C-r+"` : Inserts last yanked test.

- `.` register stores last typed text.
    e.g. `C-r+.` : Inserts last typed text.
         `C-a`   : Short for `C-r+.`

### Completion

- Start completion with `C-p`. Cycle through it using `C-p` and `C-n`.
    Completions are taken from `complete` setting.
    `:set complete`: See current `complete` setting.
    `set complete=.,w,b,u,t,i,kspell`
- Tag completion: `C-x`+`C-]`
    `C-x` puts you into Completion Mode. `C-]` will search for only in ctags file.
- File completion: `C-x`+`C-f`
    It completes recursively!
- Context-aware completion: `C-x`+`C-p` and `C-x`+`C-n`
- Context-aware Line completion: `C-x`+`C-l`

## Misc

- `C-a`: Increment number under cursor
- `C-x`: Decrement number under cursor
- `g`+`C-a`: Try and find out!


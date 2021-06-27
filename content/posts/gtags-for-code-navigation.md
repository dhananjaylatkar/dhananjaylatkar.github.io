---
title: "`gtags` for code navigation"
author: ["Dhananjay"]
date: 2021-06-27T15:45:00+05:30
tags: ["gtags", "cscope", "vim", "neovim"]
categories: ["editor"]
draft: false
weight: 2001
---

## Introduction {#introduction}

GNU Global, also known as `gtags` is a source code tagging system that works the same way across diverse environments. It can be used in `shell`, `vim`, `emacs` and more.


## Why \`gtags\`? {#why-gtags}

-   Independent of any editor.
-   Has capability to treat definition and reference.
-   Substantially faster than `cscope` and `ctags`.


## Installation {#installation}


### Ubuntu {#ubuntu}

```shell
sudo apt install global
```


### MacOS {#macos}

Using [homebrew](https://brew.sh).

```shell
brew install global
```


### Build from source {#build-from-source}

-   Get source code from `global` server and extract it.

<!--listend-->

```shell
wget https://ftp.gnu.org/pub/gnu/global/global-6.6.tar.gz
tar -xvzf global-6.6.tar.gz
cd global-6.6
```

-   Compile and Install

<!--listend-->

```shell
./configure
make -j4
sudo make install
```


## Using \`gtags\` in \`neovim\` {#using-gtags-in-neovim}

There are lots of ways to use `gtags`. I prefer to use it in `neovim`. Same instructions can be used for `vim`.

We'll be using plugins to set-up auto tag generation for a project. These plugins re-generate DB when files in project are changed.
Thanks to the incremental update support by `gatgs` re-generation takes only few seconds.

-   My setup for this tutorial:
    -   `neovim` v0.5 (nightly)
    -   Plugin manger - [packer.nvim](https://github.com/wbthomason/packer.nvim)


### Plugin installation {#plugin-installation}

We'll use [vim-gutentags](https://github.com/ludovicchabant/vim-gutentags) and [gutentags\_plus](https://github.com/skywind3000/gutentags%5Fplus).

Add these lines to `init.lua` and run `:PackerSync`

```lua
local use = require('packer').use
require('packer').startup(function()
  -- snip --
  use 'ludovicchabant/vim-gutentags' -- Automatic tags management
  use 'skywind3000/gutentags_plus'   -- Cscope support
  -- snip --
end)
```


### Few more configurations {#few-more-configurations}

Use following settings for better experience.

```lua
-- Gutentags
-- generate cscope compatible DB
vim.g.gutentags_modules = {'gtags_cscope'}
-- add folders you want to exclude from index e.g. {'tests', 'doc'}
vim.g.gutentags_ctags_exclude = {}
 -- project root detection other that default ones e.g {'.projectile'}
vim.g.gutentags_project_root = {}
-- store DB in different dir so project doesn't get cluttered
vim.g.gutentags_cache_dir= '~/.gutentags'
-- change focus to quickfix window after search
vim.g.gutentags_plus_switch = 1
-- Debugging
vim.g.gutentags_define_advanced_commands = 1
```


### Keymaps {#keymaps}

| Keymap       | Description                                      |
|--------------|--------------------------------------------------|
| `<leader>cs` | Find symbol (reference) under cursor             |
| `<leader>cg` | Find symbol definition under cursor              |
| `<leader>cd` | Functions called by this function                |
| `<leader>cc` | Functions calling this function                  |
| `<leader>ct` | Find text string under cursor                    |
| `<leader>ce` | Find egrep pattern under cursor                  |
| `<leader>cf` | Find file name under cursor                      |
| `<leader>ci` | Find files #including the file name under cursor |
| `<leader>ca` | Find places where current symbol is assigned     |
| `<leader>cz` | Find current word in ctags database              |


## Example {#example}

Code navigation in `cURL` source code.

Pressing `<leader>cg` on `Curl_convert_to_network` symbol opens a quickfix window with links to defination.

<a id="orgdf9ff43"></a>

{{< figure src="/images/gtags-curl.png" caption="Figure 1: cURL source code navigation" >}}

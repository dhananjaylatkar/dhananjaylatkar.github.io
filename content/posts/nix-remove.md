---
title: "Uninstall nix package manager from Linux"
date: 2022-04-20T16:56:36+05:30
tags: ["linux", "nix"]
categories: ["package_manager"]
draft: false
---

## Introduction
As of Apr'22 nix package manager does not have official uninstaller. This post provides a way to remove nix. It can also be found in man page.

## Uninstall NIX

1. Remove `nix` related folders and Files
    ```bash
    rm -rf $HOME/{.nix-channels,.nix-defexpr,.nix-profile,.config/nixpkgs}
    sudo rm -rf /nix
    ```

2. Remove all references to `nix` in `~/.profile`, `~/.bashrc` and `~/.zshrc`

3. Cleanup `systemd`
    ```bash
    sudo systemctl stop nix-daemon.socket
    sudo systemctl stop nix-daemon.service
    sudo systemctl disable nix-daemon.socket
    sudo systemctl disable nix-daemon.service
    sudo systemctl daemon-reload
    ```

4. Delete `nixbldX` users 
    ```bash
    grep nixbld /etc/passwd | awk -F ":" '{print $1}' > /tmp/nixusers
    cat /tmp/nixusers | xargs -t -n 1 sudo userdel -r
    ```

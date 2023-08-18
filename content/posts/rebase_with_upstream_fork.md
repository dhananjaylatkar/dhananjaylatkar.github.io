---
title: "git rebase with upstream fork"
date: 2023-08-18T18:00:08+05:30
tags: ["git", "github", "rebase", "fork"]
categories: ["developement"]
draft: false
---

This post details the instructions to sync forked repo with upstream.

## Steps to rebase

Add original repo to fork as upstream
```bash
git remote add upstream https://link/to/original/repo.git
```

Fetch master (or main) from upstream
```bash
git fetch upstream master
```

Rebase current branch with upstream master
```bash
git rebase upstream/master
```

Force push to remote
```bash
git push --force
```

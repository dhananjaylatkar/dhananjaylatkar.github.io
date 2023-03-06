---
title: "Extend btrfs root partition"
author: ["Dhananjay"]
tags: ["btrfs", "linux", "filesystem", "fs", "fedora"]
categories: ["operating system"]
date: 2023-03-06T00:25:44+05:30
draft: false
---

## Introduction
This article will demonstrate how to increase btrfs root partition size.

Use case:
- In dual boot system (Fedora + Windows), use unsed disk space in Windows for Fedora.

## Steps

### Shrink Windows drive
- Boot into Windows.
- Shrink unsed drive using Disk Manager.

### Create partition
- Boot into Fedora.
- Use `gpart` (CLI) or Disks (GUI) to create partition.
- Check partition using `lsblk`. (`nvme0n1p8`  is newly created partition).
    ```
    ➜ sudo lsblk
    [sudo] password for dhananjay:
    NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
    zram0       252:0    0     8G  0 disk [SWAP]
    nvme0n1     259:0    0 476.9G  0 disk
    ...
    ├─nvme0n1p6 259:6    0     1G  0 part /boot
    ├─nvme0n1p7 259:7    0 145.5G  0 part /home
    │                                     /
    └─nvme0n1p8 259:8    0 169.1G  0 part
    ```

### Extend root
- `df` output before extending.
    ```
    ➜ df
    Filesystem      Size  Used Avail Use% Mounted on
    ...
    /dev/nvme0n1p7  146G  113G   32G  79% /
    ...
    ```
- Extend `/` using `btrfs` tool.
    ```
    ➜ sudo btrfs device add -f /dev/nvme0n1p8 /
    Performing full device TRIM /dev/nvme0n1p8 (169.14GiB) ...
    ```
- `df` output after extending.
    ```
    ➜ df
    Filesystem      Size  Used Avail Use% Mounted on
    ...
    /dev/nvme0n1p7  315G  113G  201G  37% /
    ...
    ```
- That's all!

### Balancing data between 2 partitions (Optional)
- Check device usage.
    ```
    ➜ sudo btrfs device usage /
    /dev/nvme0n1p7, ID: 1
       Device size:           145.48GiB
       Device slack:              0.00B
       Data,single:           138.01GiB
       Metadata,DUP:            4.00GiB
       System,DUP:             16.00MiB
       Unallocated:             3.46GiB

    /dev/nvme0n1p8, ID: 2
       Device size:           169.14GiB
       Device slack:              0.00B
       Unallocated:           169.14GiB
    ```
- Run following command to balance the data. (It takes time based on disk size.)
    ```
    ➜ sudo btrfs device usage /
    ```
- Verify device usage.
    ```
    ➜ sudo btrfs device usage /
    [sudo] password for dhananjay:
    /dev/nvme0n1p7, ID: 1
       Device size:           145.48GiB
       Device slack:              0.00B
       Data,single:            72.00GiB
       Metadata,DUP:            2.00GiB
       System,DUP:             64.00MiB
       Unallocated:            71.42GiB

    /dev/nvme0n1p8, ID: 2
       Device size:           169.14GiB
       Device slack:              0.00B
       Data,single:            96.00GiB
       Metadata,DUP:            2.00GiB
       Unallocated:            71.14GiB
    ```

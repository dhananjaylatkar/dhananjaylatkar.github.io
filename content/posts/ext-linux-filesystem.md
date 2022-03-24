---
title: "ext Linux Filesystem"
author: ["Dhananjay"]
date: 2022-03-24T21:36:07+05:30
tags: ["ext", "ext2", "ext3", "ext4", "linux", "filesystem", "fs"]
categories: ["operating system"]
draft: false
weight: 2011
---
This post covers all versions of `ext` filesystem.
## ext
- Created to overcome limitations of MINIX filesystem.
- First filesystem specificaly desined for Linux kernel.
- Restricted filename lengths to 255 characters and supported partitions up to 2GB.
- Major flaw - Allowed only one timestamp per file.

## ext2
- Released less than year afer introduction of ext.
- Supported the maximum file size of 2TiB and filename can be 255 bytes long.
- Does not support journaling.
    - Leads to data corruption on power loss.
    - Less write actions => Better lifetime of flash chips.
- Performance loss due to fragmentation.
- Use `sudo dumpe2fs /dev/sdb1 | less` to get info about filesystem.


### ext2 (Physical Layout)
- The space in ext2 is split up into blocks.
- Blocks are grouped into block groups.
- Block Group
    - copy of the superblock
    - block group descriptor table
    - block bitmap
    - inode bitmap
    - inode table
    - actual data blocks

    ![ext2-block-group](/images/fs-ext2-block-group.png)

### ext2 (inode)
- Every file and directory in the file system is described by one and only one inode.
- Each inode contains:
   - mode: describe one of file, directory, symbolic link, block device, character device or FIFO.
   - Owner info: The user and group identifiers of the owners of this file or directory.
   - Size: Size of file in bytes.
   - Timestamps: time that the inode was created, accessed and the last time that it was modified
   - Datablocks: Pointers to the blocks that contain the data that this inode is describing.

    ![ext2-inode](/images/fs-ext2-inode.png)

### ext2 (superblock)
- The Superblock contains a description of the basic size and shape of this file system.
- Usually only the Superblock in Block Group 0 is read when the file system is mounted but each Block Group contains a duplicate copy in case of file system corruption
- Some important info stored in Superblock:
   - Magic Number: For the current version of EXT2 this is 0xEF53.
   - Revision Level: Some revision info to help mounting software to detect available features.
   - Mount Count and Maximum Mount Count: Number of times the system is mounted.
   - Block Group Number: The Block Group number that holds this copy of the Superblock.
   - Block Size: The size of the block for this file system in bytes.
   - Blocks per Group: The number of blocks in a group.
   - Free Blocks: The number of free blocks in the file system.
   - Free Inodes: The number of free Inodes in the file system.
   - First Inode: This is the inode number of the first inode in the file system.

### ext2 (Group Descriptor)
- All the group descriptors for all of the Block Groups are duplicated in each Block Group in case of file system corruption.
- Group Descriptor contains following info:
   - Blocks Bitmap: The block number of the block allocation bitmap for this Block Group.
   - Inode Bitmap: The block number of the inode allocation bitmap for this Block Group.
   - Inode Table: The block number of the starting block for the inode table for this Block Group.
   - Free blocks count:
   - Free Inodes count:
   - Used directory count:

### ext2 (Directories)
- Directories are special files that are used to create and hold access paths to the files in the file system.
- A directory file is a list of directory entries, each one containing the following information:
    - inode: The inode for this directory entry.
    - name length: The length of this directory entry in bytes.
    - name: The name of this directory entry.

- The first two entries for every directory are always the standard `.` and `..` entries

![ext2-dir](/images/ext2-dir.png)

## ext3
- ext3 adds the following features to ext2:
    - A journal
    - Online file system growth
    - HTree indexing for larger directories
- Shares same set of standard utilities with ext2.

### ext3 (journaling)
ext3 supports 3 levels of journaling
- Journal
    - Both metadata and file contents are written to the journal before being committed to the main file system.
    - Journal is relatively continuous on disk, this can improve performance, if the journal has enough space.
- Ordered
    - Only metadata is journaled; file contents are not, **but** it's guaranteed that *file contents are written to disk before associated metadata is marked as committed in the journal*.
    - In case of crash while a file is being written or appended to, the journal will indicate that the new file or appended data has not been "committed", so it will be purged by the cleanup process.
    - Files being overwritten can be corrupted because the original version of the file is not stored. Thus it's possible to end up with a file in an intermediate state between new and old, without enough information to restore either one or the other
- Writeback
    - Only metadata is journaled; file contents are not.
    - The contents might be written before or after the journal is updated.
    - A file being appended to may be marked in the journal as being larger than it actually is, causing garbage at the end. Older versions of files could also appear unexpectedly after a journal recovery.

### ext3 (Disadvantages)
- Defragmentation
    There is no online ext3 defragmentation tool that works on the filesystem level.
    `e2defrag` works on ext3 but, might destroy data.
    There are userspace defragmentation tools, like Shake and defrag.
- Compression: No officaial way to compress ext3 fs. e3compr[30] is an unofficial patch for ext3 that does transparent compression.
- Lack of journal checksum
- 4 byte Timestamps which will not work after January 18, 2038.
- Lack of Snapshots. `Next3` is modified `ext3` which supports Snapshots.

## ext4
ext4 modifies important data structures of the filesystem such as the ones destined to store the file data. The result is a filesystem with an improved design, better performance, reliability and features.
- Large file system
    - volumes with sizes up to 1 exbibyte (EiB)
    - single files with sizes up to 16 tebibytes (TiB) with the standard 4 KiB block sizes
    - Unlimited number of subdirectories
- Extents
    - Extents replace the traditional block mapping scheme used by ext2 and ext3
    - An extent is a range of contiguous physical blocks, improving large-file performance and reducing fragmentation.
    - A single extent in ext4 can map up to 128 MiB of contiguous space with a 4 KiB block sizes
    - There can be four extents stored directly in the inode.
- Fragmentation
    - EXT4 reduces fragmentation by scattering newly created files across the disk
    - pre-allocate extra disk space when a new file is created or when an existing file is extended.
    - New files are never allocated immediately after existing file
- Delayed Allocation (Multiblock Allocator)
    - allow the filesystem to collect all the data being written to the disk before allocating space to it.
- Checksum
    - ext4 uses checksums in the journal to improve reliability
    - metadata checksumming was added in 2012
- Unallocated block groups and sections of the inode table are marked as such. This enables file system checking tools to skip over block groups.
- Timestamps have nano sec accuracy. Extra 2 bits were added to defer 2038 problem by 408 more years.


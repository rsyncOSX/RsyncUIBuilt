+++
author = "Thomas Evensen"
date = "2024-02-20"
title =  "Network-attached storage (NAS)"
tags = ["NAS"]
categories = ["NAS"]
lastmod = "2024-04-03"
+++
I have been using a server at home as a backup and sharing files for a long time. I have also been very fascinated [about ZFS](https://openzfs.org/wiki/Main_Page) as a filesystem and have commenced using it as part of OpenSolaris many years ago. To make a long story short, the size and cost of my servers have decreased over the years. Last year, 2023, I sold my dedicated server HW and cabinet. On that server, I connected two WD Red Sata SSD discs, 500 GB each, in a mirrored setup by ZFS. The SATA discs has now been reused on the new NAS.  My NAS is not sharing files, it is only used for backup of data using RsyncUI. 

I have bought two Raspberry Pis. One Pi4 last year and recently the newest Pi5, both with 8GB of RAM. The Pi5 is more powerful than the Pi4, but it is not yet supported by FreeBSD, which the Pi4 is. The Pi5 is now the main NAS. Or, to be more precise, my backup server.

The latest version of Netdata was downloaded from GitHub and compiled out of the box on both Pis. On the Pi5, some development libraries were required to be installed. The process of compiling Netdata informed which libraries were missing, and some googling was done to install the missing libraries.

## Raspberry Pi5 - 8 GB ram

For the moment there is no official support for FreeBSD on the Pi5. But the default Raspberry PI OS 64-bit, which is based on Debian 12 Bookworm, supports the ZFS filesystem. To install ZFS follow the instructions in [OpenZFS for Debian](https://openzfs.github.io/openzfs-docs/Getting%20Started/Debian/index.html). I am not using ZFS on root, only importing the mirrored zpool used on the Pi4.

Powersupplly: the official USB C Raspberry Pi 27W, max 5A.

## Raspberry Pi4 - 8GB ram

The Raspberry Pi4 is official supported by FreeBSD. It is very easy to install FreeBSD on it; there are several guides around. My Pi4 runs plain vanilla FreeBSD 14. The two SSDs was connected, and ZFS filesystems were imported. Netdata was downloaded from GitHub and compiled on the Pi4 for monitoring. Max power consumption is between 8 and 9 watts. 

Powersupplly: the official USB C Raspberry Pi 15W, max 3A.
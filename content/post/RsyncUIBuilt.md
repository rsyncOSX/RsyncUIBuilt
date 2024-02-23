+++
author = "Thomas Evensen"
title = "RsyncUI - overview"
date = "2024-02-22"
tags = ["Overview"]
categories = ["Overview"]
lastmod = "2024-02-22"
+++
RsyncUI is a pure *SwiftUI*  and Swift based macOS application utilizing the command line tool `rsync` for synchronizing files. It is `rsync` which executes the real synchronizing task, not RsyncUI. RsyncUI is a GUI only on top of rsync. The development of RsyncUI has been an evolving process. The open source community has been and still is a great resource for ideas and how to solve issues. The SwiftData version of RsyncUI is developed, but not released as a dmg file. 

| App      | Storage  | Lines of code | Swift files | Version 1.0 |
| ----------- | ----------- |   ----------- | -------- | -------- |
| RsyncUI  | JSON files |  about 13.3K     | about 162       | 6 May 2021 |
| RsyncUI  | SwiftData |  about 11.7K     | about 137       | February 2024 |

# Why this site

RsyncUI is by no means an advanced or complicated application. But it is a pure SwiftUI and Swift based application, and learning about both languages has been and still is fun. And writing about the development is also fun. So there are no deeper thoughts about this web. There are many developers out there writing about SwiftUI, discovering new features, and how to use them. I am reading some of this myself, and this web is more about how I use SwiftUI features. 


# Why not App Store

One important requirement for macOS apps on Apple App Store is quote Apple: *"To distribute a macOS app through the Mac App Store, you must enable the App Sandbox capability."* There are restrictions what an app can do inside the App Sandbox. Execute `rsync`, obvious, and enable passwordless login by ssh to remote servers are two must have features. The Apple Sandbox causes a few issues to both features when switch on. And I have not yet any success when RsyncUI is set to use default rsync in macOS and only attached discs. But I am inveastigating, so there might be an Apple App Store version in the future.

Passwordless login by ssh is by ssh-keys and default ssh-key is:

```bash
$HOME/.ssh/id_rsa
```
The JSON version of RsyncUI is also storing data in a `.dotfile` catalog. This does not apply for the SwiftData version. 

```bash
$HOME/.rsyncosx/macserialnumber/*.json
```

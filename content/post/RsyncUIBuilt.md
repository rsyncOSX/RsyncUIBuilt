+++
author = "Thomas Evensen"
title = "RsyncUI - how is it built"
date = "2024-02-22"
tags = ["overview"]
categories = ["general information"]
lastmod = "2024-02-22"
+++

RsyncUI is a pure *SwiftUI*  and Swift based macOS application utilizing the command line tool `rsync` for synchronizing files. It is `rsync` which executes the real synchronizing task, not RsyncUI. RsyncUI is a GUI only on top of rsync. RsyncUI is signed and notarized by Apple.   This web is an overview of the main components of RsyncUI. The development of RsyncUI has been an evolving process. The open source community has been and still is a great resource for ideas and how to solve issues. Both apps today are stable and in a state of maintenance.  Check out  [the categories](/categories).

Some numbers:

| App      | Storage  | Lines of code | Swift files | Version 1.0 |
| ----------- | ----------- |   ----------- | -------- | -------- |
| RsyncUI  | JSON files |  about 13.3K     | about 162       | 6 May 2021 |
| RsyncOSX  |  JSON files |  about 11K   | about 121      | 14 March 2016 |	

Which application should I use? Both applications do the same job, but RsynUI is more feature-rich, and the GUI, including navigation, is better. **Are you on macOS Sonoma?** Go for RsyncUI. And for the last year and years to come, all development has been and will be within RsyncUI. 

# The future

Apple is clear: *SwiftUI*, which RsyncUI is developed by, is the future. This means *all new development* is on RsyncUI. The main differences between the two apps are the user interface (UI) and how the UI is built. There has also been a lot of cleanup and enhancements in code of RsyncUI. My advice is use RsyncUI. Both apps utilise another great declarative library, Combine, developed by Apple, and JSON files for storing tasks, log records, and user configuration. And the latest development, for test, is a version of RsyncUI using SwiftData instead of JSON files as storage. There is some more info about SwiftData later on this page.

| App      | UI | Paradigm |
| ----------- | ----------- |   ----------- |
| RsyncUI   | SwiftUI, Swift | declarativ  |
| RsyncOSX   | Storyboard, Swift  | imperativ |

# A few words about the code

Even though I am an educated IT person, most of my professional work has been as an IT manager and not a developer. Most of my coding experience is with private projects such as RsyncOSX and RsyncUI. Google is and has been a great resource for research and advice on how to solve specific problems. Reading about other developers code and discussions is always valuable input for me. The MVC pattern and single source of truth are important patterns for both apps. I have also tried to use all Apple Frameworks, utilizing most of the required built-in functions.  And even if there are many lines of code in both apps, I have tried to write as little code as possible. So if you are looking at my code, keep this in mind: my code is only one of many solutions.

I also believe there is no right or wrong in development. Each developer and team chooses their way to develop based on their skills and what they want to achieve. I know my own code, and I have no issues understanding what my code is doing. And as times go on, in my opinion, I make changes to the code for the better. I also fully understand that other developers might have some issues understanding my code. 

What I also experience is how my own understanding of OO, declarative programming, Swift and SwiftUI is growing. I would never write code today, as I did when I commenced my project in 2015 to learn Swift. But still, I am not a professional programmer, and there are several topics within Swift and SwiftUI that I do not understand. But I am capable of learning; if I spend enough time on a topic, there is a good probability that I will get the idea.


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

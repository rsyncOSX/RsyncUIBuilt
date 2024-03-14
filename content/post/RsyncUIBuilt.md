+++
author = "Thomas Evensen"
title = "RsyncUI - overview"
date = "2024-02-28"
tags = ["Overview"]
categories = ["Overview"]
lastmod = "2024-02-28"
+++
RsyncUI is a pure *SwiftUI*  and *Swift* based macOS application utilizing the command line tool `rsync` for synchronizing files. It is `rsync` which executes the real synchronizing task, not RsyncUI. RsyncUI is a GUI only on top of rsync. A  SwiftData version of RsyncUI is also developed, but not released as a dmg file. And there is for the moment no plan to release it either. It was primarly a project to learn more about SwiftData. 

| App      | Storage  | Lines of code | Swift files | Version 1.0 |
| ----------- | ----------- |   ----------- | -------- | -------- |
| RsyncUI  | JSON files |  about 11.8K     | about 150       | 6 May 2021 |
| RsyncUI  | SwiftData |  about 11.7K     | about 137       | February 2024 |

There are lesser code in the SwiftData version of RsyncUI; there are no read and write of JSON data wich requiere decode and encode data and no explicit write of all data when data is changed. Using SwiftData takes care of all this. 

# Why this site

RsyncUI is by no means an advanced or complicated application. But it is a pure SwiftUI and Swift based application, and learning about both languages has been and still is fun. And writing about the development is also fun. There is no deeper thoughts about this site. There are many developers out there writing about SwiftUI, discovering new features and how to use them. 

I am reading some of these sites myself. This web is not a site to show how to use SwiftUI and new features about SwiftUI. This site is more about how I use SwiftUI features in developing RsyncUI. 

# A few words about the code

Even though I am an educated IT person, most of my professional work has been as an IT manager and not a developer. Most of my coding experience is with private projects such as RsyncOSX and RsyncUI. Google is and has been a great resource for research and advice on how to solve specific problems. Reading about other developers code and discussions is always valuable input for me. The MVC pattern and single source of truth are important patterns for both apps. I have also tried to use all Apple Frameworks, utilizing most of the required built-in functions.  And even if there are many lines of code in both apps, I have tried to write as little code as possible. So if you are looking at my code, keep this in mind: my code is only one of many solutions.

# No rigth or wrong

I also believe there is no right or wrong in development. Each developer and team chooses their way to develop based on their skills and what they want to achieve. I know my own code, and I have no issues understanding what my code is doing. And as times go on, in my opinion, I make changes to the code for the better. I also fully understand that other developers might have some issues understanding my code. 

What I also experience is how my own understanding of OO, declarative programming, Swift and SwiftUI is growing. I would never write code today, as I did when I commenced my project in 2015 to learn Swift. But still, I am not a professional programmer, and there are several topics within Swift and SwiftUI that I do not understand. But I am capable of learning; if I spend enough time on a topic, there is a good probability that I will get the idea.

# Why not App Store

One important requirement for macOS apps on Apple App Store is quote Apple: *"To distribute a macOS app through the Mac App Store, you must enable the App Sandbox capability."* There are restrictions what an app can do inside the App Sandbox. Execute `rsync`, obvious, and enable passwordless login by ssh to remote servers are two must have features. The Apple Sandbox causes a few issues to both features when switch on. And I have not yet any success when RsyncUI is set to use default rsync in macOS and only attached discs. But I am inveastigating, so there might be an Apple App Store version in the future.

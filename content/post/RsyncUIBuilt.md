+++
author = "Thomas Evensen"
title = "Development of RsyncUI"
date = "2024-02-28"
tags = ["Development"]
categories = ["Development"]
lastmod = "2024-04-02"
+++
RsyncUI is by no means an advanced or complicated application. It is a pure SwiftUI and Swift based application, and learning about both languages has been and still is fun. And writing about the development is also fun. There are no deeper thoughts about this site. There are many developers out there writing about SwiftUI, discovering new features and how to use them.  This web is **not** a site to show how to use SwiftUI and new features about SwiftUI. This site is about how I use SwiftUI features in developing RsyncUI.  

## JSON files vs SwiftData

A SwiftData version of RsyncUI was also developed but not released. And there is no plan to release it either. It was primarily a project to learn more about SwiftData. There is less code in the SwiftData version of RsyncUI; there is no read and write of JSON data, which requires decoding and encoding data, and no explicit write of all data when data is changed. Using SwiftData takes care of all this. 

I believe SwiftData is great for applications with a lot of data. As far as I have learned from my test project for RsyncUI and SwiftData, using SwiftData is kind of easy to learn and use in your project. It also makes sure your data is consistent when changed. SwiftData takes care of all updates, no explicit read and write as for JSON files.

There are some more info about JSON vs SwiftData on this web. Trye the search function if interessted.

| App      | Storage  | Lines of code | Swift files | Version 1.0 |
| ----------- | ----------- |   ----------- | -------- | -------- |
| RsyncUI  | JSON files |  about 11.6K     | about 146       | 6 May 2021 |
| RsyncUI  | SwiftData |  about 10.2K     | about 122       | February 2024 |

## A few words about the code

Even though I am an educated IT person, most of my professional work has been as an IT manager and not a developer. Most of my coding experience is with private projects such as RsyncOSX and RsyncUI. Google is and has been a great resource for research and advice on how to solve specific problems. Reading about other developers code and discussions is always valuable input for me. The MVC pattern and single source of truth are important patterns for both apps. I have also tried to use all Apple Frameworks, utilizing most of the required built-in functions.  And even if there are many lines of code in both apps, I have tried to write as little code as possible. So if you are looking at my code, keep this in mind: my code is only one of many solutions.

## No rigth or wrong

I also believe there is no right or wrong in development. Each developer and team chooses their way to develop based on their skills and what they want to achieve. I know my own code, and I have no issues understanding what my code is doing. And as times go on, in my opinion, I make changes to the code for the better. I also fully understand that other developers might have some issues understanding my code. 

What I also experience is how my own understanding of OO, declarative programming, Swift and SwiftUI is growing. I would never write code today, as I did when I commenced my project in 2015 to learn Swift. But still, I am not a professional programmer, and there are several topics within Swift and SwiftUI that I do not understand. But I am capable of learning; if I spend enough time on a topic, there is a good probability that I will get the idea.

## Why not App Store

One important requirement for macOS apps on Apple App Store is quote Apple: *"To distribute a macOS app through the Mac App Store, you must enable the App Sandbox capability."* There are restrictions what an app can do inside the App Sandbox. Execute `rsync`, obvious, and enable passwordless login by ssh to remote servers are two must have features. The Apple Sandbox causes a few issues to both features when switch on. And I have not yet any success when RsyncUI is set to use default rsync in macOS and only attached discs. But I am inveastigating, so there might be an Apple App Store version in the future.

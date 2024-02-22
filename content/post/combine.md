+++
author = "Thomas Evensen"
date = "2024-02-22"
title =  "Combine"
tags = ["combine"]
categories = ["combine"]
lastmod = "2024-02-22"
+++
Combine, a *declarative* library by Apple, is utilized in several parts of RsyncUI. Before the new `@Observable` macro was introduced Combine was also a requiered part of the property wrapper `@StateObject` in combination with `ObservableObject`. The new macro `@Observable` is not depended upon Combine and the following are examples of utilizing Combine in RsyncUI:

- [read json ](https://github.com/rsyncOSX/RsyncUI/blob/main/RsyncUI/Model/Storage/ReadConfigurationJSON.swift) configuration file for tasks, only the JSON version of RsyncUI
- [write json](https://github.com/rsyncOSX/RsyncUI/blob/main/RsyncUI/Model/Storage/WriteConfigurationJSON.swift) configuration file for tasks, only the JSON version of RsyncUI
- [observing signals](https://github.com/rsyncOSX/RsyncUI/blob/main/RsyncUI/Model/Process/Main/Async/RsyncProcessAsync.swift) within the process object,
- debouncing input from user, filter strings and other values which are validated before saving

The user input for search or filter is by default a `@State` string variable. The view reacts on the input by every keypress and the filter algorithm is triggered by every keypress. This causes a sluggish user interface, but by debounce input by *a second* causes the filter algorithm only to update the view after the debounce period.

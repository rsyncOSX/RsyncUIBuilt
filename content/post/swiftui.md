+++
author = "Thomas Evensen"
date = "2024-02-20"
title =  "SwiftUI"
tags = ["SwiftUI"]
categories = ["SwiftUI"]
lastmod = "2024-02-20"
+++
SwiftUI is the latest declarative framework developed by Apple for views, controls, and layout structures for user interface. RsyncUI utilizes SwiftUI for the UI. UI components are views, which is a value type `struct` and not a reference type `class`. UI components are added to RsyncUI by code. Every time a property within a SwiftUI view is changed the view is recreated by the runtime. In SwitfUI there are several property wrappers to create bindings to mutable properties.

# Start of RsyncUI

The start of RsyncUI conforms to the [App protocol](https://developer.apple.com/documentation/SwiftUI/App). There is only one entrance point, `@main`, in RsyncUI. The `@main` initialises the app, setup of the menubar, and opens the navigation bar which is the main user start for the app.

# The model

SwiftUI views are *value types*, structs, and not *reference types*, classes, as in Storyboard and Swift. The model is responsible for informing the views when there are changes. The memory footprint of tasks is minimal. Data for tasks is kept in memory during the lifetime of RsyncUI. The memory footprint for logrecords will grow over time as new logs are created and stored. Logrecords are only read from the store when viewing and deleting logs. When data about logs is not used, the data is released from memory to keep the memory as low as possible.

# Property wrapper for objects

With Swift 5.9, Xcode 15 and macOS 14 Apple introduced the `@Observable` macro. On previous macOS versions, the property wrapper `@StateObject` in combination with `ObservableObject` and Combine, was used to create binding to mutable properties. The new macro *greatly simplifies* how to create bindings and there is also quite a boost in performance. The performance part is not that important for RsyncUI, but lesser code is better code.  There is also a new `@Bindable` property wrapper which create bindings to mutable properties of `@Observable` objects. 

All old property wrapper `@StateObject` in combination with `ObservableObject` and Combine are converted to the new macro.

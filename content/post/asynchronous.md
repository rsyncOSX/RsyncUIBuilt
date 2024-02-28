+++
author = "Thomas Evensen"
date = "2024-02-26"
title =  "Asynchronous execution"
tags = ["Asynchronous"]
categories = ["Execution"]
lastmod = "2024-02-26"
+++
Asynchronous execution is a key component of RsyncUI. Every time a `rsync` synchronize and restore task is executed the termination of the task is not known ahead.  When the *termination signal* is observed some actions are required. Some actions are like send a message about a task is completed, execute next synchronize task, stopping a progressview and do some logging. 

There are two methods for asynchronous execution. *Callback functions* or *completion handlers*, which trigger next action when task is completed, and Swift´s `async` and `await` keywords for asynchronous execution. Utilizing `async` and `await` makes the code simpler and cleaner. Swift concurrency is a seperate and huge topic to discuss. I dont have the details and knowlegde to discuss it. There are structured and unstructered concurrency in Swift. Within RsyncUI there is only one asynchronous execution at any time except for concurrency within the Swift and SwiftUI libraries such as GUI updates. 

# The Process object

The `Process` object is where the real work is done. Input to the `Process` are the command to execute and the parameters for the command. The `Process` object utilizes Combine for monitoring *process termination* and *the output* from the command.  There are two versions of the process object:

- [the process object](https://github.com/rsyncOSX/RsyncUI/blob/main/RsyncUI/Model/Process/Main/RsyncProcess.swift)
- [the async process object](https://github.com/rsyncOSX/RsyncUI/blob/main/RsyncUI/Model/Process/Main/Async/RsyncProcessAsync.swift)

The difference between those two objects are minor, the async version marks the function for execution with keyword `async`. Calling the `async` require the `await` keyword. 

RsyncUI is depended to communicate with command line tools within the macOS system. The main command is executing `rsync` with the approriate arguments. But there are also commands executing `rm`, `ssh` and `mkdir`. And the Process object is utilized for all those commands. 

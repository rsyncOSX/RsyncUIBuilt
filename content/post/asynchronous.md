+++
author = "Thomas Evensen"
date = "2024-02-26"
title =  "Asynchronous execution"
tags = ["Asynchronous"]
categories = ["Execution"]
lastmod = "2024-02-26"
+++
Asynchronous execution is a key component of RsyncUI. Every time a `rsync` synchronize and restore tasks are executed the termination of the task is not known ahead.  When the *termination signal* is observed some actions are required. Some actions are like send a message about a task is completed, execute next synchronize task, stopping a progressview and do some logging. 

In Swift there are, as far as I know, only two methods for asynchronous execution. Using *completion handlers* or Swift´s `async` and `await` keywords. Utilizing `async` and `await` makes the code  simpler, easy to read and understand. In RsyncUI it is a `Process` object which is responsible for execution of all tasks. 

# An issues with async and structured concurrency in RsyncUI

One of the  `Process` object is refactored to an `async` function. The idea was to use it in what is called structured concurrency and not use *completion handlers*. But I soon discovered I still had to use *completion handlers*.  

The async execution is a class importing the `Process` library and the function `executeProcess`, which is an async function, kicks of the real task. In structured concurrency the call `await process.executeProcess()` should  cause RsyncUI to wait until the task is completed.  After the completion of the async task RsyncUI should continue the execution.

```bash
func execute(config: SynchronizeConfiguration, dryrun: Bool) async {
        ...
        let process = RsyncProcessAsync(arguments: arguments,
                                        config: config,
                                        processtermination: processtermination)
        Task {
            await process.executeProcess()
        }
        // After task is completed, continue here and
        // execute any task after the async function is completed
        somefunctionafterasynctaskiscompleted()
    }
```
But that does not work when Combine is used to monitor when the external task is completed. 
```bash
NotificationCenter.default.publisher(
            for: Process.didTerminateNotification)
            .debounce(for: .milliseconds(500), scheduler: globalMainQueue)
            .sink { _ in
                // Process termination ...
                ...
                // Release Combine subscribers
                self.subscriptons.removeAll()
            }.store(in: &subscriptons)
```
After the call `await process.executeProcess()` RsyncUI just continue execution. 

The only way to get structured concurrency to work as expected is to write `task.waitUntilExit()` after the `task.run()`. This will enable structured concurrency to work.

```bash
do {
    try task.run()
      } catch let e {
    // catch error
    }
task.waitUntilExit()
```
But I believe it is safer and more effective to use Combine to listen for a termination signal. And that is why using completion handler is requiered also when Swift´s `async` and `await` keywords are used in RsyncUI. 

# The Process objects

There are two versions of the process object most for learning more about `async` and `await`. The difference between those two objects are minor, the async version marks the function for execution with keyword `async`. Calling the `async` require the `await` keyword. 

- [the process object](https://github.com/rsyncOSX/RsyncUI/blob/main/RsyncUI/Model/Process/Main/RsyncProcess.swift)
- [the async process object](https://github.com/rsyncOSX/RsyncUI/blob/main/RsyncUI/Model/Process/Main/Async/RsyncProcessAsync.swift)



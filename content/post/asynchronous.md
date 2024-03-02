+++
author = "Thomas Evensen"
date = "2024-02-26"
title =  "Asynchronous execution"
tags = ["Asynchronous"]
categories = ["Execution"]
lastmod = "2024-02-26"
+++
Asynchronous execution is a key component of RsyncUI. Every time a `rsync` synchronize and restore task is executed the termination of the task is not known ahead.  When the *termination signal* is observed some actions are required. Some actions are like send a message about a task is completed, execute next synchronize task, stopping a progressview and do some logging. 

In Swift there are, as far as I know, only two methods for asynchronous execution. Using *completion handlers* or Swift´s `async` and `await` keywords. Utilizing `async` and `await` makes the code  simpler, easy to read and understand. In RsyncUI it is a `Process` object which is responsible for execution of all tasks. And there are two versions of the class to execute tasks. One of the `Process` objects in RsyncUI is a `async` function. 

# Some issues with async in RsyncUI

When I refactored the `Process` object to an `async` function, the idea was to use it in what is called structured concurrency and not use *completion handlers*. But I soon discovered I still had to use *completion handlers*.  The issue is as described below. 

The execute function is an async task, the process is a class a class importing the `Process` library and within the class the function `executeProcess` is an async function. In structured concurrency a call `await process.executeProcess()` should  cause RsyncUI to wait until the the async task was completed. 

```bash
func execute(config: SynchronizeConfiguration, dryrun: Bool) async {
        let arguments = ArgumentsSynchronize(config: config).argumentssynchronize(dryRun: dryrun, forDisplay: false)
        rsyncoutput = ObservableRsyncOutput()
        showprogressview = true
        let process = RsyncProcessAsync(arguments: arguments,
                                        config: config,
                                        processtermination: processtermination)
        Task {
            await process.executeProcess()
        }
    }
```
And after the call do what is requiered after the call like this:

```bash
Task {
            await process.executeProcess()
        }
  // Continue and execute any task after the async function is completed
```
Combine is used to monitor when the external task is completed. 
```bash
NotificationCenter.default.publisher(
            for: Process.didTerminateNotification)
            .debounce(for: .milliseconds(500), scheduler: globalMainQueue)
            .sink { _ in
                // Process termination and Log to file
                self.processtermination(self.outputprocess?.getOutput(), self.config?.hiddenID)
                _ = Logfile(TrimTwo(self.outputprocess?.getOutput() ?? []).trimmeddata, error: false)
                // Release Combine subscribers
                self.subscriptons.removeAll()
            }.store(in: &subscriptons)
```
Using Combine like this does not work as structured concurrency. After the call `await process.executeProcess()` RsyncUI just continue execution. The only way to get structured concurrency work as expected is to write `task.waitUntilExit()` after the `task.run()`. This will enable structured concurrency. 

```bash
do {
            try task.run()
        } catch let e {
            let error = e
            propogateerror(error: error)
        }
   task.waitUntilExit()
```

But I believe it is safer and more effective to use Combine to listen for a termination signal. And that is why using completion handler is requiered also when Swift´s `async` and `await` keywords are used in RsyncUI. 

# The Process objects

There are two versions of the process object. There are two versions most for learning about `async` and `await`. 

- [the process object](https://github.com/rsyncOSX/RsyncUI/blob/main/RsyncUI/Model/Process/Main/RsyncProcess.swift)
- [the async process object](https://github.com/rsyncOSX/RsyncUI/blob/main/RsyncUI/Model/Process/Main/Async/RsyncProcessAsync.swift)

The difference between those two objects are minor, the async version marks the function for execution with keyword `async`. Calling the `async` require the `await` keyword. 

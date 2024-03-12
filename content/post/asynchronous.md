+++
author = "Thomas Evensen"
date = "2024-02-26"
title =  "Asynchronous execution"
tags = ["Asynchronous"]
categories = ["Execution"]
lastmod = "2024-02-26"
+++
Asynchronous execution is a key feature of RsyncUI. Every time a `rsync` synchronize and restore tasks are executed the termination of the task is not known ahead.  When the *termination signal* is observed some actions are required. Some actions are like send a message about a task is completed, execute next synchronize task, stopping a progressview and do some logging.  

In Swift there are, as far as I know, two methods for asynchronous execution. Using *completion handlers* or Swift´s `async` and `await` keywords which is part of the Swift concurrency model. Utilizing `async` and `await` makes the code  simpler to read and understand. This is easy to see if there are like nested completion handlers. Nested completion handlers might be difficult to follow. 

In RsyncUI it is a `Process` object which is responsible for execution of all tasks. It seems difficult to make a Process task async. One method does work, `task.waitUntilExit()`, but as far as I understand it is not 100%. That is why *completion handlers* is used when asynchronous execution is completed. Combine is used to montior the signal for termination:

```bash
// Combine, subscribe to Process.didTerminateNotification
        NotificationCenter.default.publisher(
            for: Process.didTerminateNotification)
            .debounce(for: .milliseconds(500), scheduler: DispatchQueue.main)
            .sink { [self] _ in
                self.processtermination(self.outputprocess?.getOutput(), self.config?.hiddenID)
                // Logg to file
                _ = Logfile(TrimTwo(outputprocess?.getOutput() ?? []).trimmeddata, error: false)
                // Release Combine subscribers
                subscriptons.removeAll()
            }.store(in: &subscriptons)
```
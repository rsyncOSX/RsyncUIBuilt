+++
author = "Thomas Evensen"
date = "2024-02-26"
title =  "Asynchronous execution"
tags = ["Asynchronous"]
categories = ["Execution"]
lastmod = "2024-02-26"
+++
Concurrency and asynchronous execution are key components of Swift. The latest version of Swift makes it even easier to write asynchronous code using Swift´s `async` and `await` keywords. The concurrency model of Swift is complex, and it requires, at least for me, time and study to understand the basics. Apart from GUI updates, which SwiftUI takes care of, there is no concurrency in RsyncUI. But there is asynchronous execution, but only one at a time.  Every time a `rsync` synchronize and restore tasks, the termination of the task is not known ahead.  When the *termination signal* is observed, some actions are required. Some actions are like sending a message about a task being completed, executing the next synchronize task, stopping a progressview and doing some logging.  

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
In theory, there could be concurrency and asynchronous execution in RsyncUI. If a user of RsyncUI has two or more tasks added, RsyncUI could execute concurrent tasks. But it does not; it executes one task at a time. When one task, which is executed asynchronously, is completed, it kicks off the next task. But every `rsync` synchronize and restore task is asynchronous. When a task is executed, the GUI updates a progressbar and waits until the task is completed. Combine is used to listen for termination of the external process. When the termination signal is discovered, a completion handler is activated, which causes RsyncUI to evaluate what to do next.
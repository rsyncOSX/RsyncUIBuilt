+++
author = "Thomas Evensen"
date = "2024-02-26"
title =  "Asynchronous execution"
tags = ["Asynchronous"]
categories = ["Execution"]
lastmod = "2024-02-26"
+++
Asynchronous execution is a key component of RsyncUI. Every time a `rsync` synchronize and restore tasks are executed the termination of the task is not known ahead.  When the *termination signal* is observed some actions are required. Some actions are like send a message about a task is completed, execute next synchronize task, stopping a progressview and do some logging.  In Swift there are, as far as I know, only two methods for asynchronous execution. Using *completion handlers* or Swift´s `async` and `await` keywords. Utilizing `async` and `await` makes the code  simpler, easy to read and understand. In RsyncUI it is a `Process` object which is responsible for execution of all tasks. 

There is only a few `async` and `await` tasks in RsyncUI. All `Process` objects trigger *completion handlers* when asynchronous execution is completed.

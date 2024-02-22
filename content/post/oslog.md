+++
author = "Thomas Evensen"
date = "2024-02-22"
title =  "Application logging"
tags = ["OSLog"]
categories = ["OSLog"]
lastmod = "2024-02-22"
+++

Included in Swift 5 there is a unified logging feature `OSLog`. There are several methods for logging and investigate what the application is up to. By using OSLog there is no need for print statements to follow execution. All logging is by utilizing OSLog displayed as part of Xcode. The `Process` objects are where all the real work is done. OSLog is  included in all objects which perform work and it is very easy to check which commands RsyncUI are executing.

OSLog info in Xcode. The logging is displaying commands and arguments as shown below. It makes it convenient to check that RsyncUI is executing the correct command.

{{< figure src="/images/Xcode/Logger.png" alt="" position="center" style="border-radius: 8px;" >}}

And the OSLogs might be read by using the Console app. Be sure to set the Action in Console app menu to `Include Info Messages` and enter `no.blogspot.RsyncUI` as subsystem within the search field.

{{< figure src="/images/Xcode/console.png" alt="" position="center" style="border-radius: 8px;" >}}

+++
author = "Thomas Evensen"
date = "2024-02-20"
title =  "Execution"
tags = ["Execution"]
categories = ["Execution"]
lastmod = "2024-02-20"
+++
There are several ways to execute tasks:

- two double clicks on a task, first is an estimate run and second is the real run
- select task(s) and either estimate first or execute without estimation
- estimate first all tasks and then execute or execute all tasks without estimation

The following is a brief overview of *estimate first and then execute tasks*. The difference between an estimation run and a real run is; estimation run is a `--dryn-run` and there is no logging. An estimation run is commenced by selecting the *wand and stars* icon or shortcut `⌘E` from the main view. The navigation path for estimate all view is pushed onto the view stack and a progress informes which tasks are estimated. For each task the result of the estimation run is saved. After estimation RsyncUI marks tasks where there is data to synchronize. 

The user then select synchronize tasks and the navigation path for executing tasks including progress of each is pushed onto the view stack. From this view the reference of the class with data from estimation is passed onto the class which executes the tasks. The id for each task is pushed onto a stack and the class executing tasks is not released until the stack is emtpy and a process termination signal is observed from the last task. The progess of synchronizing is communicated to the progress view by an *escaping closure*, `@escaping (Int) -> Void)`. 

The execution of tasks is equal in both versions, but the update of data is different. That is reflected by the input parameters to `func executemultipleestimatedtasks()` which kicks of synchronization of all estimated tasks and data to be synchronized. 

## JSON

When the execution of tasks are completed the updated data is writen to JSON files from within the `func executemultipleestimatedtasks()`. The updated data is feed back to the `@Bindable` object by the `func updateconfigurations(_ configurations: [SynchronizeConfiguration])` which also causes the UI to be updated by SwiftUI.

```bash
func executemultipleestimatedtasks() {
        var uuids: Set<SynchronizeConfiguration.ID>?
        if selecteduuids.count > 0 {
            uuids = selecteduuids
        } else if executeprogressdetails.estimatedlist?.count ?? 0 > 0 {
            let uuidcount = executeprogressdetails.estimatedlist?.compactMap { $0.id }
            uuids = Set<SynchronizeConfiguration.ID>()
            for i in 0 ..< (uuidcount?.count ?? 0) where
                executeprogressdetails.estimatedlist?[i].datatosynchronize == true
            {
                uuids?.insert(uuidcount?[i] ?? UUID())
            }
        }
        guard (uuids?.count ?? 0) > 0 else { return }
        if let uuids = uuids {
            multipletaskstate.updatestate(state: .execute)
            ExecuteMultipleTasks(uuids: uuids,
                                 profile: rsyncUIdata.profile,
                                 rsyncuiconfigurations: rsyncUIdata,
                                 multipletaskstateDelegate: multipletaskstate,
                                 executeprogressdetailsDelegate: executeprogressdetails,
                                 filehandler: filehandler,
                                 updateconfigurations: updateconfigurations)
        }
    }

    func updateconfigurations(_ configurations: [SynchronizeConfiguration]) {
        rsyncUIdata.configurations = configurations
    }
    
     func filehandler(count: Int) {
        progress = Double(count)
    }
```
## SwiftData

All changes of data, e.g. only the changes, are braught back to the calling view by the two functions `func updatedates(_ configrecords: [Typelogdata])` and  `func updatelogrecords(_ logrecords: [Typelogdata])`. The updates are feed back to the calling view and data is updated on the fly. The update causes the views to update as well by SwiftData.

```bash
func executemultipleestimatedtasks() {
        var uuids: Set<SynchronizeConfiguration.ID>?
        if selecteduuids.count > 0 {
            uuids = selecteduuids
        } else if executeprogressdetails.estimatedlist?.count ?? 0 > 0 {
            let uuidcount = executeprogressdetails.estimatedlist?.compactMap { $0.id }
            uuids = Set<SynchronizeConfiguration.ID>()
            for i in 0 ..< (uuidcount?.count ?? 0) where
                executeprogressdetails.estimatedlist?[i].datatosynchronize == true
            {
                uuids?.insert(uuidcount?[i] ?? UUID())
            }
        }
        guard (uuids?.count ?? 0) > 0 else { return }
        if let uuids = uuids {
            multipletaskstate.updatestate(state: .execute)
            ExecuteMultipleTasks(uuids: uuids,
                                 configurations: configurations,
                                 multipletaskstateDelegate: multipletaskstate,
                                 executeprogressdetailsDelegate: executeprogressdetails,
                                 filehandler: filehandler,
                                 updatedates: updatedates,
                                 updatelogrecords: updatelogrecords)
        }
    }

    func updatedates(_ configrecords: [Typelogdata]) {
        for i in 0 ..< configrecords.count {
            let hiddenID = configrecords[i].0
            let date = configrecords[i].1
            if let index = configurations.firstIndex(where: { $0.hiddenID == hiddenID }) {
                // Caution, snapshotnum already increased before logrecord
                if configurations[index].task == SharedReference.shared.snapshot {
                    if let num = configurations[index].snapshotnum {
                        configurations[index].snapshotnum = num + 1
                    }
                }
                configurations[index].dateRun = date
            }
        }
    }

    func updatelogrecords(_ logrecords: [Typelogdata]) {
        if SharedReference.shared.detailedlogging {
            for i in 0 ..< logrecords.count {
                let hiddenID = logrecords[i].0
                let stats = logrecords[i].1
                let currendate = Date()
                let date = currendate.en_us_string_from_date()
                if let index = configurations.firstIndex(where: { $0.hiddenID == hiddenID }) {
                    var resultannotaded: String?
                    if configurations[index].task == SharedReference.shared.snapshot {
                        if let snapshotnum = configurations[index].snapshotnum {
                            resultannotaded = "(" + String(snapshotnum - 1) + ") " + stats
                        } else {
                            resultannotaded = "(" + "1" + ") " + stats
                        }
                    } else {
                        resultannotaded = stats
                    }
                    var inserted: Bool = addlogexisting(hiddenID,
                                                        resultannotaded ?? "",
                                                        date)
                    // Record does not exist, create new LogRecord (not inserted)
                    if inserted == false {
                        inserted = addlognew(hiddenID, resultannotaded ?? "", date)
                    }
                }
            }
        }
    }
    
     func filehandler(count: Int) {
        progress = Double(count)
    }
```
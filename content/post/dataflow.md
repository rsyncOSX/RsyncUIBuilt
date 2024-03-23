+++
author = "Thomas Evensen"
date = "2024-02-26"
title =  "Dataflow in RsyncUI"
tags = ["SwiftData","JSON"]
categories = ["dataflow"]
lastmod ="2024-02-26"
+++
The flow and handling of data are slightly different in RsyncUI using JSON file vs SwiftData. From Apple Developement Documentations quote Apple: *"Combining Core Data’s proven persistence technology and Swift’s modern concurrency features, SwiftData enables you to add persistence to your app quickly, with minimal code and no external dependencies."*.   Sorting and filter data is also somewhat different using JSON or SwiftData as storage. There are *three files* saved to storage: tasks, log records and user settings.

# JSON files as Storage

All files are saved as JSON files and `Combine` is utilized to read and save data. JSON files are *encoded* before a write operation and *decoded* when read from storage. The encode and decode is requiered to represent JSON data as internal data within RsyncUI. Both reading data and saving data to JSON-files are explicit actions in code.  When RsyncUI starts it reads *user configuration* and *data about tasks for the default profile*. 

```bash
 var rsyncUIdata: RsyncUIconfigurations {
        return RsyncUIconfigurations(selectedprofile)
    }
```
All views which require data about tasks get data by a mutable property wrapper `@Bindable var rsyncUIdata: RsyncUIconfigurations`.  Within the  `@Observable final class RsyncUIconfigurations` there is a observed variable holding the data structure about tasks. 
```bash
import Observation
import SwiftUI

@Observable
final class RsyncUIconfigurations {
    var configurations: [SynchronizeConfiguration]?
    var profile: String?

    init(_ profile: String?) {
        self.profile = profile
        if profile == SharedReference.shared.defaultprofile || profile == nil {
            configurations = ReadConfigurationJSON(nil).configurations
        } else {
            configurations = ReadConfigurationJSON(profile).configurations
        }
    }
}
```
When tasks are updated, like timestamp last run, the class which executes the tasks does two jobs when executing tasks is completed. The internal datastructure is always updated. The first job is to send the changed updated datastructure up to the view by an *escaping closure*, `@escaping ([SynchronizeConfiguration]) -> Void)`. The view updates the observed variable holding the data structure about tasks and the SwiftUI runtime updates the views. The second job is to write the updates the permanent storage.

When there are changes on data the complete datastructure is written to file, like if there are 2000 logrecords and adding a new log record causes 2001 records to be written to the JSON-file. Data for the views are made avaliable by a `@Bindable` property and an `@Observable` object.  

## Sorting log records (JSON)

Filter and sort log records in JSON-file version is different. The function get all log records, combine all arrays of records by `flatmap` and uses a standard filter function.

```bash
@MainActor
    func updatelogsbyfilter() async {
        guard debouncefilterstring != "" else { return }
        if let logrecords = rsyncUIlogrecords.logrecords {
            if hiddenID == -1 {
                var merged = [Log]()
                for i in 0 ..< logrecords.count {
                    merged += [logrecords[i].logrecords ?? []].flatMap { $0 }
                }
                let records = merged.sorted(using: [KeyPathComparator(\Log.date, order: .reverse)])
                logs = records.filter { ($0.dateExecuted?.en_us_date_from_string().long_localized_string_from_date().contains(debouncefilterstring)) ?? false || ($0.resultExecuted?.contains(debouncefilterstring) ?? false)
                }
            } else {
                if let index = logrecords.firstIndex(where: { $0.hiddenID == hiddenID }) {
                    let records = (logrecords[index].logrecords ?? []).sorted(using: [KeyPathComparator(\Log.date, order: .reverse)])
                    logs = records.filter { ($0.dateExecuted?.en_us_date_from_string().long_localized_string_from_date().contains(debouncefilterstring)) ?? false || ($0.resultExecuted?.contains(debouncefilterstring) ?? false)
                    }
                }
            }
        }
    }
```

# SwiftData as Storage

[RsyncUISwiftData](https://github.com/rsyncOSX/RsyncUISwiftData) is the repository for the SwiftData version of RsyncUI.  One of the main differences compared to the JSON-file version of RsyncUI are that read and updates of data is taken care of by SwiftData. Every time a view needs data it is only requiered to use the `@Environment` to get the model and a `@Query` property wrapper to get the actual data. 

```bash
struct Sidebar: View {
    @Environment(\.modelContext) private var modelContext
    @Query private var configurations: [SynchronizeConfiguration]
```

There are two database operations used, insert and delete. Updates of data is by selecting the record and update, SwiftData takes care of updating the database and informes the views to update. The datastructure in RsyncUI is a struct, but SwiftData requiere `@Model final class`, that is the structs holding the datamodels are modified to class:

```bash
import Foundation
import SwiftData

@Model
final class SynchronizeConfiguration: Identifiable {
    var id = UUID()
    var hiddenID: Int
    var task: String
    var localCatalog: String
    var offsiteCatalog: String
    var parameter1: String
    var parameter2: String
    var parameter3: String
    var parameter4: String
    var parameter5: String
    var parameter6: String
    var backupID: String
    var dateRun: String?
    // parameters choosed by user
    var parameter8: String?
    var parameter9: String?
    var parameter10: String?
    var parameter11: String?
    var parameter12: String?
    var parameter13: String?
    var parameter14: String?
    var profile: String = "Default profile"
   }
```
For every task added a `LogRecords` is created the first time the task is executed. Every log for this task is stored in the `@Relationship(deleteRule: .cascade, inverse: \Log.logrecord) var records: [Log]?` variable. 

```bash
@Model
final class LogRecords: Identifiable {
    var id = UUID()
    @Attribute(.unique) var hiddenID: Int
    var dateStart: String
    @Relationship(deleteRule: .cascade, inverse: \Log.logrecord) var records: [Log]?

    init(id: UUID = UUID(), hiddenID: Int, dateStart: String, records: [Log]? = nil) {
        self.id = id
        self.hiddenID = hiddenID
        self.dateStart = dateStart
        self.records = records
    }
}

@Model
final class Log: Identifiable {
    var id = UUID()
    var dateExecuted: String
    var resultExecuted: String
    var logrecord: LogRecords?
    @Relationship(inverse: \LogRecords.records)

    init(id: UUID = UUID(), dateExecuted: String, resultExecuted: String, logrecord: LogRecords? = nil) {
        self.id = id
        self.dateExecuted = dateExecuted
        self.resultExecuted = resultExecuted
        self.logrecord = logrecord
    }
}
```
The datamodel is initialized when the app is starting, if first time the datastore is automatically created . 

```bash
 var sharedModelContainer: ModelContainer = {
        let schema = Schema([SynchronizeConfiguration.self,
                             UserConfiguration.self,
                             LogRecords.self])

        let storeURL = URL.documentsDirectory.appending(path: "rsyncui.sqlite")
        let configuration = ModelConfiguration(schema: schema, url: storeURL)

        do {
            return try ModelContainer(for: schema, configurations: [configuration])
        } catch {
            fatalError("Could not create ModelContainer: \(error)")
        }
    }()
```
The datamodel is made avaliable for all the views by:
```bash
Window("RsyncUI", id: "main") {
            RsyncUIView()
                .frame(minWidth: 1300, minHeight: 510)
        }
        .modelContainer(sharedModelContainer)
```

You can create relations and much more in SwiftData, but for RsyncUI there is no need for an advanced datamodel. When data is changed, like update timestamp or create a log, the changes are propogated up to the view which initiated the change. The data is updated to the database by SwiftData and SwiftUI updates the views on the fly.  

## Sorting log records (SwiftData)

Showing logcrecords by task, sorted by Date is done by getting the data by a `@Query` property using a Sortdescriptor. The View is *initialized* from the parent view sending in the UUID for the selected task, and a Predicate filter shows logs by UUID and a filter by date.

```bash
import SwiftData
import SwiftUI

struct LogRecordsTableByUUIDView: View {
    @Environment(\.modelContext) var modelContext
    @Query(sort: [SortDescriptor(\Log.dateExecuted, order: .reverse)])
    var records: [Log]

    @State private var selectedloguuids = Set<Log.ID>()
    // Alert for delete
    @State private var showAlertfordelete = false
    // Number of logs
    @Binding var number: Int

    var body: some View {
        Table(records, selection: $selectedloguuids) {
            TableColumn("Date") { data in
                let date = data.dateExecuted
                Text(date.en_us_date_from_string().localized_string_from_date())
            }

            TableColumn("Result") { data in
                Text(data.resultExecuted)
            }
        }
        .onAppear {
            number = records.count
        }
        .onDeleteCommand {
            showAlertfordelete = true
        }
        .overlay { if records.count == 0 {
            ContentUnavailableView {
                Label("There are no logs by this filter", systemImage: "doc.richtext.fill")
            } description: {
                Text("Try to search for other filter in Date or Result")
            }
        }
        }
        .sheet(isPresented: $showAlertfordelete) {
            DeleteLogsView(selectedloguuids: $selectedloguuids)
        }
    }

    init(sort: SortDescriptor<Log>, searchString: String, uuid: UUID, _ number: Binding<Int>) {
        _records = Query(filter: #Predicate {
            if searchString.isEmpty {
                return true && $0.logrecord?.id == uuid
            } else {
                return $0.dateExecuted.localizedStandardContains(searchString) && $0.logrecord?.id == uuid
            }
        }, sort: [sort])

        _number = number
    }
}
````



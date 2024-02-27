+++
author = "Thomas Evensen"
date = "2024-02-20"
title =  "Navigation"
tags = ["NavigationSplitView","NavigationStack"]
categories = ["Navigation"]
lastmod = "2024-02-20"
+++
The main navigation, when RsyncUI starts, is by a `NavigationSplitView`: *A view that presents views in two or three columns, where selections in leading columns control presentations in subsequent columns.* RsyncUI utilizes two columns. Left column for main functions and the right column for details about each main function.  The details part is computed every time the user select a function like the Synchronize view, Tasks view and so on. The navigation within usersetting is also by `NavigationSplitView`. The function `SidebarRow(sidebaritem: selectedview) -> View` returns icons for the sidebar functions. 

```bash
enum Sidebaritems: String, Identifiable, CaseIterable {
    case synchronize, tasks, rsync_parameters, snapshots, log_listings, restore
    var id: String { rawValue }
}

struct Sidebar: View {
    ....
    @State private var selectedview: Sidebaritems = .synchronize
    ...

    var body: some View {
        NavigationSplitView {
            Divider()

            List(Sidebaritems.allCases, selection: $selectedview) { selectedview in
                NavigationLink(value: selectedview) {
                    SidebarRow(sidebaritem: selectedview)
                }
            }
        } detail: {
            selectView(selectedview)
        }
    }

    @ViewBuilder
    func selectView(_ view: Sidebaritems) -> some View {
        switch view {
        case .tasks:
            AddTaskView(rsyncUIdata: rsyncUIdata,
                        selectedprofile: $selectedprofile,
                        profilenames: profilenames)
        case .log_listings:
            if let configurations = rsyncUIdata.configurations {
                SidebarLogsView(configurations: configurations,
                                profile: rsyncUIdata.profile)
            }

        case .rsync_parameters:
            RsyncParametersView(rsyncUIdata: rsyncUIdata)
        case .restore:
            NavigationStack {
                if let configurations = rsyncUIdata.configurations {
                    RestoreTableView(profile: $rsyncUIdata.profile,
                                     configurations: configurations)
                }
            }
        case .snapshots:
            SnapshotsView(rsyncUIdata: rsyncUIdata)
        case .synchronize:
            SidebarTasksView(rsyncUIdata: rsyncUIdata, 
                                    selecteduuids: $selecteduuids)
        }
    }
}
```

Navigation within **all** view is by `NavigationStack`: "*A view that displays a root view and enables you to present additional views over the root view*".  One root view is the tasks view, and the all other views like estimating details, execution of tasks and so on will be presented ontop of the root view. RsyncUI utilizes two APIs of  `NavigationStack`:

The additional view is one view only and it is presented when `$somestate` becomes true.

```bash
@State private var somestate: Bool = false

var body: some View {
 	NavigationStack {
 		...
 		.navigationDestination(isPresented: $somestate) {
                SomeView()
            }
        }
    }
```

The views ontop of the root view are computed depended upon what the user decides to do. Like with the main tasks view, selecting estimate all, push the enum which represent start estimate onto the path and the `NavigationStack` loads that view ontop of the root view.

```bash
@State var path: [Tasks] = []
 
var body: some View {
	NavigationStack(path: $path) {
		...
		.navigationDestination(for: Tasks.self) { which in
                    makeView(view: which.task)
            }
        }
        
    @ViewBuilder
    func makeView(view: DestinationView) -> some View {
        switch view {
        case .executestimatedview:
            ExecuteEstimatedTasksView(selecteduuids: $selecteduuids,
                                      reload: $reload,
                                      path: $path)
                .environmentObject(executeprogressdetails)
        case .executenoestimatetasksview:
           ...
        case .estimatedview:
           ...
        }
    }
}
 
 enum DestinationView: String, Identifiable {
    case executestimatedview, executenoestimatetasksview,
         estimatedview, firsttime, dryrunonetask, alltasksview,
         dryrunonetaskalreadyestimated, viewlogfile
    var id: String { rawValue }
}

struct Tasks: Hashable, Identifiable {
    let id = UUID()
    var task: DestinationView
}
```

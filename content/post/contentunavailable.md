+++
author = "Thomas Evensen"
date = "2024-02-22"
title =  "ContentUnavailableView"
tags = ["content"]
categories = ["content"]
lastmod = "2024-02-22"
+++
In macOS 14 and SwiftUI there is a new view `ContentUnavailableView` to notify the user when there is nothing to show. There are several options for informing and within RsyncUI the view is utlized in combination with filter search. One example is search for records in snapshot:

```bash
if logrecords.count == 0,
           selectedconfig != nil,
           selectedconfig?.task == SharedReference.shared.snapshot,
           snapshotdata.snapshotlist == false
        {
            ContentUnavailableView {
                Label("There are no snapshot records by this search string in Date or Tag",
                      systemImage: "doc.richtext.fill")
            } description: {
                Text("Change search string to filter records")
            }
        }
```
{{< figure src="/images/Xcode/contentavailable.png" alt="" position="center" style="border-radius: 8px;" >}}
After filter no records:
{{< figure src="/images/Xcode/contentunavailable.png" alt="" position="center" style="border-radius: 8px;" >}}

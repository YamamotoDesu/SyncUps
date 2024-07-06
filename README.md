## IdentifiedCollections
Before:

```swift
import Foundation
import SwiftUI


struct SyncUp: Equatable, Identifiable, Codable {
  let id: UUID
  var attendees: [Attendee] = []
  var duration: Duration = .seconds(60 * 5)
  var meetings: [Meeting] = []
  var theme: Theme = .bubblegum
  var title = ""

```

While these models would work well enough in our SyncUps application, we can do better. First it is not ideal to hold onto plain arrays in our domain. This leads us to a situation of referring to attendees and meetings by their positional index in the array, rather than their inherent ID, as given by the Identifiable protocol. This becomes especially problematic when performing asynchronous effects in which the position of a value can change while the effect is being performed, making it possible to refer to the wrong value, leading to bugs, or a nonexistent value, leading to crashes.

https://arc.net/l/quote/jqedqnmo

After:

```swift
import Foundation
import IdentifiedCollections
import SwiftUI

struct SyncUp: Equatable, Identifiable, Codable {
  let id: UUID
  var attendees: IdentifiedArrayOf<Attendee> = []
  var duration: Duration = .seconds(60 * 5)
  var meetings: IdentifiedArrayOf<Meeting> = []
  var theme: Theme = .bubblegum
  var title = ""
```

## Naming rules

> [!IMPORTANT]
> As we saw in Meet the Composable Architecture, we prefer to name our actions after exactly what happens in the view rather than what logic we want to execute. So we prefer addSyncUpButtonTapped over something like showAddSheet.

https://arc.net/l/quote/brhhamqp

```swift
@Reducer
struct SyncUpsList {
    @ObservableState
    struct State: Equatable {
        var syncUps: IdentifiedArrayOf<SyncUp> = []
    }
    enum Action {
      case addSyncUpButtonTapped
      case onDelete(IndexSet)
      case syncUpTapped(id: SyncUp.ID)
    }
}
```

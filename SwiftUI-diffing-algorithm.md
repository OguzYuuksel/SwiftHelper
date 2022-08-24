
#### Primitive Views
Primitive views are not defined in terms of other views. (`Text`, `Button`, `Color`, `Shape`, `Image`, `List`, `VStack`, `HStack`)
- **Unary views**: Views with a single displayable. (`Text`, `Button`, `Color`, `Shape`, `Image`)
- **Structural views**: Views that take zero or more other views, and combines them into a view with some subset of their displayables. 
  (`ForEach`, `EmptyView`, and the views used by `ViewBuilder`, such as `TupleView` and `_ConditionalView`.
- **Container views**: Views that take the displayables of another view and manage them by deciding 
  whether they should be displayed and how they should be laid out. (`List`, `VStack`, `HStack`, `LazyVStack`)
- **Modifiers**: Views that take one other view, and change the layout or look of all of its displayables individually. 
  (the views that modifiers such as `.border`, `.padding`, `.frame` generate, which are of type ModifiedContent.)

#### Composed Views
As clients of the SwiftUI framework, we can only create composed views. 
Composed views can have state, and can be seen as a function of this state to another view. 
Composed views use primitive views in their body. Primitive views are the building blocks of any type of view.
(`ContentView`)




#### Lazyness 
`List` is one of the lazy views in SwiftUI

```
import SwiftUI
import PlaygroundSupport

var unLazyViewCallCount = 0
var lazyViewCallCount = 0
let totalItemCount = 20

struct ContentView: View {

    var body: some View {
        HStack {
            LazyListExample()
            UnLazyListExample()
        }
    }
}

struct LazyListExample: View {
    var body: some View {
        List(0...totalItemCount, id: \.self) { index in
            contentView(index: index)
                .onAppear {
                    print("Lazy Item count: ", index + 1)
                }
        }
    }
    
    @ViewBuilder
    private func contentView(index: Int) -> some View {
        let _ = { lazyViewCallCount += 1 }()
        VStack {
            Text("LazyText \(index)")
            Circle()
                .foregroundColor(.red)
                .frame(width: 100, height: 100, alignment: .center)
        }
    }
}

struct UnLazyListExample: View {
    private let conditions: [Bool] = Array(repeating: true, count: totalItemCount)
    
    var body: some View {
        List(conditions.indices, id: \.self) { index in
            conditionalView(if: conditions[index])
                .onAppear {
                    print("UnLazyItem count: ", index + 1)
                }
        }
    }
    
    @ViewBuilder
    private func conditionalView(if condition: Bool) -> some View {
        let _ = { unLazyViewCallCount += 1 }()
        if condition {
            Text("condition: true")
        } else {
            EmptyView()
        }
    }
}

PlaygroundPage.current.setLiveView(ContentView())

print("Type of ContentView.body: ", type(of: ContentView().body))
print("Type of LazyListExample.body: ", type(of: LazyListExample().body))
print("Type of UnLazyListExample.body: ", type(of: UnLazyListExample().body))
print("Total item count in ListView", totalItemCount)
print("_ConditionalView & OptionalView(unlazy) called \(unLazyViewCallCount) times.")
print("lazy called \(lazyViewCallCount) times.")
```

#### Sources
https://rensbr.eu/blog/swiftui-diffing/

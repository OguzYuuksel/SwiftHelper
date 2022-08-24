
#### Primitive Views
Primitive views are not defined in terms of other views.
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


#### [SwiftUI Diffing](https://rensbr.eu/blog/swiftui-diffing/)

#### Lazyness 
`List` is one of the lazy views in SwiftUI.

`List` needs to know the size of a composed view without evaluating it for lazyness, but can it know?
Turns out, in many cases we can, just by knowing the type of the body of a composed view. For example, the size of a `TupleView<(Text, Text, Button)>` is always 3, no matter the particular instance. So a composed view where the body is a `TupleView<(Text, Text, Button)>` also always has size 3.

But you can't always know the size of a view just by its type. A `_ConditionalView<EmptyView, Text>` can have sizes 0 or 1. If a composed view has a body of this type, and it is used in a lazy stack or a list, we still have to evaluate it to determine its size.

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

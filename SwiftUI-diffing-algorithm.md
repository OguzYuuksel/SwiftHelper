
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





#### Sources
https://rensbr.eu/blog/swiftui-diffing/

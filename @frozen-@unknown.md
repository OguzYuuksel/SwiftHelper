# **@frozen & @unknown**
Custom Enums that you create during developing are `@frozen`, but standard Swift library Enums may be `@unfrozen` that means Apple may add cases in the future. Such as, adding new text font cases to the SwiftUI Font Enums in the future, when you upgrade your project to the new SwiftUI version, you need to be prepared to cover conditions for these new cases.
```swift
switch sizeClass { // SizeClass has total of 3 cases for now, but it is @unfrozen Enum.
case .unspecified:
   // ...
case .compact:
   // ...
case .regular:
   // ...
@unknown default: // Covers all possible future cases
   // ...
}
```

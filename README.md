### **final**

- `final` is a restriction on a class, method or property that indicates that the declaration cannot be overridden.
- It helps increase in performance by reducing **Dynamic Dispatch**.
- For most cases *XCode* automatically optimize your classes to the `final class` if they don't have subclasses.

```swift
final class ExampleClass1 {
    var integer = 9
    func function() { print("ExampleClass1().function()") }
}

class ExampleClass2: ExampleClass1 { // Error: inheritance from a final class "ExampleClass1".
    var string = "sample"
}
```

```swift
class ExampleClass3 {
    // Cannot override stored properties without a turn around, so instead I defined computed property.
    final var string: String { "hello world" }
    final func function() { print("ExampleClass3().function()") }
}

class ExampleClass4: ExampleClass3 {
    override var string: String { "world hello" } // Error: cannot override a final property.
    override var function() { print("ExampleClass4().function()") } // Error: cannot override a final function.
}
```

# lazy

`lazy` variables allow you to delay the initialization of stored properties.

- Difference between *lazy* vs *computed property* is, computed property calculated is every time you called it, lazy is computed only in the first time you called it.

- Because `lazy` variables are created later, it can't be `let`, it must be `var`.

### `struct` version.
```swift
struct Person {
    var age = 16
    lazy var = fibonacciOfAge: Int = { return fibonacci(of: self.age) }()
    func fibonacci(of num:Int) -> Int {
        if num < 2 { return num }
        else { return fibonacci(of: num - 1) + fibonacci(of: num - 2) }
    }
}
var sample = Person()
print(sample.age) // 16
print(sample.fibonacciOfAge) // 987 - fibonacciOfAge is created, it is same as stored value now (var fibonacciOfAge = 987)
sample.age = 25
print(sample.age) // 25
print(sample.fibonacciOfAge) // 987 - fibonacciOfAge is created before so It won't be calculated again.
```

### `class` version.
```swift
class Person {
    var age = 16
    lazy var = fibonacciOfAge: Int = { [unowned self] in return fibonacci(of: self.age) }()
    func fibonacci(of num:Int) -> Int {
        if num < 2 { return num }
        else { return fibonacci(of: num - 1) + fibonacci(of: num - 2) }
    }
}
let sample = Person()
print(sample.age) // 16
print(sample.fibonacciOfAge) // 987 - fibonacciOfAge is created, it is same as stored value now (var fibonacciOfAge = 987)
sample.age = 25
print(sample.age) // 25
print(sample.fibonacciOfAge) // 987 - fibonacciOfAge is created before so It won't be calculated again.
```

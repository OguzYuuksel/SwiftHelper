### Summary
- Functors: you apply a function to a wrapped value using `map`.
- Applicatives: you apply a wrapped function to a wrapped value using `apply`, if defined.
- Monads: you apply a function that returns a wrapped value, to a wrapped value using `flatMap`.

### [Article](https://mokacoding.com/blog/functor-applicative-monads-in-pictures/#:~:text=An%20applicative%20is%20a%20type,an%20applicative%2C%20and%20a%20monad.)

### Example
```
// MARK: - Applicative implementation.
extension Array {
    func apply<U>(fs: [(Element) -> U]) -> [U] {
    var result = [U]()
      for f in fs {
        for element in self.map(f) {
          result.append(element)
        }
      }
      return result
    }
}

extension Optional {
    func apply<U>(f: ((Wrapped) -> U)?) -> U? {
    switch f {
      case .some(let someF): return self.map(someF)
      case .none: return .none
    }
  }
}

// MARK: - Examples
let array = [[1, 2, 3], [4, 5, 6], [7]]
let optional: Int?? = 1
let applicativeFunc: ((Int) -> String)? = String.init

// MARK: Results
// -- Functor
// ---- Unwraps the value from the outer context,
//      then apply the modifying function to the value,
//      and wraps the new value to the outer context. (applied one level deep contexts)
print("----- Functor Examples -----")
print("Array: ", array.map { $0 })
print("Optional: ", optional.map { $0 } ?? "Optional(nil)")

// -- Applicative
// ---- Unwraps the value and modifying function from the outer context,
//      then apply the modifying function to the value,
//      and wraps the new value to the outer context. (applied one level deep contexts)
print("----- Applicative Examples -----")
print("Array: ", array.apply(fs: [String.init]))
print("Optional: ", optional.flatMap { $0 }.apply(f: applicativeFunc) ?? "nil")

// -- Monad
// ---- Flattens nested contexts by looping contexts until reaching the value,
//      then apply the modifying function to the value,
//      and wraps the new value to the outer context. (applied nested contexts)
print("----- Monad Examples -----")
print("Array: ", array.flatMap { $0 })
print("Optional: ", optional.flatMap { $0 } ?? "nil")
```

# ARC

- **ARC (Automatic Reference Counting)** keeps track of strong references to an object, and when the count goes down to 0, the object will automatically deallocate from memory.

- **weak & unowned** don't increase count by not creating a strong hold on objects.

- **Retain Cycle** is the memory count of an instance never goes down to 0 which leads to a memory leak.

- **Retain Count (Reference count)** represents the number of owners for a particular object.

```swift
class Person {
  let name: String
  init(name: String) {
    self.name = name
  }
  deinit { print("Person() deinit") }
}
let person1 = Person(name: "Swifts") // retain count: 1
let person2 = person1 // retain count: 2
let person3 = person1 // retain count: 3
```

Swift has two parts for memory allocations and automatically allocates them in the *Heap* or *Stack*.

### **Heap Memory**

- Allocates memory dynamically, but less efficient than the stack.
- Can grow and shrink in size.
- Reference types, such as classes, closures, Class inside Struct(Stored in Heap), Value Type with Generics
- Classes are great to use as an identity and for indirect storage.
- Shared by all CPU threads.

### **Stack Memory**

- Faster than Heap memory.
- Stacks store value types, such as structs and enums.
- Data stored in the stack is only there temporarily until the function exits. After returning of function, stack memory is deallocated.
- First In Last Out -> deallocates like cascade
- Each CPU thread has its own Stack.

<img src=".\Assets\stack-heap.png" alt="stack-heap" style="zoom: 80%;" />



**Reference Type**: *Class*, *Closure*

**Value Type**: *Struct*, *Enum*, *Tuples*

- **Value Types with Inner Reference -** *Stroing in Stack*: Inherit reference counting overhead with a decrease in performance.

  *String*, *Array*, *Dictionary*, *Set*, *Structs which are parents of Classes* or *Value Types with Inner References*.

    ```swift
    class ClassA {
        let str = ""
    }
    struct StructA {
        let class = ClassA()
    }
   
    var struct1 = StructA()
    var struct2 = StructA() // struct2.class === struct1.class = false
    var struct3 = struct1 // struct3.class === struct1.class = true
   
    ```

- **Swift Tail Recursion Optimization**: This optimization can make recursive calls take constant stack space (they use the same space becase it is the same function), rather than deallocating and allocating space again and again.

  ```swift
  struct Struct1 {
      let str = "test"
  }
  struct Struct2 {
      let str1 = "test1"
      let str2 = "test2"
      let str3 = "test3"
  }
 
  for _ in 0..<1_000_000 {
      let copy = Struct1
  } // 0.005 seconds
 
  for _ in 0..<1_000_000 {
      let copy = Struct2
  } // 0.0033 seconds
  // copying a static sized struct is a constant time operation.
  ```



## Performance Issues

- If your *Value Type* is more complicated than below and you have a performance issues, you can improve your performance by swapping unnecessary references with proper static size *Value Types*.

  ```swift
  struct DeliveryAddress {
  let identifier: String // has inner reference
  let type: String // has inner reference
  }
  ```

  ```swift
  struct DeliveryAddress {
  enum AdressType {
          case home
          case work
      }
      let identifier: UUID // value type
      let type: AdressType // value type
  }
  ```

- If a *Value Type* have too much *Generic Type* and/or *Inner References*, and if this *Sturct/Enum* is copied multiple places in the project, it may be better to make it a *Class* to share reference instead of copying.

- **Copy On Assignment** : Value Types

- **Copy On Write**: Value Types with Inner Reference. all the data structures (such as `Array`, `Dictionary`, and `Set`) are copy-on-write in the Swift standard library.
  ```swift
  // copy on Assignment
  let emptyStruct = EmptyStruct() // adress A
  let copy = emptyStruct // adress B
 
  // copy on write
  let array = [1,2,3] // adress C
  var notACopy = array // adress C
  notACopy = [4,5,6] // adress D
  ```

  - **Copy On Write for Custom Types**

    ```swift
    fileprivate class BackendQueue<T> { // Queue: FIFO type (addItem-getItem)
      private var items = [T]()
      public init() {} // Creates new instance of BackendQueue with empty array.
      private init(_ items: [T]) { self.items = items } // Creates new instance of BackendQueue with current items array.
      public func addItem(item: T) { items.append(item) } // Queue: adds in the end of list.
      public func getItem() -> T? { if items.count > 0 { return items.remove(at: 0) } else { return nil } }  // Queue: gets first item in the list. O(n).
      public func count() -> Int { return items.count }
      public func copy() -> BackendQueue<T> { return BackendQueue<T>(items) } // Returns new unique instance of BackendQueue<T>
    }
    ```

    ```swift
    struct Queue {
      private var internalQueue = BackendQueue<Int>()
      mutating private func checkUniquelyReferencedInternalQueue() {
            // If unique, not need created another instance(Reference to the same pointer will cause less memory allocation.).
            // Else create another(Copy On Write) because you change values inside queue.
      if !isKnownUniquelyReferenced(&internalQueue) {
            print("Making a copy of internalQueue")
            internalQueue = internalQueue.copy() // Returns new unique instance of BackendQueue<T>
      } else {
            print("Not making a copy of internalQueue")
        }
      }
      public mutating func addItem(item: Int) {
            checkUniquelyReferencedInternalQueue() // Create new internalQueue if Copy On Write
            internalQueue.addItem(item: item) // Apply changes in your new copied instance.
      }
      public mutating func getItem() -> Int? {
            checkUniquelyReferencedInternalQueue() // Create new internalQueue if Copy On Write
            return internalQueue.getItem()  // Apply changes in your new copied instance.
      }
      public func count() -> Int { return internalQueue.count() }
      mutating public func uniquelyReferenced() -> Bool {
            return isKnownUniquelyReferenced(&internalQueue) // Check if your Queue is unique or not.
      }
    }

    var queue1 = Queue()
    queue1.addItem(item: 1) // Not making a copy of internalQueue
    print(queue1.uniquelyReferenced()) // True - BackendQueue() ref count: 1 (queue1.internalQueue)
    var queue2 = queue1
    print(queue1.uniquelyReferenced()) // False - BackendQueue() ref count: 2 (queue2.internalQueue, queue1.internalQueue)
    print(queue2.uniquelyReferenced()) // False - BackendQueue() ref count: 2 (queue2.internalQueue, queue1.internalQueue)
    queue1.addItem(item: 5) // Making a copy of internalQueue
    print(queue1.uniquelyReferenced()) // True - BackendQueue() ref count: 1 (queue1.internalQueue)
    print(queue2.uniquelyReferenced()) // True - BackendQueue() ref count: 1 (queue2.internalQueue)
    ```

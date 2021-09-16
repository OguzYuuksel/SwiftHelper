# **@inlineable**
`@inlinable` allows you to enable cross-module inlining for a particular method. When this is done, the implementation of the method will be exposed as part of the module's public interface, allowing the compiler to further optimize calls made from different modules.
 
 If the inlinable method is calling other methods internally, the compiler will ask you to also `@inlinable` these methods. This can be done either by `@inlinable` or `@usableFromInline`, which indicates that although the method is being used from an inlined method, it is not inlined.
 ```swift
 @inlinable func myMethod() {
     myMethodHelper()
 }
 
 // Used within an inlinable method, but not inlinable itself
 @usableFromInline internal func myMethodHelper() {
     // ...
 }
 ```
On the other hand, `@inlinable` can be a massive problem depending on what you're building. If the implementation of an `@inlinable` method changes, the modules that import it will not be able to make use of the modifications unless they are recompiled. Normally you would be able to update a framework by simply replacing the binary, but because the implementation of some methods got inlined, the app will keep running the old behavior even if you're linking to the new version. Because of this, apps with the **library evolution** setting enabled may find themselves unable to play around with `@inlinable` as this can break the ABI stability of your framework.


## Bundle

Bundle is a directory in the file system that contains executable code and related resources such as images and sounds, nib files, private frameworks and libraries, plug-ins, loadable bundles, or any other type of code or resource.


It also contains a runtime-configuration file called the information property list *(Info.plist)*. Each of these items has its proper place in the bundle structure. Resources such as images, sounds, and nib files are deposited in the *Resources* subdirectory.

   <img src=".\Assets\structureBundle.png" alt="structureBundle" style="zoom:60%;" />

   

------

   

## **Package**  

Swift Package contains source files and manifest file *(Package.swift)* which describes the configuration for the Swift package. Package dependencies are linked statically into application itself by default.

### Framework (*.framework)

- A hierarchical directory that encapsulates a dynamic library(Static libraries compiled in the same binary file with apps so we don't have a static library binary file to add Framework.), header files, and resources, such as storyboards, image files, and localized strings, into a single package.

- System Frameworks are shared between all app in iOS, macOS, tvOS, watchOS.

- You can have a custom shared framework in macOS(~/Library/Frameworks) that all Apps can use. However, in iOS, tvOS, watchOS, you can only have static binary frameworks. Even if two Apps using the same custom framework will load their own copies of framework during run time.

- **All Swift Frameworks are *.XCFrameworks* after XCode 12.**

> Let's say you want to cook a fish curry. Then you need ingredients like oil, spices and other utilities. You also need fish which is your base to prepare your dish on (This is data of your application). all ingredients together called a framework. Now you gonna use them one by one or in combination to make your fish curry which is your final product. Compare that with a web framework which is made out of underscore.js, bootstrap.css, bootstrap.js, fontawesome, AngularJS etc. For an example, Twitter Bootstrap v.35.
>
> Now, if you consider only one ingredient, like say oil. You can't use any oil you want because then it will ruin your fish (data). You can only use Olive Oil. Compare that with underscore.js. Now what brand of oil you want to use is up to you. Some dish was made with American Olive Oil (underscore.js) or Indian Olive Oil (lodash.js). This will only change taste of your application. Since they serve almost same purpose, their use depends on the developer's preference and they are easily replaceable.
>
> <img src=".\Assets\framework.png" alt="framework" style="zoom:20%;" />
>
> **Framework** : A collection of libraries which provide unique properties and behavior to your application. (All ingredients)
>
> **Library** : A well defined set of instructions which provide unique properties and behavior to your data. (Oil on Fish)
>
> P.S. AngularJS is a MVC framework but a JavaScript library. Because I believe Library extends default behavior of native technology (JavaScript in this case).

  - **XCFramework**: a binary framework that can containing code for multiple architectures and platforms(iOS, macOS, tvOS, watchOS, Simulators). You will still be required to generate archives for different platforms and bundle them up together in single XCFrameworks. It is also possible to make Swift packages that include one or more XCFrameworks.

   

    ### **Library**

      - **Static Library(*.a)**: *Source* code and *Static Library* code compiles into a single executable file, which loaded into memory in its entirety at runtime.
        - You’ll need to build a library for the same processor architecture as the client code. A library for the iOS application you will need to create a library for *iOS Simulator(x86 for Intel Macs)* and *iOS devices(ARM)*.
        - The library can’t include resource files: images, assets, nibs, strings file and other visual data. If you will include this resources to project them will be separate from .a file. Usually, as a solution to this problem, all relates external resources provided within another independent bundle.
      - **Dynamic Library(*.dylib)**: They are not copied into executable file, like static libraries. Instead, they are dynamically linked to at load or runtime. Dynamic libraries stored and versioned separately in app bundle.
        - System libraries are dynamic. This means that your app will receive improvements from Apple’s updates without new build submission.

## Overview

Linking too many static libraries into an app produces large app executable files, slow launch times and large memory footprints. Frameworks gives you much more flexibility than static libraries, they can contain resources. But each embedded framework added to project increases startup time as well.

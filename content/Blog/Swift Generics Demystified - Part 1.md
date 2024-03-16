---
title: "Swift Generics Demystified - Part 1: Concrete/Soft Types"
publish: true
---


In our last article, we learned about how the generics system is deeply integrated into Swift at practically every level. This can give us magical features that help like *Type Inference* which makes our code easier to read and right, but it can also lead frustrating and confusing compile-time errors. Furthermore, most modern Swift libraries are filled with generic code, especially in Apple first-party frameworks such as **SwiftUI**, **Combine**, and the recently announced **SwiftData**. I hope that I've made a strong case that generics in Swift are simply too important to ignore. So without further ado, let's dive into generics, albeit with a slightly different approach than you might expect. 

## Reading Generic Code
You might expect an article on Swift Generics to start with writing generic code, and in fact [[#Suggested Articles on Swift Generics|many fantastic authors have already covered this quite well]]. But perhaps a better approach would be to start with **reading** generic code. This is for a few reasons: 
1. By nature, generic code is generalized to multiple use cases. It takes work to understand **one** use case, let alone many. 
2. Generic code is quite abstract. 

So here is what we will do. Let's look at a few basic common types that are used throughout SwiftUI, and see what we can learn from them, starting with the most basic of them all, the humble [View](https://developer.apple.com/documentation/swiftui/view). 

### SwiftUI's `View`
Every single SwiftUI View has a `: View` after it's name like so: 
```swift
struct MyView: View {
	 var body: some View {
		 Text("Hello World")
	 }
}
```
In Xcode, right click on the word `View` and click "Jump to Definition". You should see something like this: 
```swift
public protocol View {

    /// ...
    associatedtype Body : View

    /// ...
    @ViewBuilder @MainActor var body: Self.Body { get }
}
```
What can we learn here about the `View` type? Well, that's a bit of a trick question. `View` isn't really a Type, exactly. It's a protocol. The Swift documentation says this: 

> ## [Protocols as Types](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/protocols/#Protocols-as-Types)
> 
> Protocols don’t actually implement any functionality themselves. Regardless, you can use a protocol as a type in your code.

Think of protocols as rules. In real life, if we follow certain rules, we get perks. If you pass the driving test, then you get the perk of being allowed to drive legally. Likewise, if your type conforms to the `View` protocol, then it now gets to do all the cool things that SwiftUI Views can do. But the View protocol doesn't actually do anything since it doesn't "actually implement any functionality".  **The Type that conforms to the `View` protocol is the actual thing that has properties and methods.**

### Introducing Concrete and "Soft" Types
If you look at the Swift docs on [Types](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/types/) and do a CMD-F search for "*concrete type*", you'll see that the phrase is used throughout. Unfortunately, though, I haven't yet found an official definition of what exactly *concrete type* means.[^1] But I think the definition is pretty clear from the context. **A concrete type is the *actual* type that will be used at runtime.** But if there's such a thing as *concrete types* then that implies that there are *non-concrete* types, types that aren't actually used at runtime. However, I haven't found an official name for these *non-concrete* types, so I'll refer to them as *soft types*. **A _soft type_ is a type isn't actually used at runtime. Instead, it gives instructions to Swift on how to find the _concrete type_.**  We can see an example of this in every SwiftUI View: 

[^1]: You might say that I haven't found a *concrete* definition of *concrete types*.

```swift 
var body: some View
```
The `body` property is explicitly typed using `:` but what is the type? `some View`. But `View` is not a *concrete type* since it's a protocol. Somewhere, Swift has to infer the *concrete type*. Remember, Swift is a strongly typed language so **everything** has a type. The answer is that this is an example of an [opaque type](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/opaquetypes). Essentially, we're telling Swift that body will be "some View". We're not telling Swift which specific type it will be. Instead, Swift will infer the type for us as long as we give it a type that conforms to `View`. For example: 

```swift
var body: some View { // `some View` is the soft type
	 Text("Hello World") // `Text` is the concrete type
}
// ...
var body: some View {
	VStack { // `some View` is the soft type
		Text("Hello World") 
	} // The concrete type is `VStack<Text>`
}
```

As you can see VStack is generic. Now try altering your `body` to look like this: 

```swift
struct MyView: View {
  var body: VStack { // 🛑 Error: Reference to generic type 'VStack' requires arguments in <...>

    VStack { // `some View` is the soft type
      Text("Hello World")
    } // The concrete type is `VStack<Text>`
  }
}
```

So I would say that `VStack` is also a *soft type*. In other words, even if Swift knows that it's a `VStack`, that is not enough information for Swift to infer the *concrete type*. In fact, every generic type is a *soft type*. Every time that we use a generic type, we have to make sure that we are giving Swift enough information to find the concrete type. This could get very tedious and error prone, and so that's why Swift gives us various tools like opaque types (the `some` keyword) to make this easier. 

```swift 
struct MyView: View {
  var body: some View { // `some View` is the soft type
    List { // ⭐ the concrete type is some gigantic nested monstrosity
      ForEach(0..<9) { num in
        VStack {
          Text("This is some text in a row cell.")
          Text("This is the current number: \(num)")
        }
      }
    }
  }
}
```

### `associatedType`: generics for protocols
Just as we can make types generic, we can also make protocols generic using the `associatedType` keyword. The [Swift docs says](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/generics/): 
>When defining a protocol, it’s sometimes useful to declare one or more associated types as part of the protocol’s definition. An _associated type_ gives a placeholder name to a type that’s used as part of the protocol. The actual type to use for that associated type isn’t specified until the protocol is adopted.

So just like how the `Array` type has a *generic type parameter* called `Element`, the `View` protocol has an *associated type* called `Content`. And as we can see in the definition, `Body` must conform to the `View` protocol. 

```swift
public protocol View {
    associatedtype Body : View
    @ViewBuilder @MainActor var body: Self.Body { get }
}
```

But don't forget `Body` is **not** a concrete type. It's a *soft type*, a placeholder for some type that conforms to `View`. 
## Suggested Articles on Swift Generics
### Progressive Disclosure of Information



#### Nominal Typing vs. Structural Typing

### Generics: How to Make Swift Loosen up a little


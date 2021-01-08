---
layout: post
title:  5 Ways To Unwrap an Optional in Swift
date:   2021-01-07 10:00:00 -0800
tags: Swift
mins: 4
---

Tony Hoare introduced Null references in ALGOL W back in 1965. He said that decision was his [billion-dollar mistake][1]. It's true that handelling Null objects is a pain. That's why I love that Swift does not have Null references. Instead it has an elegant `Optional` type. You use the `Optional` type whenever you need to use optional values. 

Optional values are different from Null references. Object references in other languages can point to the real object or not point to anything (i.e. they are Null). Whereas `Optional` in Swift is actually an enum of two cases - `none` and `some`.

```swift
enum Optional<Wrapped> {
    /// The absence of a value.
    case none

    /// The presence of a value, stored as `Wrapped`.
    case some(Wrapped)
}
```

Here `Wrapped` is the underlying type. Swift simplifies the syntax to define optional using trailing question mark (`?`) instead of typing the full type name. For example, if a variable has the type `Int?`, that’s shortened way of writing `Optional<Int>`. And the absence of a value is shortened using the `nil` literal rather than the explicit `.none` enum case.

You must "unwrap" the value of an `Optional` instance before you can use it. I will show 5 ways to unwrap optional values. The following examples use this dictionary of `String` to `Cat` objects. 

```swift
let schrodingersBox = [ "cat": Cat(name: "Shady")]
```

Getting a dictionary’s value using a key returns an optional value, so `schrodingersBox["Cat"]` has type `Cat?`.

## Forced Unwrapping
If you’re sure that an optional contain a value, you can _force unwrap_ its underlying value by adding an exclamation point (`!`) to the end of the optional’s name.

```swift
if schrodingersBox["Cat"] != nil {
    schrodingersBox["Cat"]!.meow()
}
```

Trying to use `!` to access a nonexistent optional value triggers a runtime error. Therefore, forced unwrapping is not generally recommended. 

## Optional Binding
You can use `if let` to find out whether an optional contains a value, and if so, unwrap that value as a new variable. This is called _optional binding_. 

```swift
if let aliveCat = schrodingersBox["Cat"] {
    aliveCat.meow()
}
```

In this example the scope of `aliveCat` is only within the body of the `if` statement.

## Guard Statement
You can use `guard` statement to also check if optional contain a value. The `guard` statement has an `else` clause. The code inside the `else` clause is executed if the optional doesn't have a value. If the optional has a value, it is available in the lines of code that follow the guard statement.

```swift
guard let aliveCat = schrodingersBox["Cat"] else {
    return
}
aliveCat.meow()
```

Using `guard` statement lets you _early exit_ a method if value is not present. It improves the readability of the code compared to doing the same check with an `if` statement.


## Nil-Coalescing Operator
The fourth way you can unwrap an optional is using the nil-coalescing operator (`??`). This lets you provides a default if optional is `nil`. 

```swift
let aliveCat = schrodingersBox["Cat"] ?? defaultCat
aliveCat.meow()
```

## Optional Chaining

Finally, you can also unwrap by placing a question mark (`?`) after the optional. This is called _optional chaining_. 

```swift
schrodingersBox["Cat"]?.meow()
```

If the optional contains a value, the method call succeeds; if the optional is `nil`, the method returns `nil`. Multiple queries can be chained together, and the entire chain fails gracefully if any link in the chain is `nil`. This is similar to forced unwrapping using `!`. But the difference is that optional chaining fails gracefully when the optional is `nil`, whereas forced unwrapping triggers a runtime error.


[1]: https://www.infoq.com/presentations/Null-References-The-Billion-Dollar-Mistake-Tony-Hoare/


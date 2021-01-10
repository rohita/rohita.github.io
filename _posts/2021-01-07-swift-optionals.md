---
layout: post
title:  6 Ways To Unwrap an Optional in Swift
date:   2021-01-07 10:00:00 -0800
tags: Swift
mins: 6
excerpt: <p><code>Optional</code> instances in Swift must be "unwraped" before the values can used. I will show you 6 ways to unwrap optionals using a fun Schrodinger's cat example. How many of these methods do you know already? The last one may surprise you!</p>
---

Tony Hoare introduced Null references in ALGOL W back in 1965. He said that decision was his [billion-dollar mistake][1]. It's true that handelling Null objects is a pain. That's why I love that Swift does not have Null references. Instead it has an elegant `Optional` type. You use the `Optional` type whenever you need to use optional values. 

Optional values are different from Null references. Object references in other languages can point to the real object or not point to anything (i.e. they are Null). Whereas `Optional` in Swift is actually an enum of two cases.

```swift
enum Optional<Wrapped> {
    case none
    case some(Wrapped)
}
```

Optional either holds _no value_ or _some value_. The `.none` enum case denotes the absence of a value and is equivalant to the `nil` literal. The presence of value is stored as `Wrapped`, which is the underlying type of the value. Swift simplifies the syntax to define optional by using trailing question mark (`?`) instead of typing the full type name. For example, if a variable has the type `Int?`, that’s shortened way of writing `Optional<Int>`. 

You must "unwrap" the value of an `Optional` instance before you can use it. I will show you 6 ways to unwrap optional values. The following examples use this dictionary of `String` to `Cat` objects. 

```swift
let schrodingersBox = ["cat": Cat(name: "Shady")]
let optionalCat: Cat? = schrodingersBox["Cat"]
```

Getting a dictionary’s value using a key [returns an optional value][2], so `schrodingersBox["Cat"]` has type `Cat?`.

## Optional Binding
The first way you can unwrap an optional is use `if let` to find out whether an optional contains a value, and if so, unwrap that value as a new variable. This is called _optional binding_. 

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
The third way you can unwrap an optional is using the _nil-coalescing operator_ (`??`). This lets you provides a default value if optional is `nil`. 

```swift
let aliveCat = schrodingersBox["Cat"] ?? defaultCat
aliveCat.meow()
```

## Optional Chaining

Next, you can also unwrap by placing a question mark (`?`) after the optional. This is called _optional chaining_. 

```swift
schrodingersBox["Cat"]?.meow()
```

If the optional contains a value, the method call succeeds; if the optional is `nil`, the method returns `nil`. Multiple queries can be chained together, and the entire chain fails gracefully if any link in the chain is `nil`. 


## Forced Unwrapping
So far we look as _safe_ ways to unwrap optionals. These next 2 should be used when you’re sure that an optional contain a value. You can _force unwrap_ an optional's underlying value by adding an exclamation point (`!`) to the end of the optional’s name.

```swift
if schrodingersBox["Cat"] != nil {
    schrodingersBox["Cat"]!.meow()
}
```

This is similar to optional chaining using `?`. But the difference is that optional chaining fails gracefully when the optional is `nil`, whereas trying to use `!` to access a nonexistent optional value triggers a runtime error. Therefore, forced unwrapping is not generally recommended. 

## Implicit Unwrapping

Finally, sometimes it’s useful to remove the need to check and unwrap the optional’s value every time it’s accessed, because it can be safely assumed to have a value all of the time. These kinds of optionals are declared using exclamation mark instead of a question mark, and are called as _implicitly unwrapped optionals_. 

```swift
let aliveCat: Cat! = schrodingersBox["Cat"]
aliveCat.meow()
```

This way optionals are unwrapped automatically and can be used like a non-optional values, without the need to unwrap each time it’s accessed. However, if an implicitly unwrapped optional is `nil` and you try to access its wrapped value, you’ll trigger a runtime error. 

## Conclusion

I cannot say if Swift Optionals saves a billion dollars, but I hope at least this article helpes in understanding optionals in Swift and how to unwrap them. Please feel free to contact me if you have any questions or comments. 


[1]: https://www.infoq.com/presentations/Null-References-The-Billion-Dollar-Mistake-Tony-Hoare/
[2]: https://developer.apple.com/documentation/swift/dictionary/2885650-subscript


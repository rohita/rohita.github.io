---
layout: post
title:  6 Ways To Unwrap an Optional in Swift
date:   2021-01-07 10:00:00 -0800
category: Blog
tags: Swift
mins: 6
---

`Optional` instances in Swift must be "unwraped" before their values can be used. I will show you 6 ways to unwrap optionals using examples from an imaginary cat experiment. How many of these methods do you already know? The last one may surprise you!

# Introduction

[Tony Hoare][3] invented the Null reference in 1965 as part of the ALGOL W language. He later called it as a ["billion-dollar mistake"][1], saying

> ...it has led to innumerable errors, vulnerabilities, and system crashes, which have probably caused a billion dollars of pain and damage in the last forty years. 

Swift does not have Null references. Instead it has an elegant `Optional` type, which represents either a value or `nil`. Optional values are different from Null references. Object references in other languages can point to the real object or not point to anything (i.e. they are Null). Whereas, `Optional` in Swift is actually an enum of two cases.

```swift
enum Optional<Wrapped> {
    case none
    case some(Wrapped)
}
```

Optional either holds _no value_ or _some value_. The `none` enum case denotes the absence of a value and is equivalant to the `nil` literal. The presence of value is stored as `Wrapped`, which is the underlying type of the value. Swift simplifies the syntax to define optional by using trailing question mark (`?`) instead of typing the full type name. For example, if a variable has the type `Int?`, that’s shortened way of writing `Optional<Int>`. 

# Unwrapping Optionals	

Unwapping optionals means extracting out the stored value before it can be used. I will show you 6 ways to unwrap optional values. The following examples use a dictionary of `String` to `Cat` objects. 

```swift
let schrodingersBox = ["cat": Cat(name: "Shady")]
let optionalCat: Cat? = schrodingersBox["Cat"]
```

Getting a dictionary’s value using a key [returns an optional value][2], so `schrodingersBox["Cat"]` has type `Cat?`.

## 1. Optional Binding
The first way you can unwrap an optional is use `if-let` to find out whether an optional contains a value, and if so, unwrap that value as a new variable. This is called _optional binding_. 

```swift
if let aliveCat = schrodingersBox["Cat"] {
    aliveCat.meow()
}
```

In this example the scope of `aliveCat` is only within the body of the `if` statement.

## 2. Guard Statement
You can use `guard` statement to also check if optional contains a value. The `guard` statement has an `else` clause. The code inside the `else` clause is executed if the optional doesn't have a value. If the optional has a value, it is available in the lines of code that follow the guard statement.

```swift
guard let aliveCat = schrodingersBox["Cat"] else {
    return
}
aliveCat.meow()
```

Using `guard` statement lets you _early exit_ a method if value is not present. It improves the readability of the code compared to doing the same check with an `if` statement.


## 3. Nil-Coalescing Operator
The third way you can unwrap an optional is using the _nil-coalescing operator_ (`??`). This lets you provide a default value if optional is `nil`. 

```swift
let aliveCat = schrodingersBox["Cat"] ?? defaultCat
aliveCat.meow()
```

## 4. Optional Chaining

Next, you can also unwrap by placing a question mark (`?`) after the optional. This is called _optional chaining_. 

```swift
schrodingersBox["Cat"]?.meow()
```

If the optional contains a value, the method call succeeds; if the optional is `nil`, the method returns `nil`. Multiple queries can be chained together, and the entire chain fails gracefully if any link in the chain is `nil`. 


## 5. Forced Unwrapping
So far we have looked at _safe_ ways to unwrap optionals. These next 2 are unsafe, and should be used when you’re sure that an optional contains a value. First of these is to _force unwrap_ an optional's underlying value by adding an exclamation point (`!`) to the end of the optional’s name.

```swift
if schrodingersBox["Cat"] != nil {
    schrodingersBox["Cat"]!.meow()
}
```

This is similar to optional chaining using `?`. But the difference is that optional chaining fails gracefully when the optional is `nil`, whereas trying to use `!` to access a nonexistent optional value triggers a runtime error. Therefore, forced unwrapping is not generally recommended. 

## 6. Implicit Unwrapping

Finally, sometimes it’s useful to remove the need to check and unwrap the optional’s value every time it’s accessed, because it can be safely assumed to have a value all of the time. These kinds of optionals are declared using exclamation mark instead of a question mark, and are called as _implicitly unwrapped optionals_. 

```swift
let aliveCat: Cat! = schrodingersBox["Cat"]
aliveCat.meow()
```

This way optionals are unwrapped automatically and can be used like non-optional values, without the need to unwrap them each time they're accessed. However, if an implicitly unwrapped optional is `nil` and you try to access its wrapped value, you’ll trigger a runtime error. 

# Conclusion

I cannot say if Swift optionals have saved a billion dollars, but I hope at least this article helped you in understanding how to unwrap them. Please feel free to contact me if you have any questions or comments. 


[1]: https://www.infoq.com/presentations/Null-References-The-Billion-Dollar-Mistake-Tony-Hoare/
[2]: https://developer.apple.com/documentation/swift/dictionary/2885650-subscript
[3]: https://en.wikipedia.org/wiki/Tony_Hoare


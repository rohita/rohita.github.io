---
layout: post
title:  "Swift Optionals"
date:   2021-01-03 10:00:00 -0800
tags: Swift
mins: 10
---

https://www.infoq.com/presentations/Null-References-The-Billion-Dollar-Mistake-Tony-Hoare/

https://www.avanderlee.com/swift/optionals-in-swift-explained-5-things-you-should-know/
https://jllnmercier.medium.com/swift-enums-b68a1015e2ce


## If Statements and Forced Unwrapping
You can use an `if` statement to find out whether an optional contains a value by comparing it against `nil`. Once you’re sure that the optional does contain a value, you can _forced unwrap_ its underlying value by adding an exclamation point (`!`) to the end of the optional’s name.

```swift
if convertedNumber != nil {
    print("convertedNumber has an integer value of \(convertedNumber!).")
}
```

Trying to use `!` to access a nonexistent optional value triggers a runtime error. 

## Optional Binding

## Optional Chaining

https://developer.apple.com/documentation/swift/optional

## Nil-Coalescing Operator

## Early Exit

## Implicitly Unwrapped Optionals



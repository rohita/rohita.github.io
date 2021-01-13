---
layout: post
title:  Error Handling
date:   2021-01-11 10:00:00 -0800
category: Blog
tags: Swift
mins: 6
---

empty Error protocol

There is no common Exception class which every one use without having to create a new one every time they have to throw an error. 

https://github.com/highlightjs/highlight.js/blob/master/src/styles/stackoverflow-light.css



## Enums as Errors



```swift

```

Up until Swift 4.0, adding a new case to an enum was a source-breaking change

## Struct as Errors

```swift


```

## Throwing

To indicate that a function can throw an error, you write the `throws` keyword in the function’s declaration. A function marked with `throws` is called a _throwing function_. This is similar to _checked exceptions_ in other languages, like Java. But unlike Java, Swift does not also support unchecked exceptions. Only throwing functions can propagate errors. Any errors thrown inside a nonthrowing function must be handled inside that function.

### Source Breaking 

This is somewhat probamatic. Consider a call hierarchy of a large system. Functions at the top call functions below them, which call more functions below them, as so on. If one of the lowest level functions is modified to throw an error, then every function that calls it must also be modified either to catch the error or add the `throws` clause to its signature. And so on. The result is a cascade of changes from the lowest levels to the highest. 

So be careful adding `throws` to existing methods, since it is can be a big source-breaking change. 

### Alternative return values

The checked expection are really alternative return values. The whole idea of exceptions is that an error thrown somewhere way down the call chain can bubble up and be handeled by somewhere futher up, without the intervening code having to worry about it. Checked exceptions require every level of code between the thrower and the catcher to declare they know about the exception that can go through them. This is really no different in practice to returning special return values which the caller had to check for. 



## Handelling
You must handle errors if you call a throwing function. You cannot let it silently bubble up the errors. As mentioned above, if a nonthrowing low level function suddenly becomes a throwing function, is a source-breaking change, because now you have to handle it. 


There are four ways to handle errors in Swift. 

### 1. Propagate up

```swift
func buyFavoriteSnack(person: String, vendingMachine: VendingMachine) throws {
    let snackName = favoriteSnacks[person] ?? "Candy Bar"
    try vendingMachine.vend(itemNamed: snackName)
}
```

### 2. Handle using do-catch

```swift
do {
    try buyFavoriteSnack(person: "Alice", vendingMachine: vendingMachine)
    print("Success! Yum.")
} catch VendingMachineError.invalidSelection {
    print("Invalid Selection.")
}
```

The `catch` clauses don’t have to handle every possible error that the code in the `do` clause can throw. If none of the `catch` clauses handle the error, the error propagates to the calling scope. In a nonthrowing function, an enclosing `do-catch` statement must handle the error cases.

### 3. Handle as an Optional 

You use `try?` to handle an error by converting it to an optional value. If an error is thrown the value of the expression is `nil`. 

```swift
let x : Int? = try? someThrowingFunction()
x == nil
```

Note that x is an optional of whatever type someThrowingFunction() returns. 


### 4. Assert that error will not occur

you can write `try!` before the expression to disable error propagation and wrap the call in a runtime assertion that no error will be thrown. If an error actually is thrown, you’ll get a runtime error.

```swift
let photo = try! loadImage(atPath: "./Resources/John Appleseed.jpg")
```



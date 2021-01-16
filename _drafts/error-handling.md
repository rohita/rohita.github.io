---
layout: post
title:  Error Handling
date:   2021-01-11 10:00:00 -0800
category: Blog
tags: Swift
mins: 6
---

We will look into 3 aspects of Error Handling in Swift: modeling errors, throwing errors, and handling errors. 

# Modeling Errors

Before you can throw errors you have define its type. In Swift, any type that conforms to the `Error` protocol can be used to represent an error. The protocol itself is empty.

```swift
public protocol Error { }
```

Any type can be an error, such as classes, structs or enums. 

{:.note}
There is no ready-made `Exception` class in the standard library which you can use in a crunch. You have to create a custom error type in your application to throw an error. This seems like an intentional decision by Swift designers to force developers to not be lazy and model errors for their domain.  


## 1. Enums as Errors

The standard Swift pattern is to define an `enum` conforming to `Error` protocol, with various related error conditions as its cases. The `enum` provides sort of a namespace of errors for your application domain. These cases can have associated values for additional information about the error.

```swift
enum HomeworkError : Error {
  case overworked
  case impossible
  case eatenByDog(Dog)
  case stopStressingMeWithYourRules
}
```

Use different error conditions only if there are times when you want to take different actions for each, or catch one error and allow the other to pass through.

## 2. Struct as Errors

Sometimes a single error type is fine for a perticular area of code. The message sent with the exception can distinguish the errors.   


```swift
struct HomeworkError: Error {
  let reason: String
}

func doHomework() throws {
  // ...
  throw HomeworkError(reason: "Eaten by dog: \(dog)")
  // ...
}
```

# Throwing

To indicate that a function can throw an error, you write the `throws` keyword in the function’s declaration. A function marked with `throws` is called a _throwing function_. This is similar to _checked exceptions_ in other languages, like Java. But unlike Java, Swift does not also support unchecked exceptions. Meaning, only throwing functions can propagate errors. Any errors thrown inside a nonthrowing function must be handled inside that function.

```swift
func foo() -> Int { // This function is not permitted to throw. 
func bar() throws -> Int { // This function is permitted to throw.
``` 	

### Source Breaking 

{:.note}
This is somewhat probamatic. Consider a call hierarchy of a large system. Functions at the top call functions below them, which call more functions below them, as so on. If one of the lowest level functions is modified to throw an error, then every function that calls it must also be modified either to catch the error or add the `throws` clause to its signature. And so on. The result is a cascade of changes from the lowest levels to the highest. 

```swift
error: error is not handled because the enclosing catch is not exhaustive
```	


So be careful adding `throws` to existing methods, since it is can be a big source-breaking change. 


### Alternative return values

The checked expection are really alternative return values. The whole idea of exceptions is that an error thrown somewhere way down the call chain can bubble up and be handeled by somewhere futher up, without the intervening code having to worry about it. Checked exceptions require every level of code between the thrower and the catcher to declare they know about the exception that can go through them. This is really no different in practice to returning special return values which the caller had to check for. 

### Scattered error handling

It tends to promote scattered error handling instead of letting errors propagate to a level that actually knows how to handle them. Someprogrammerswillalwaysbetemptedtoincorrectlypeppertheircodewithhandlersthatjustswallow errors instead of correctly propagating them to the right place. This is often worse than useless; it would often be better if the error just propagated silently.


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

As a general principle, don't catch errors unless you know what to do with them. Allow the error to propagate up. Otherwise, catching errors at the wrong level tends to result in code that silently fails without providing any useful feedback to the calling code (and ultimately the user of the software).  The only reasons why a function should have a catch and rethrow mechanism are: 

You want to convert one exception to a different one that is more meaningful to the caller above.
You want to add extra information to the exception.



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



---
layout: post
title:  Deep Dive into Error Handling in Swift
date:   2021-01-18 10:00:00 -0800
category: Blog
tags: Swift
mins: 10
---

Swift has a unique approach to error handling. I will show you 3 aspects of its error handling mechanics: Modeling, Propagating, and Handling. We will also deep dive into the "Typed" and "Marked" error propagations and look into its implications for error design.  

# Modeling Errors

There is no ready-made base `Exception` class in the standard library which you can use in a crunch to throw errors. You have to create custom error types in your application that conforms to the empty `Error` protocol. This seems like an intentional decision by Swift designers to force developers to not be lazy and model errors for their applications. However, you can easily create one of your own. 

```swift
struct Exception: Error { // Error is an empty protocol
  let message: String
}

if timeNow > dueByDate {
  throw Exception(message: "HomeworkError: Not enough time.")
}
```

Generally string error messages are just fine if all you plan to do is log them. You would create different error types when you want callers to take different actions for each error, or catch one error and allow the other to pass through. The problem arises when developers use base `Exception` when they should be creating different types, because creating new error classes seems too heavy a lift. Swift provides a very light-weight way to create errors using `Enums`. 

```swift
enum HomeworkError: Error {  // Also conforms to Error protocol
  case forgot 
  case notEnoughTime
  case eatenByDog(Dog)  // Additional info re: this error
}

if timeNow > dueByDate {
  throw HomeworkError.notEnoughTime
}
```

Now you don't have to create new classes; you just create one error `enum` type for your application and add new cases whenever you have new error types. The `enum` provides a namespace for your application errors. And if you still have a need for those no-action-string-message errors, you can even add a generic case for that -

```swift
enum HomeworkError: Error {
  case forgot 
  case notEnoughTime
  case eatenByDog(Dog)
  case generic(String)  // for no-action-string-message errors

  // Convenience constructor
  init(_ message: String) {
    self = .generic(message)
  }
}

if cantThinkOfAnExcuse {
  throw HomeworkError("Stolen by aliens!")
}
```


# Propagating Errors

Swift requires that for a function to throw an error, you write the `throws` keyword in that function’s declaration. This is called [Typed Propogation][1]. This is similar to _checked exceptions_ in other languages, like Java. But unlike Java, Swift does not also support unchecked exceptions. Meaning, only throwing functions can propagate errors. 

```swift
func gradeHomework(student: Student) { 
  // This function is not permitted to throw
}

func doHomework() throws -> Homework { 
  // This function is permitted to throw
}
```

Swift also requires that the places where you call such error throwing functions, to be _identifiable_ as potential error sites. This is done by "marking" them with `try` keyword. This is called [Marked Propagation][2]. 

```swift
func gradeHomework(student: Student) throws {
  let mathHomework = try student.doHomework() // error site
}
```

In addition to marking the location of potential error, `try` keyword also lets the error propagate out. The `try` can be modified using `try?` and `try!` to _stop_ error propagation right there. 

```swift
let ignoreExcuses: Homework? = try? student.doHomework()  
let itsDoneForSure: Homework = try! aceStudent.doHomework() 
```

The `try?` keyword handles an error by converting it to an optional value. If an error is thrown the value of the expression is `nil`. Whereas, the `try!` keyword asserts that no error will be thrown. If an error actually is thrown, you get a runtime error. In both cases the error is swallowed and doesn't propagate up. 

{:.note} 
Use `try?` and `try!` with caution. Swallowing errors can leave system in an inconsistent state with no record of why. It also makes debugging hard.


# Handling Errors

Finally, to handle errors in Swift, you use the `do-try-catch` block. 

```swift
do {
  let homework = try student.doHomework()
  print("Good job!")
} catch HomeworkError.eatenByDog {
  print("Show me the proof.")
} catch HomeworkError.forgot, HomeworkError.notEnoughTime {  // Combine 2 cases
  print("Do it tomorrow.")
} catch HomeworkError.generic(let message) { // Get addition value re: error
  print(message)
}
```

A `catch` clause can handle individual `enum` cases or the entire `enum` type. Also, if a `catch` clause doesn’t have a pattern, the clause matches any error and binds the error to a local constant named `error`. 

```swift
do {
  let homework = try student.doHomework()
} catch is HomeworkError {   // catches all errors of this type
  print("No excuses. Fail!")
} catch {
  print("Something else happened: \(error)") 
}
```

The `catch` clauses don’t have to handle every possible error that the code in the `do` clause can throw. If none of the `catch` clauses handle the error, the error propagates up. 

# Design Notes

So far we looked at the mechanics of error handling in Swift. The design choice by Swift to use Typed and Marked propagation is interesting. Lets see some of its implications.  


* __Violation of Open/Closed Principle:__ The whole idea of error handling is that an error thrown somewhere way down the call chain can bubble up and be handled by somewhere further up, without the intervening code having to worry about it. Typed and marked propagation requires every level of code between the thrower and the catcher to declare they know that the error can go through them. This means that a change in a low level function to throw an error, can force changes on many higher level functions. These changed functions must be rebuilt and redeployed, even though nothing they care about changed.  

  {:.note}
Be careful about adding `throws` to existing functions, since it is can be a big source-breaking change.

* __Eager Error Handling:__ Since every layer knows about the error, typed and marked propagation tends to promote "eager" error handling. Some programmers will incorrectly handle or just swallow errors instead of letting errors propagate up to a level that actually knows how to handle them. Conversely, if there was unchecked propagation, those errors would just propagated silently, since the programmer is less likely to care that much.

  {:.note}
As a general principle, don't catch errors unless you know what to do with them. Allow the error to propagate up. Otherwise, catching errors at the wrong level tends to result in code that silently fails without providing any useful feedback to the calling code. 

* __Unnecessary Marker:__ The `try` keyword marker seem somewhat unnecessary. Even without this marker, both the compiler and the programmer can know that its a potential error site, given the `throws` keyword in the signature of the called function. Even without this marker, the error can propagate up automatically. However, the `try` marker does allow for two very unique Swift features using `try?` and `try!`.

# Conclusion

I hope this article helped you in understanding exception handling in Swift. Please feel free to contact me if you have any questions or comments. 




[1]: https://github.com/apple/swift/blob/main/docs/ErrorHandlingRationale.rst#id3
[2]: https://github.com/apple/swift/blob/main/docs/ErrorHandlingRationale.rst#id4



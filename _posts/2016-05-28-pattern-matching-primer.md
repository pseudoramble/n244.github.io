---
layout: post
title: A Primer on Pattern Matching
author: pseudoramble
comments: true
---

Ever find yourself writing a `switch` statement and wonder "what if this could do more?" What if it could:

 * catch scenarios that may have been missed,
 * help organize code by constructor type,
 * or detect when a list is a certain size or shape.

If this has ever been you, pattern matching is quite possibly what you've been needing in your day.

## What is pattern matching?

The goal of pattern matching is to detect and handle scenarios that need to be dealt with in respect to some expression. Oftentimes, it's very useful to have your compiler evaluate these structures to help avoid runtime mistakes in your code, if possible. However, pattern matching is ultimately about handling input expressions in certain ways.

Pattern matching can be found in a variety of languages such Haskell, Lisp, Scala, OCaml, and others. For the sake of the following examples, we'll use F#. When we use `match` expressions, you may realize what we mean by `switch` statements that can do much more.

## Working through an example

Let's take a classic boring example, [the Fibonacci sequence](https://en.wikipedia.org/wiki/Fibonacci_number). This is basically a sequence when starting from 1, we take the previous two numbers and add them together. This step is done `n` times.

One method of implementing it may be this:

    let rec fib n =
        if n <= 2
        then 1
        else (fib n - 1) + (fib n - 2)
        
Easy enough. Code like this lends itself to missing edge cases. Like if n < 0, or if n = 0, as some examples. 

Pattern matching can help us here by examining the type of thing we're dealing with in this case, and ensuring we've covered our bases. Here's another attempt at the function above, only using a `match` statement instead:

    let rec fib n =
        match n with
            | 1 -> 1
            | 2 -> 1
            | x when x > 2 -> fib (n - 2) + fib (n - 1)
            | _ -> -1

Here's what's happening:

 * In the first two cases (where `n = 1` or `n = 2`) we just return the constant 1. 
   * This is called a "Constant Pattern".
 * In the next case, we create a new binding `x` in the other scenarios, but "guard" it with a condition that `x > 2` in this case.
   * This is called a "Variable Pattern".
 * In the final case, we know we're not concerned with what was given. So I ignored the value, and returned -1 in this scenario.
   * This is called a "Wildcard Pattern". 
 
The `match` keyword is an expression in F#. That means they yield values, and we can do things like return those values, or assign them to bindings. The `fib` function above always returns the value of the `match` expression.

## What else can these things do?

[F# has a multitude of kinds of patterns it can match](https://msdn.microsoft.com/visualfsharpdocs/conceptual/pattern-matching-%5bfsharp%5d). We won't look at all of them here, but just be aware that making use of F#'s type system can make for a really nice developer experience. But here are a few that I really enjoy:

### Tear apart lists! (List and Cons patterns)
 
 This is a way to break up lists into individual pieces and remaining pieces. This lets you detect things like empty lists, one/two value lists, and more.
 
    match tests with
       | [] -> a :: steps
       | x :: xs when x * x > a -> a :: steps
       | x :: xs -> path a x steps xs

In the example above, we're in the middle of processing a list. 

 * First, we check if the list is empty, and yield our new list with a attached to steps (using the List Pattern)
 * Next, we break up the list into the first element and its remaining pieces (`x` and `xs` respectively using the Cons Pattern)
   * When `x^2 > a` where `a` is a constant defined elsewhere, we create a list with `a` appended to `steps`. 
   * When `x^2 <= a` we call the function `path` to determine our next steps. 

### Null Null-and-Void (Option type pattern)

This is a way to handle the `Option` type, where a value `x` can exist (referred to as `Some x`) or it does not exist `None`. There is no option for a null reference when using an `Option`, but we must explicitly handle the cases.

    type Account = {
        additionalMembers: Option<seq<string>>
        }
        
    let hasPolicy account =
        match account.additionalMembers with
          | Some members -> sprintf "You have %d additional members on your account" <| Seq.length members
          | None -> "You have no additional members"
          
The example above is a bit contrived, but nevertheless illustrates how one might use it.

### Destructing Types

Pattern matching can be used to take apart tuples, records, and discriminated unions. It can be nice to do this as a type parameter, especially if you want to use each field in a function anyway.

In this example, we're taking apart a `PaymentEntry` which is a record type describing a single payment on a mortgage. We can use each part we grabbed from the argument in the variables `m`, `ti`, etc.

    let toPaymentEntryStr { month = m; toInterest = ti; toPrinciple = tp; principleLeft = pl } =
        sprintf "%5d | %-8s | %-9s | %s" m (string ti) (string tp) (string pl)

This makes building up functions that need to examine a bunch of parts of a type easier. The F# compiler also knows what type we're talking about here of course.

## What Next?

If nothing else, I hope you take away from this that pattern matching is a nice language feature. Its intent is to help disassemble data and types, and handle any decisions depending on those data and types. In some cases, the language and compiler can help one avoid major errors (like trying to dereference the null pointer, or working on empty lists).

We've only briefly touched on pattern matching in one language. [You can learn more about F#'s pattern matching here](https://msdn.microsoft.com/visualfsharpdocs/conceptual/pattern-matching-%5bfsharp%5d) and be sure to check out these abilities in other languages (Haskell, Scala, Lisp, more probably!).

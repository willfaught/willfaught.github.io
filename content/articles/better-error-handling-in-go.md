---
categories: ["code"]
date: 2022-08-17T16:49:07-07:00
description: "A design that removes boilerplate code for error and context handling"
tags: ["go"]
title: "Default Error And Context Handling For Go"
---

In 2018, [Russ Cox](https://github.com/rsc), technical leader for the Go Team, [proposed](https://go.googlesource.com/proposal/+/master/design/go2draft-error-handling-overview.md) a new way to handle errors in Go:

>One way that Go programs fail to scale well is in the writing of error-checking and error-handling code. In general Go programs have too much code checking errors and not enough code handling them. (This will be illustrated below.) The draft design aims to address this problem by introducing lighter-weight syntax for error checks than the current idiomatic assignment-and-if-statement combination.

The design was [rejected](https://github.com/golang/go/wiki/Go2ErrorHandlingFeedback) by the community.

In 2019, [Robert Griesemer](https://github.com/griesemer), member of the Go Team, [proposed](https://github.com/golang/go/issues/32437) another new way to handle errors in Go:

>We propose a new built-in function called `try`, designed specifically to eliminate the boilerplate `if` statements typically associated with error handling in Go. No other language changes are suggested. We advocate using the existing `defer` statement and standard library functions to help with augmenting or wrapping of errors. This minimal approach addresses most common scenarios while adding very little complexity to the language. The `try` built-in is easy to explain, straightforward to implement, orthogonal to other language constructs, and fully backward-compatible. It also leaves open a path to extending the mechanism, should we wish to do so in the future.

Robert [decided](https://github.com/golang/go/issues/32437#issuecomment-512035919) to withdraw the proposal after some negative feedback from the community:

>As far as technical feedback, this discussion has helpfully identified some important considerations we missed, most notably the implications for adding debugging prints and analyzing code coverage.
>
>More importantly, we have heard clearly the many people who argued that this proposal was not targeting a worthwhile problem. We still believe that error handling in Go is not perfect and can be meaningfully improved, but it is clear that we as a community need to talk more about what specific aspects of error handling are problems that we should address.

While the particulars of that design were rejected, I agree that the problem he identified is real, and worth solving. I describe a design below that I've been turning over in my head since then.

## Background

### Exceptions in Java

In Java, methods can fail by declaring that they can throw an exception, and then throwing an exception:

```java
void a() throws Exception {
    throw new Exception("s");
}
```

Callers must check for thrown exceptions by explicitly trying to catch them:

```java
try {
    a();
} catch (Exception e) {
    // ...
}
```

Callers can specify code that finally runs after the "try" and "catch" clauses run:

```java
try {
    a();
} catch (Exception e) {
    // ...
} finally {
    // ...
}
```

Code in the "finally" clause may throw an exception, which overrides any exception thrown by the "catch" clause.

Callers can distinguish between different kinds of exceptions:

```java
try {
    a();
} catch (Exception e) {
    if (e instanceof IOException) {
        // ...
    } else {
        // ...
    }
}
```

Callers can compose and decompose exceptions:

```java
try {
    a();
} catch (Exception e) {
    throw new Exception("a: " + e.getMessage(), e);
}
```

Sometimes, a caller can't usefully handle an exception, so it throws it to its caller:

```java
try {
    a();
} catch (Exception e) {
    throw e;
}
```

Where exceptions are thrown extensively, but can't be handled usefully, catching exceptions can be a chore:

```java
try {
    a();
} catch (Exception e) {
    throw e;
}
try {
    b();
} catch (Exception e) {
    throw e;
}
try {
    c();
} catch (Exception e) {
    throw e;
}
```

Many calls, especially in programs that perform lots of I/O, can't usefully handle an exception, and are thereby mired in try/catch boilerplate code, which can be tedious and error-prone to write, and can clutter and obscure the rest of the code.

Fortunately, a method can declare that it too can throw an exception, and default exception handling automatically throws an uncaught exception to its caller:

```java
void x() throws Exception {
    a();
    b();
    c();
}
```

When an exception is thrown, control flow jumps to the nearest encompassing try/catch statement in the call chain (I'm simplifying). Any intermediate calls in the call chain not made inside a try statement are skipped entirely (I'm simplifying). Debugging shows the same behavior.

When deciding whether a method should use default exception handling, an important consideration is whether the caller must know the origin of an exception. In the example above, it only makes sense to omit the try/catch statements if it's not important for the caller to know which method threw the exception. Callers must be judicious in the use of default exception handling. It depends on their needs, and the needs of their callers.

All of this is applicable to Go.

### Errors in Go

In Go, functions can fail by declaring that they can **return an error**, and then **returning an error**:

```go
func a() error {
    return errors.New("s")
}
```

Callers must check for **returned errors** by explicitly **comparing** them:

```go
if err := a(); err != nil {
    // ...
}
```

Callers can specify code that runs after **checking for, and handling, an error**:

```go
defer f()
if err := a(); err != nil {
    // ...
}
```

**Deferred** code may override any **error** returned by an **error handler** using a **named result**.

Callers can distinguish between different kinds of **errors**:

```go
if err := a(); err != nil {
    if e, ok := e.(os.PathError); ok {
        // ...
    } else {
        // ...
    }
}
```

Callers can compose and decompose **errors**:

```go
if err := a(); err != nil {
    return fmt.Errorf("a: %w", err)
}
```

Sometimes, a caller can't usefully handle an **error** itself, so it **returns** it to its caller:

```go
if err := a(); err != nil {
    return err
}
```

Where **errors** are **returned** extensively, but can't be handled usefully, **returning errors** can be a chore:

```go
if err := a(); err != nil {
    return err
}
if err := b(); err != nil {
    return err
}
if err := c(); err != nil {
    return err
}
```

Many calls, especially in programs that perform lots of I/O, can't usefully **handle** an **error**, and are thereby mired in **error handling** boilerplate code, which can be tedious and error-prone to write, and can clutter and obscure the rest of the code.

Unfortunately, Go doesn't have a way for a function to declare that it too can return errors, and for default error handling to automatically return an unhandled error to its caller. This means every function must explicitly handle errors, even if it can't handle errors usefully. This has the same downside: error handling boilerplate code, which can be tedious and error-prone to write, and can clutter and obscure the rest of the code.

Currently, there's no way to write code like this that handles errors in a default way:

```go
func x() error {
    a()
    b()
    c()
}
```

### Summary

In effect, Go already has the equivalent of Java's throw/try/catch/finally functionality:

```go
defer f() // finally
err := a() // try
if err != nil { // catch
    return err // throw
```

Go doesn't need new language features to do these things. Like Java's exceptions, Go's errors are normal values. Unlike Java's special exception handling syntax and semantics, Go's error handling syntax and semantics isn't special; it's normal Go code: ifs, returns, comparisons, type assertions, and so on.

However, Java has default handling and assertions for exceptions, and Go doesn't have comparable things for errors, resulting in lots of boilerplate error handling code. Try and assert calls enable these things in a backward-compatible way that works with existing and new code. They add little clutter to the syntax. They appear close to the function value in a call expression, making it clear how the call works. Their behavior is simple and obvious and similar to exceptions in popular, successful languages like Java or Python.

### Infection of explicit values

Error handling is threading error values up through a stack of function calls, and sometimes inspecting and manipulating them. If one function far down the call stack gives an error that can't be handled there, it should be passed up the stack, and so every intermediate function should also handle errors. Error conditions, reporting, and handling are such ubiquitous concerns that they span many problem domains and code designs. In a sense, errors "infect" code, spreading from function signature to function signature, worming their way into, and inhabiting, most code; they're "cancerous". Code is supposed to proceed in lockstep with error values, checking for non-nil errors at every opportunity to stop, clean up, and return.

There is another type like this: contexts. Context handling is threading context values through a tree of function calls, sometimes inspecting and manipulating them. If one function far up the call stack takes a context, it should be passed down the stack, and so every intermediate function should also handle contexts. Context state, cancellation, and handling are such ubiquitous concerns that they span many problem domains and code designs. In a sense, contexts "infect" code, spreading from function signature to function signature, worming their way into, and inhabiting, most code; they're "cancerous". Code is supposed to proceed in lockstep with the context, checking for cancellation at every opportunity to stop, clean up, and return.

Errors and contexts are two sides of the same coin: threading and handling values up and down the call stack.

We find ourselves in a place where, in the general case, functions' first parameter is a context, and last result is an error:

```go
func(context.Context, ...) (..., error)
```

Like with errors, many functions can't usefully handle a context they're given, only pass it along to callees.

Having to explicitly include these parameters and results, and thread the values down and up through the call chain, is often boilerplate that is tedious to read and to write, and can clutter and obscure the rest of the code.

## The solution

### Default error handling

Default error handling can be enabled by a `?` operator in a call, which is called a **try call**:

```go
func x() error {
    a?()
    b?()
    c?()
}
```

In a try call, the caller's and callee's last (or only) result type must be an error. The callee's results are transformed such that the last (or only) error type is dropped for the caller. For example:

```go
var f = func() (int, error) { return 0, nil }
var n = f?() // returns int, because the type is func() int

var g = func() error { return nil }
g?() // returns nothing, because the type is func()
```

If the callee's error result is not nil, the caller returns it immediately, along with zero values for the other results. Deferred functions in the caller then execute as usual.

### Assertion error handling

In Java, the main method can declare it throws an exception:

```java
public static void main(String[] args) throws Exception {
    a();
}
```

A default handler catches an uncaught exception, prints the exception's details, then exits the program.

In Go, the main function cannot return an error, so it can't use try calls. An equivalent approach is to assert that a function that *can* return an error *won't* in fact return an error, and if it does, then panic with that error.

This can be enabled by an `!` operator in a call, which is called an **assert call**:

```go
func main() {
    a!()
    b!()
    c!()
}
```

Where an assert call is used, the callee's last (or only) result type must be an error. The callee's results are transformed such that the last (or only) error type is dropped for the caller. For example:

```go
var f = func() (int, error) { return 0, nil }
var n = f!() // returns int, because the type is func() int

var g = func() error { return nil }
g!() // returns nothing, because the type is func()
```

If the callee's error result value is not nil, the caller panics with it immediately. Deferred functions in the caller then execute as usual.

Assert calls can be useful in places other than main, where errors should be impossible or are otherwise programmer errors.

##### Control flow

After a try or assert call handles a non-nil error from a callee (up to and including a return or panic), control flow jumps to the execution of deferred functions in the caller, if any, like normal, then jumps to the invocation site in the caller's caller, like normal, and completes the function return. If the function return is for another try or assert call, the process repeats. At the end of a chain of nested try or assert calls, control flow completes the final function return, then executes like normal.

Debugging shows the same behavior.

The intent is for control flow to be equivalent to that of exceptions.

##### Trade-offs

When deciding whether a function should use try calls, an important consideration is whether the caller must know the origin of an error.

```go
func x() error {
    a?()
    b?()
    c?()
}
```

In the example above, it only makes sense to use try calls instead of explicit error handling if it's not important for the caller of `x` to know which method returned the error. Callers must be judicious in the use of try calls. It depends on their needs, and the needs of their callers.

Try calls and normal calls can be mixed and matched to suit the needs of callers:

```go
func x() error {
    a?()
    if err := b(); err != nil {
        return fmt.Errorf("b: %w", err)
    }
    c?()
}
```

##### Other considerations

**Are try calls permitted in function literals?**

Yes, if it has a conforming error result.

**Are try calls permitted in defer statements?**

Yes. If there's an error, it overrides the current error result of the outer function.

**Are try calls permitted in go statements?**

No.

**Are assert calls permitted in defer statements?**

Yes.

**Are assert calls permitted in go statements?**

Yes.

**Do try or assert calls change the order of evaluation for calls?**

No. They are executed in the same order they would have been if they were normal calls. In a deeply nested tree of assert calls, if one returns an error, the entire rest of the call expression, and indeed the entire rest of the function body, will be skipped, like for panics.

**In a nested chain of try calls, how do I determine the origin of a particular error?**

You don't, because you decided it was unimportant when you wrote the code. So, by definition, it's unimportant. See again the Trade-offs section just above. Exceptions in other languages, like Java, have this same trade-off.

**In a nested chain of try calls, how do I debug a particular error?**

See the previous answer. If you want to debug error creation and handling, you must write explicit error handling. It's a trade-off.

**As exit points for a function, are try calls as visible as return statements?**

No, but neither are panics, or function calls that cause panics. The only thing that skimming for return statements tells you is where the writer explicitly decided to return. It doesn't tell you everything about the control flow, or even how all errors are handled (speaking as if try calls are absent).

Try/catch syntax with keywords would be more consistent with return syntax in terms of visibility and structure, but that ship has sailed as far as Go 1 compatibility is concerned. Call operators are backward compatible.

**Call operators would introduce a new token and optionally allow two symbol tokens to appear between a function value and its argument list in a call expression. These are new syntax and semantics, so isn't this automatically bad? Isn't added complexity bad?**

Not necessarily. There have been many discussions about improving Go's error handling, so much so that the Go Team proposed a solution among its first round of Go 2 proposals. Go just added lots of new syntax and semantics for generics, because it was determined to be an important feature, where the pros out-weighed the cons. You have to make the same judgement here, weighing the pros and cons.

# TODO snipped intro

Since Go isn't geared for functional programming, monads and the like are off the table, which leaves implicit state. In this approach, every function has an implicit first parameter for a context and an implicit last result for an error. A function call uses the caller's context as the callee's context, and a function return handles the callee's error result, causing the caller to return the error immediately for a non-nil value, along with zero values for the other results.

We now have implicit context parameters and error results, and default context handling and default error handling between function calls.

A function can still declare an explicit context parameter as the first parameter, or an explicit error result as the last result, which opts it out of default context handling and default error handling, respectively.

Try calls aren't permitted in functions with an implicit error result.

Assert calls aren't permitted for functions with an implicit error result.

A defer statement uses the caller's context for the callee's context, and overrides the caller's implicit error result with the callee's implicit error result if it's non-nil.

A go statement uses the caller's context for the callee's context. The caller's implicit error result is unaffected by the callee's implicit error result.

To override default context handling or error handling, we can use built-in functions.

Overriding default error handling

To report an error, we can use a `throw` built-in function:

```go
var f = func() int {
    throw(errors.New("s"))
}
```

The `throw` function causes the caller to immediately return the error result, along with zero values for the other results, like the try call, but it panics if the error is nil.

To handle an error, we can use a `catch` built-in function:

```go
var n, err = catch(f())
```

The error must be handled explicitly:

```go
if err != nil {
    throw(fmt.Errorf("foo: %w", err))
}
```

The `catch` function expands the signature of a function to include an explicit error result as the last result.

The `throw` function can't be used in a function that has an explicit error result as the last result.

The `catch` function can't be used for a function that has an explicit error result as the last result.

The try call can be used with the catch function:

```go
catch?(f())

f?(g?(), h?())
throw catch f()
try(f())
try f(try g(), try h())
```

When a real Go v2 is possible---when breaking changes are possible---the `throw` and `catch` functions can be converted to keywords:

```go
var f = func() int {
    throw errors.New("s")
}

var n, err = catch f()

if err != nil {
    throw fmt.Errorf("foo: %w", err)
}
```

##### Overriding default context handling

To get the context, we can use a `context` built-in function:

```go
var ctx context.Context = context()
```

To use a context for a function call other than the caller's context, we can use a `with` built-in function:

```go
var old = context()
var new = makeSpecialContext(old)

f(x, y) // f passed old

with(f(new, x, y)) // f passed new

with(func() {
    f(x, y) // f passed new
}(new))

f(x, y) // f passed old
```

The `with` function expands the signature of the called function by prepending an explicit context parameter, opposite to how try calls shrink signatures by dropping an explicit error result. The context value is passed in explicitly.

When a function has an explicit context parameter as the first parameter, the default context handling isn't used within the function. The context must be passed explicitly to function calls, like before. In such a function, to pass a context to functions with implicit context parameters, the `with` function can be used.

The `with` function can't be used for a function that has an explicit context parameter as the first parameter.

When a real Go v2 is possible---when breaking changes are possible---the `with` function can be converted to a keyword:

```go
var old = context()
var new = makeSpecialContext(old)

f(x, y) // f passed old

with f(new, x, y) // f passed new

with func() {
    f(x, y) // f passed new
}(new)

f(x, y) // f passed old
```

##### Call operators

The `catch` and `with` functions could be call operators instead like:

```go
var n, err = f?()

f@(ctx, x, y)
```

However, these calls tend to happen with no or little indentation, so perhaps call operators wouldn't add much value, unlike try calls, which may be nested deeply and clustered closely together. Also, there's a nice symmetry when `throw` and `catch` have similar notation, and also when `throw`, `catch`, and `with` have similar notation.

Just a thought.

##### Examples

In a function with an explicit error result, calling a function with an explicit error result:

```go
func f() error {
    g?()
}
```

In explicit, calling implicit:

```go
func f() error {
    catch?(g())
}
```

In implicit, calling explicit:

```go
func f() {
    if err := g(); err != nil {
        throw(err)
    }
}
```

In implicit, calling implicit:

```go
func f() {
    g()
}
```

In a function with an explicit **context**, calling a function with an explicit **context**:

```go
func f(ctx context.Context) {
    g(ctx)
}
```

In explicit, calling implicit:

```go
func f(ctx context.Context) {
    with(g(ctx))
}
```

In implicit, calling explicit:

```go
func f() {
    g(context())
}
```

In implicit, calling implicit:

```go
func f() {
    g()
}
```

Other:


# !!!!!!!!! TODO

```go
func f() {
    try(os.Stdout.Write([]byte("foo")))
}
```

show error:
catch
hide error:
try call

OS operations:

```go
func main() {
    os.Chdir!("foo")
    var data = os.ReadFile!("bar")
    os.Remove!("bar")
    fmt.Println!(string(data))
}

func main() {
    try os.Chdir("foo")
    var data = try os.ReadFile("bar")
    try os.Remove("bar")
    try fmt.Println(string(data))
}



func f() error {
    var x = try f(try f(), try f())
    
    if err := catch f(); err != nil {
        throw err
    }
    try func() {

    }()
}

in explicit funcs:
    throw: returns with error and zero values
    try: hides and throws explicit and implicit errors
    catch: reveals and returns explicit and implicit errors
in implicit funcs:
    throw: returns with error and zero values
    try: hides and throws explicit and implicit errors
    catch: reveals and returns explicit and implicit errors
```

# TODO auto convert explicit error result into implicit error throw

##### Summary

All functions have an implicit context parameter as the first parameter, and an implicit error result as the last result. Either can be made explicit, in which case the user must handle those values explicitly. The `with` and `catch` functions reveal an implicit context parameter and an implicit error result, respectively. The try call makes an explicit error result implicit, and handles non-nil errors in a default way. The `throw` function uses an error for an implicit error result.

Together, these things enable Go users to weigh the benefits and trade-offs of implicit and explicit context and error handling, and mix and match them as appropriate.

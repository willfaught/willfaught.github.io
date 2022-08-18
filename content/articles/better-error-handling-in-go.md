---
categories: ["code"]
date: 2022-08-17T16:49:07-07:00
description: ""
tags: ["go"]
title: "Better Error Handling in Go"
---

##### Exceptions in Java

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

**All of this is applicable to Go.**

##### Errors in Go

In Go, functions can fail by declaring that they can **return an error**, and then **returning an error**:

func a() error {

    return errors.New("s")

}

Callers must check for **returned errors** by explicitly **comparing** them:

if err := a(); err != nil {

    // ...

}

Callers can specify code that runs after **checking for, and handling, an error**:

defer func() { // ...

if err := a(); err != nil {

    // ...

}

**Deferred** code may override any **error** returned by an **error handler** using a **named result**.

Callers can distinguish between different kinds of **errors**:

if err := a(); err != nil {

    if e, ok := e.(os.PathError); ok {

        // ...

    } else {

        // ...

    }\
}

Callers can compose and decompose **errors**:

if err := a(); err != nil {

    return fmt.Errorf("a: %w", err)

}

Sometimes, a caller can't usefully handle an **error** itself, so it **returns** it to its caller:

if err := a(); err != nil {

    return err

}

Where **errors** are **returned** extensively, but can't be handled usefully, **returning errors** can be a chore:

if err := a(); err != nil {

    return err

}

if err := b(); err != nil {

    return err

}

if err := c(); err != nil {

    return err

}

Many calls, especially in programs that perform lots of I/O, can't usefully **handle** an **error**, and are thereby mired in **error handling** boilerplate code, which can be tedious and error-prone to write, and can clutter and obscure the rest of the code.

**Unfortunately, Go doesn't have a way for a function to declare that it too can return errors, and for default error handling to automatically return an unhandled error to its caller. ****This means every function must explicitly handle errors, even if it can't handle errors usefully. This has the same downside: error handling boilerplate code, which can be tedious and error-prone to write, and can clutter and obscure the rest of the code.**

Currently, there's no way to write code like this that handles errors in a default way:

func x() error {

    a()

    b()

    c()

}

Default error handling

Default error handling can be enabled by a `?` operator in a call, which is called a **try call**:

func x() error {

    a?()

    b?()

    c?()

}

In a try call, the caller's and callee's last (or only) result type must be an error. The callee's results are transformed such that the last (or only) error type is dropped for the caller. For example:

var f = func() (int, error) { return 0, nil }

var n = f?() // returns int, because the type is func() int

var g = func() error { return nil }

g?() // returns nothing, because the type is func()

If the callee's error result is not nil, the caller returns it immediately, along with zero values for the other results. Deferred functions in the caller then execute as usual.

Assertion error handling

In Java, the main method can declare it throws an exception:

public static void main(String[] args) throws Exception {

    a();

}

A default handler catches an uncaught exception, prints the exception's details, then exits the program.

In Go, the main function cannot return an error, so it can't use try calls. An equivalent approach is to assert that a function that *can* return an error *won't* in fact return an error, and if it does, then panic with that error.

This can be enabled by an `!` operator in a call, which is called an **assert call**:

func main() {

a!()

    b!()

    c!()

}

Where an assert call is used, the callee's last (or only) result type must be an error. The callee's results are transformed such that the last (or only) error type is dropped for the caller. For example:

var f = func() (int, error) { return 0, nil }

var n = f!() // returns int, because the type is func() int

var g = func() error { return nil }

g!() // returns nothing, because the type is func()

If the callee's error result value is not nil, the caller panics with it immediately. Deferred functions in the caller then execute as usual.

Assert calls can be useful in places other than main, where errors should be impossible or are otherwise programmer errors.

Control flow

After a try or assert call handles a non-nil error from a callee (up to and including a return or panic), control flow jumps to the execution of deferred functions in the caller, if any, like normal, then jumps to the invocation site in the caller's caller, like normal, and completes the function return. If the function return is for another try or assert call, the process repeats. At the end of a chain of nested try or assert calls, control flow completes the final function return, then executes like normal.

Debugging shows the same behavior.

Trade-offs

When deciding whether a function should use try calls, an important consideration is whether the caller must know the origin of an error.

func x() error {

    a?()

    b?()

    c?()

}

In the example above, it only makes sense to use try calls instead of explicit error handling if it's not important for the caller of `x` to know which method returned the error. Callers must be judicious in the use of try calls. It depends on their needs, and the needs of their callers.

Try calls and normal calls can be mixed and matched to suit the needs of callers:

func x() error {

    a?()

    if err := b(); err != nil {

        return fmt.Errorf("b: %w", err)

    }

    c?()

}

Other considerations

-   Are try calls permitted in function literals?

-   Yes, if it has a conforming error result.

-   Are try calls permitted in defer statements?

-   Yes. If there's an error, it overrides the current error result of the outer function.

-   Are try calls permitted in go statements?

-   No.

-   Are assert calls permitted in defer statements?

-   Yes.

-   Are assert calls permitted in go statements?

-   Yes.

-   Do try or assert calls change the order of evaluation for calls?

-   No. They are executed in the same order they would have been if they were normal calls. In a deeply nested tree of assert calls, if one returns an error, the entire rest of the call expression, and indeed the entire rest of the function body, will be skipped, like for panics.

-   In a nested chain of try calls, how do I determine the origin of a particular error?

-   You don't, because you decided it was unimportant when you wrote the code. So, by definition, it's unimportant. See again the Trade-offs section just above. Exceptions in other languages, like Java, have this same trade-off.

-   In a nested chain of try calls, how do I debug a particular error?

-   See the previous answer. If you want to debug error creation and handling, you must write explicit error handling. It's a trade-off.

-   As exit points for a function, are try calls as visible as return statements?

-   No, but neither are panics, or function calls that cause panics. The only thing that skimming for return statements tells you is where the writer explicitly decided to return. It doesn't tell you everything about the control flow, or even how all errors are handled (speaking as if try calls are absent).
-   Try/catch syntax with keywords would be more consistent with return syntax in terms of visibility and structure, but that ship has sailed as far as Go 1 compatibility is concerned. Call operators are backward compatible.

-   Call operators would introduce a new token and optionally allow two symbol tokens to appear between a function value and its argument list in a call expression. These are new syntax and semantics, so isn't this automatically bad? Isn't added complexity bad?

-   No. There have been many discussions about improving Go's error handling, so much so that the Go Team proposed a solution among its first round of Go 2 proposals. Go just added lots of new syntax and semantics for generics, because it was determined to be an important feature, where the pros out-weighed the cons. You have to make the same judgement here, weighing the pros and cons.

Summary

In effect, Go already has the equivalent of Java's throw/try/catch/finally functionality:

defer f() // finally

err := a() // try

if err != nil { // catch

    return err // throw

Go doesn't need new language features to do these things. Like Java's exceptions, Go's errors are normal values. Unlike Java's special exception handling syntax and semantics, Go's error handling syntax and semantics isn't special; it's normal Go code: ifs, returns, comparisons, type assertions, and so on.

However, Java has default handling and assertions for exceptions, and Go doesn't have comparable things for errors, resulting in lots of boilerplate error handling code. Try and assert calls enable these things in a backward-compatible way that works with existing and new code. They add little clutter to the syntax. They appear close to the function value in a call expression, making it clear how the call works. Their behavior is simple and obvious and similar to exceptions in popular, successful languages like Java or Python.

How deep the rabbit hole goes

(The rest is a second idea that builds upon the first.)

Error is cancerous. It spreads from function call to function call, from function to function, worming its way up through the call chain until it inhabits everything. Some functions return an error just in case it calls something that returns an error, now or in the future. Code is supposed to proceed in lockstep with error values, checking for non-nil errors at every opportunity to stop, clean up, and return.

Context is cancerous. It spreads from function call to function call, from function to function, worming its way down through the call chain until it inhabits everything. Some functions take a context just in case it calls something that takes a context, now or in the future. Code is supposed to proceed in lockstep with the context, checking for cancellation at every opportunity to stop, clean up, and return. If contexts existed when Go started, Reader and Writer would use it.

We find ourselves in a situation where, in the general case, functions' first parameter is a context, and last result is an error:

func(context.Context, ...) (..., error)

Like with errors, many functions can't usefully handle a context they're given, only pass it along to callees.

Having to explicitly include these parameters and results, and thread the values down and up through the call chain, is often boilerplate that is tedious to read and to write, and can clutter and obscure the rest of the code.

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

var f = func() int {

    throw(errors.New("s"))

}

The `throw` function causes the caller to immediately return the error result, along with zero values for the other results, like the try call, but it panics if the error is nil.

To handle an error, we can use a `catch` built-in function:

var n, err = catch(f())

The error must be handled explicitly:

if err != nil {

    throw(fmt.Errorf("foo: %w", err))

}

The `catch` function expands the signature of a function to include an explicit error result as the last result.

The `throw` function can't be used in a function that has an explicit error result as the last result.

The `catch` function can't be used for a function that has an explicit error result as the last result.

When a real Go v2 is possible---when breaking changes are possible---the `throw` and `catch` functions can be converted to keywords:

var f = func() int {

    throw errors.New("s")

}

var n, err = catch f()

if err != nil {

    throw fmt.Errorf("foo: %w", err)

}

Overriding default context handling

To get the context, we can use a `context` built-in function:

var ctx context.Context = context()

To use a context for a function call other than the caller's context, we can use a `with` built-in function:

var old = context()

var new = makeSpecialContext(old)

f(x, y) // f passed old

with(f(new, x, y)) // f passed new

with(func() {

    f(x, y) // f passed new

}(new))

f(x, y) // f passed old

The `with` function expands the signature of the called function by prepending an explicit context parameter, opposite to how try calls shrink signatures by dropping an explicit error result. The context value is passed in explicitly.

When a function has an explicit context parameter as the first parameter, the default context handling isn't used within the function. The context must be passed explicitly to function calls, like before. In such a function, to pass a context to functions with implicit context parameters, the `with` function can be used.

The `with` function can't be used for a function that has an explicit context parameter as the first parameter.

When a real Go v2 is possible---when breaking changes are possible---the `with` function can be converted to a keyword:

var old = context()

var new = makeSpecialContext(old)

f(x, y) // f passed old

with f(new, x, y) // f passed new

with func() {

    f(x, y) // f passed new

}(new)

f(x, y) // f passed old

Call operators

The `catch` and `with` functions could be call operators instead like:

var n, err = f?()

f@(ctx, x, y)

These calls tend to happen with no or little indentation, so perhaps call operators wouldn't add much value, unlike try calls, which may be nested deeply and clustered closely together.

There's a nice symmetry when `throw` and `catch` have similar notation. There's also a nice symmetry when `throw`, `catch`, and `with` have similar notation.

Just a thought.

Examples

In a function with an explicit error result, calling a function with an explicit error result:

func f() error {

    a()

}

In explicit, calling implicit:

func f() error {

}

In implicit, calling explicit:

func f() error {

}

In implicit, calling implicit:

func f() error {

}

In a function with an explicit context, calling a function with an explicit context:

In explicit, calling implicit:

In implicit, calling explicit:

In implicit, calling implicit:

type T struct {

    buffer bytes.Buffer

}

func (t T) Read(p []byte) int {

    var n, err = t.buffer.Read(p)

    if err != nil {

        throw(err)

    }

    return n

}

func f() error {

    os.Stdout.Write?([]byte("foo"))

}

func main() {

    os.Chdir!("foo")

    var data = os.ReadFile!("bar")

    os.Remove!("bar")

    fmt.Println!(string(data))

}

Summary

All functions have an implicit context parameter as the first parameter, and an implicit error result as the last result. Either can be made explicit, in which case the user must handle those values explicitly. The `with` and `catch` functions reveal an implicit context parameter and an implicit error result, respectively. The try call makes an explicit error result implicit, and handles non-nil errors in a default way. The `throw` function uses an error for an implicit error result.

Together, these things enable Go users to weigh the benefits and trade-offs of implicit and explicit context and error handling, and mix and match them as appropriate.

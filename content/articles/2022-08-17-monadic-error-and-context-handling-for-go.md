+++
categories = ["technology"]
date = 2022-08-17T16:49:07-07:00
description = "Error propagation and context passing through the lens of monadic composition"
tags = ["contexts", "design", "errors", "exceptions", "go", "handling", "java"]
title = "Monadic Error And Context Handling For Go"
+++

In 2018, [Russ Cox](https://github.com/rsc) of the [Go](https://go.dev) team [proposed](https://go.googlesource.com/proposal/+/master/design/go2draft-error-handling-overview.md) a new way to handle errors in Go:

>One way that Go programs fail to scale well is in the writing of error-checking and error-handling code. In general Go programs have too much code checking errors and not enough code handling them. (This will be illustrated below.) The draft design aims to address this problem by introducing lighter-weight syntax for error checks than the current idiomatic assignment-and-if-statement combination.

That draft did not become part of the language.

In 2019, [Robert Griesemer](https://github.com/griesemer), another member of the Go team, [proposed](https://github.com/golang/go/issues/32437) another new way to handle errors in Go:

>We propose a new built-in function called `try`, designed specifically to eliminate the boilerplate `if` statements typically associated with error handling in Go. No other language changes are suggested. We advocate using the existing `defer` statement and standard library functions to help with augmenting or wrapping of errors. This minimal approach addresses most common scenarios while adding very little complexity to the language. The `try` built-in is easy to explain, straightforward to implement, orthogonal to other language constructs, and fully backward-compatible. It also leaves open a path to extending the mechanism, should we wish to do so in the future.

Griesemer [announced](https://github.com/golang/go/issues/32437#issuecomment-512035919) that the proposal was declined after community feedback:

>As far as technical feedback, this discussion has helpfully identified some important considerations we missed, most notably the implications for adding debugging prints and analyzing code coverage.
>
>More importantly, we have heard clearly the many people who argued that this proposal was not targeting a worthwhile problem. We still believe that error handling in Go is not perfect and can be meaningfully improved, but it is clear that we as a community need to talk more about what specific aspects of error handling are problems that we should address.

I agree that the problem is real and worth solving. This article proposes call operators that simplify propagation of existing error results and a context scope that supplies context arguments at call sites. Both patterns have counterparts in monadic composition. The aim is to adapt that style of composition to Go's customary context parameter and error result while preserving existing function signatures and the behavior of existing programs.

The proposed additions are just `?`, `!`, and `with`. These are proposed language features, not features available in Go. The background examples use ordinary Java and Go; the design sections use hypothetical Go syntax. Examples omit imports and unrelated helper definitions. Explicit handling remains available wherever a caller needs to inspect, wrap, or recover from an error.

## Background

### Exceptions in Java

In Java, a method can report failure by throwing an exception:

```java
void a() throws Exception {
    throw new Exception("s");
}
```

A checked exception, such as the `Exception` above, must be caught or declared in the caller's `throws` clause. Unchecked exceptions do not have that requirement. A caller that wants to handle the exception can catch it:

```java
try {
    a();
} catch (Exception e) {
    // Handle the exception
}
```

A `finally` block runs when control leaves the `try` statement, including when an exception is propagated:

```java
try {
    a();
} catch (Exception e) {
    // Handle the exception
} finally {
    // Clean up
}
```

If `finally` throws an exception, that exception replaces one already being propagated from the `try` or `catch` block.

Callers can distinguish between kinds of exceptions:

```java
try {
    a();
} catch (Exception e) {
    if (e instanceof IOException) {
        // Handle an I/O failure
    } else {
        // Handle another failure
    }
}
```

They can also wrap an exception while preserving its cause:

```java
try {
    a();
} catch (Exception e) {
    throw new Exception("a failed", e);
}
```

But a caller that only wants to propagate an exception does not need to catch and rethrow it. It can declare the exception and let propagation happen automatically:

```java
void x() throws Exception {
    a();
    b();
    c();
}
```

If `a` throws, `b` and `c` are not called. Execution unwinds through applicable `finally` blocks until a matching handler is reached. The exception can retain its stack trace and cause, so propagation does not inherently lose its origin. Wrapping can still add useful information about what the caller was trying to do.

The useful distinction is between handling a failure and merely passing it to a caller that can handle it.

### Errors in Go

In Go, a function can report failure by returning an error:

```go
func a() error {
	return errors.New("s")
}
```

Callers check the returned value explicitly:

```go
if err := a(); err != nil {
	// Handle the error
}
```

Go's [`defer` statement](https://go.dev/ref/spec#Defer_statements) schedules cleanup for when the surrounding function returns or unwinds during a panic:

```go
func x() error {
	defer cleanup()
	return a()
}
```

The deferred call runs before `x` returns to its caller. A deferred closure can also inspect and modify a named result, including an error result.

Callers can distinguish between kinds of errors. For example, [`errors.As`](https://pkg.go.dev/errors#As) can find a `*os.PathError`, including one inside a wrapped error:

```go
if err := a(); err != nil {
	var pathErr *os.PathError
	if errors.As(err, &pathErr) {
		fmt.Println(pathErr.Path)
	} else {
		// Handle another failure
	}
}
```

Callers can wrap errors while preserving the original error for inspection:

```go
if err := a(); err != nil {
	return fmt.Errorf("a: %w", err)
}
```

Sometimes, a caller cannot usefully handle an error itself, so it returns it unchanged:

```go
func x() error {
	if err := a(); err != nil {
		return err
	}
	if err := b(); err != nil {
		return err
	}
	if err := c(); err != nil {
		return err
	}
	return nil
}
```

Many functions, especially those that perform lots of I/O, have little to add at these intermediate steps. Repeating the same check and return can be tedious and can obscure the rest of the code.

Go lets a function declare an error result, but it does not automatically propagate errors from calls within that function. This version is valid Go, but discards all three errors and reports success:

```go
func x() error {
	a()
	b()
	c()
	return nil
}
```

Go already has the tools to inspect, wrap, propagate, and clean up after errors. What it lacks is a concise way to request the common default: return immediately if a call fails, otherwise continue with its other results.

## Proposed call operators

The design keeps explicit error results and adds two operators at the call site: `?` to propagate an error and `!` to panic on an error.

### Default error handling

A call with `?` is a **try call**:

```go
func x() error {
	a?()
	b?()
	c?()
	return nil
}
```

The caller and callee must each have a final result of type `error`. The operator handles the callee's error result and removes it from the results available to the surrounding expression. It does not change the callee's function type.

For example:

```go
func example() (int, error) {
	f := func() (int, error) { return 42, nil }
	n := f?() // Produces an int; f still has type func() (int, error)

	g := func() error { return nil }
	g?() // Produces no values

	return n, nil
}
```

If the error is nil, execution continues. Otherwise, the enclosing function returns that error immediately, with zero values for its other results. Its deferred functions then execute under the normal rules, including any changes they make to named results.

For a caller returning `(int, error)`, the failure case is equivalent to:

```go
n, err := f()
if err != nil {
	return 0, err
}
```

The operator supplies the repeated check and return. It does not decide whether the error needs wrapping or whether additional cleanup is necessary.

### Assertion error handling

A call with `!` is an **assert call**:

```go
func main() {
	a!()
	b!()
	c!()
}
```

The callee must have a final result of type `error`. On success, that result is removed from the values produced by the call, just as with a try call. On failure, the caller panics with the error. The caller does not need an error result of its own.

```go
func example() int {
	f := func() (int, error) { return 42, nil }
	return f!()
}
```

This could be useful in `main`, which cannot return an error, or where a returned error indicates a programmer mistake. It is a deliberate choice to panic, rather than to propagate an ordinary error result. It is unrelated to Java's `assert` statement.

### Control flow and evaluation

Try calls and assert calls have different failure behavior. A try call returns normally from its enclosing function. An assert call starts a panic, which unwinds the current goroutine and runs its deferred calls. An appropriate deferred `recover` can stop that panic; otherwise the unrecovered panic terminates the program. It does not turn into an ordinary error return at the caller's call site.

Both operators would preserve Go's evaluation-order rules. If an inner call fails, the parts of the enclosing expression that have not yet been evaluated are skipped. For example, assuming suitable parameter and result types:

```go
result := combine?(left?(), right?())
```

If `left` returns an error, neither `right` nor `combine` is called.

Try calls can appear in function literals when the literal has the required final error result. They return from that literal, not from the function that created it.

A direct `go f?()` would not be permitted: a failure in the new goroutine cannot return an error from the function that launched it. A direct `go f!()` would panic in the new goroutine if `f` failed. Calls used to evaluate arguments to a `go` statement still execute in the launching goroutine under Go's ordinary rules.

### Deferred calls

A deferred try call would check the error when the deferred function executes. If the enclosing function already has a non-nil error result, the deferred call's error is ignored: it might be a consequence of the failure already being reported. If the pending error result is nil, a non-nil cleanup error becomes the error result. Other result values are left unchanged.

For example:

```go
func run() error {
	defer cleanup?()
	return work()
}
```

The error-handling behavior would be equivalent to this ordinary Go code, where the error result is named so that the deferred closure can update it:

```go
func run() (err error) {
	defer func() {
		if cleanupErr := cleanup(); err == nil {
			err = cleanupErr
		}
	}()
	return work()
}
```

The source function does not need to name its error result. The deferred `?` call gives the compiler access to the pending result, just as naming it does in the expansion above; the function type remains `func() error`.

Deferred calls still run in reverse registration order. Each deferred try call preserves a non-nil error already selected by the body or a previously executed deferred call. The check does not recover a panic; panic and recovery retain their ordinary behavior.

A deferred assert call would instead panic on a non-nil error when the deferred function executes, as explicitly requested by `!`.

### Trade-offs

A try call is appropriate when returning an error unchanged is enough. When the caller needs to identify the failed operation or take some other action, explicit handling remains useful:

```go
func x() error {
	a?()
	if err := b(); err != nil {
		return fmt.Errorf("b: %w", err)
	}
	c?()
	return nil
}
```

An error value might already identify its origin, or a library might attach a stack trace. The proposed operators would not add that information automatically. This differs from Java exceptions, which normally capture a stack trace when created.

Debugging remains possible with tests, logging, breakpoints, and explicit checks. A debugger could also support stopping at a failed try call, but that would require tooling support; it is not a consequence of the syntax alone.

The operators make possible exits less prominent than a full `return` statement. That is a real trade-off. The question is whether a visible marker at each call site provides enough clarity to justify removing the repeated check-and-return blocks.

These operators introduce syntax without reserving new keywords or changing existing function types. Existing calls retain their behavior; propagation or assertion happens only where a programmer adds an operator. That is the compatibility boundary of this design. A complete proposal would still need to specify the grammar and type-checking rules in detail.

## Context propagation

Errors travel up a call chain. Contexts create a similar pattern in the other direction: a [`context.Context`](https://pkg.go.dev/context) carries deadlines, cancellation signals, and request-scoped values down through calls. Intermediate functions often do little with it beyond passing it along.

Request-oriented APIs commonly have signatures like this:

```go
func work(context.Context, Input) (Output, error)
```

The context parameter supplies an environment for the work, while the error result reports whether it succeeded. Many intermediate functions only need to pass the context down and pass an error back up. Those are two related kinds of boilerplate.

### A context scope

A proposed predeclared function, `with`, would establish a context for calls written inside a function literal:

```go
func load(ctx context.Context) (Value, error) {
	return with(ctx, func() (Value, error) {
		data := read?()
		return parse?(data), nil
	})
}
```

Here, `read` still has an explicit first `context.Context` parameter in its declaration. The compiler supplies `ctx` at the call site, as if the source said `read?(ctx)`. If `parse` has no context parameter, its call is unchanged. Both calls separately opt into error propagation with `?`.

The function literal can also reference `ctx` directly through ordinary lexical scope if it needs to inspect cancellation, derive another context, or otherwise use the context value. No context getter is needed.

`with` would evaluate its context expression once, invoke the parameterless function literal once, and produce that invocation's ordinary results. A try call inside the literal returns from the literal; the outer `return with(...)` passes those results back to `load`'s caller.

Like `append` or `new`, the name `with` could be rebound in an inner scope. The context-passing behavior would apply only to calls that resolve to the predeclared function; calls to a user-defined `with` would follow ordinary call rules.

The predeclared function needs compiler support. An ordinary library function cannot make a call with a missing argument type-check. For this design, the second argument is a function literal whose call expressions the compiler can inspect, rather than an arbitrary function value whose body might be elsewhere.

### Scope and explicit arguments

The scope is lexical: it covers calls written in the literal's body, including nested function literals. It does not alter the bodies of separately defined callees. Those functions still receive their declared parameters and choose how to pass them onward.

A call that is already valid with its written arguments keeps its ordinary meaning. Otherwise, if its first parameter is `context.Context` and inserting the scope's context as the first argument makes the call valid, the compiler supplies that argument. This gives explicitly supplied contexts precedence and leaves calls without context parameters alone. Outside a `with` scope, all arguments remain explicit.

Nested scopes can select a different context. For example, assuming `notify` is declared as `func notify(context.Context, string)`:

```go
with(ctx, func() {
	notify("first")            // Receives ctx
	notify(other, "explicit")  // Receives the explicit other context
	with(other, func() {
		notify("second")       // Receives other
	})
	notify("third")            // Receives ctx again
})
```

The selected context behaves like an ordinary captured value, not mutable goroutine-wide state. The scope does not require an extra parameter or result in any function's type. Context arguments supplied to deferred calls and `go` statements are evaluated and saved under Go's ordinary rules, just as if they had been written explicitly.

At an entry point such as `main` or `init`, code can establish `context.Background()` as the scope's context and use assert calls to panic on returned errors:

```go
func main() {
	with(context.Background(), func() {
		run!()
	})
}
```

These are explicit choices at the entry point, rather than new behavior imposed on existing programs. Passing a context still does not itself stop work when the context is canceled. The functions doing the work must observe cancellation, either directly or through the APIs they call.

## Monadic composition

Go's error results and context parameters are manual versions of two patterns that other languages make easier to compose. An error result resembles a computation that can fail; a context parameter resembles a computation that depends on a shared environment. For routine propagation, I see Go's conventions as worse versions of those abstractions: they encode much of the same structure but leave programmers to write the sequencing rules repeatedly.

### Failure as a composable result

A monad provides operations for embedding an ordinary value in a computation and composing computations while preserving their structure. For error handling, the useful composition rule is simple: on success, pass the value to the next computation; on failure, carry the error forward without running that next computation.

Haskell's [`Either e a`](https://hackage.haskell.org/package/base/docs/Data-Either.html) represents either an error in `Left` or a successful result in `Right`. Its `Monad` instance supplies that sequencing rule through bind, written `>>=`, and `do` notation provides a convenient syntax for using it. The error checks belong to the shared composition operation rather than to every caller.

Rust's [`Result<T, E>`](https://doc.rust-lang.org/std/result/enum.Result.html) has a similar success-or-failure structure, with `and_then` for sequencing successful results. Its [`?` operator](https://doc.rust-lang.org/reference/expressions/operator-expr.html#the-try-propagation-expression) extracts a successful value or returns an error from the enclosing function, with the supported error conversion. Rust does not need Haskell's general `Monad` type class to provide this particular form of composition. Scala's [`Either`](https://www.scala-lang.org/api/2.13.16/scala/util/Either.html) offers another example, with `flatMap` carrying `Left` values forward and applying the next function to `Right` values.

JavaScript's [`Promise`](https://tc39.es/ecma262/multipage/control-abstraction-objects.html#sec-promise-objects) is commonly understood as a monadic abstraction for asynchronous success and failure: `Promise.resolve` lifts ordinary values into promises, and `.then` plays the role of bind. A chain such as:

```javascript
read().then(parse).then(use)
```

passes each fulfilled value to the next function, adopts a promise returned by that function, and carries rejection past success handlers until a rejection handler is reached. `async`/`await` provides another syntax for sequencing promises; a rejected `await` throws, so it propagates unless handled. Promise resolution also adopts objects with a callable `then` method—`then`-able objects. That assimilation and asynchronous scheduling give JavaScript promises their own semantics, so the comparison is about their monadic sequencing rather than a claim that they obey the monad laws for every possible JavaScript value.

Go's `(T, error)` convention lacks a standard result type with comparable composition operations. It supplies two separate values, and callers repeat the success test and propagation themselves. It also differs from `Either` and `Result`: Go APIs can return useful partial results alongside a non-nil error. The proposed try call deliberately discards the callee's other results on failure, so code that needs those partial results should keep an explicit check.

A Go library could provide a result wrapper and composition helpers, but existing APIs would still return separate values. Using the library would require adapters, wrapping and unwrapping values, and often additional callbacks or combinators. That adds the kind of verbosity this design is meant to remove. The operators instead apply the composition rule directly to existing APIs and their established error convention.

### A shared environment

Haskell's [`Reader r a`](https://hackage.haskell.org/package/transformers/docs/Control-Monad-Trans-Reader.html) represents a computation that uses an environment of type `r` to produce an `a`. Its bind operation passes the same environment to the composed computations.

That is close to what Go programmers do by passing a `context.Context` through a call chain. The dependency is present in both designs. The ergonomic difference is that Reader's composition operation threads it through automatically, whereas ordinary Go callers must repeat the argument at each relevant call. A `with` scope supplies the corresponding shortcut for calls in an explicitly selected region of Go code.

The patterns can be combined. A Haskell computation with type `ReaderT Context (Either Error) Value` receives an environment and can fail; running it supplies a `Context` and produces an `Either Error Value`. If it also needs I/O, a type such as `ReaderT Context (ExceptT Error IO) Value` combines the environment, failure, and I/O effects. [`ExceptT`](https://hackage.haskell.org/package/transformers/docs/Control-Monad-Trans-Except.html) supplies the error layer.

This explains why Go's common first-context-parameter, last-error-result signature is more than an arbitrary convention. It combines an environment going in with possible failure coming out, but without a shared abstraction for composing the two. Reader-style environment passing alone does not implement cancellation, just as promise rejection alone does not cancel asynchronous work; those require cooperation from the operations being composed.

### Adapting composition to Go

The proposed syntax adapts both patterns to Go's existing design. A try call supplies the success-or-propagate rule at a marked call site, using the existing final `error` result. An assert call supplies the alternative policy of panicking on failure. A `with` scope supplies a shared context argument to calls that need one.

The context parameter and error result remain explicit in function declarations. The shortcuts operate where the programmer requests them, without adding hidden parameters, changing function types, or requiring adapters around existing APIs. Outside those explicitly selected call sites and scopes, existing programs retain their behavior.

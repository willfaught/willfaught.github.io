---
categories:
- technology
date: 2022-04-02T12:45:03-07:00
description: A design that adds generic functions and types.
tags:
- design
- generics
- go
- languages
- types
title: Generics For Go
---

(Obviously for Go 1.17.)

Why you might find this approach interesting:

- Type variable declarations are implicit
- Type arguments for functions are implicit
- Type variable constraints can be omitted for the empty interface
- Methods can be generic
- Operations are represented with generics as normal methods
- It can simplify the Go language specification

Check out the *More ideas* section at the end for things beyond generics.

## Define comparison operations for all types

For the types that don't yet have them:

- Functions: Compare the corresponding memory addresses. The time complexity is constant.
- Maps: Compare the corresponding `*runtime.hmap` (pointer) values. The time complexity is constant.
- Slices: Compare the corresponding `runtime.slice` (non-pointer struct) values. The time complexity is constant.

Examples:

```go
// Functions

func F1() {}
func F2() {}

var _ = F1 == F1 // True
var _ = F1 != F2 // True

// Maps

var M1 = map[int]int{}
var M2 = map[int]int{}

var _ = M1 == M1 // True
var _ = M1 != M2 // True

// Slices

var S1 = make([]int, 2)
var S2 = make([]int, 2)

var _ = S1 == S1 // True
var _ = S1 != S2 // True

var _ = S1 == S1[:] // True because the lengths, capacities, and pointers are equal
var _ = S1 != S1[:1] // True because the lengths aren't equal
var _ = S1[:1] != S1[:1:1] // True because the capacities aren't equal
var _ = S1 != append(S1, 0)[:2:2] // True because the pointers aren't equal
```

## Define the nil value for all types

The predeclared identifier `nil` is now [assignable](https://go.dev/ref/spec#Assignability) to all types, and is the zero ([nil](https://en.wiktionary.org/wiki/nil#Noun)) value for its type. For example, array, boolean, numeric, string, and structure types all now have a nil value, which is their respective zero value.

Examples:

```go
// Assignment

var _ [N]T = nil
var _ bool = nil
var _ int = nil
var _ string = nil
var _ struct{} = nil

// Conversion

var _ = [N]T(nil)
var _ = bool(nil)
var _ = int(nil)
var _ = string(nil)
var _ = struct{}(nil)

// Comparison

var _ = [N]T{} == [N}T(nil) && [N]T{} == nil // True
var _ = false == bool(nil) && false == nil // True
var _ = 0 == int(nil) && 0 == nil // True
var _ = "" == string(nil) && "" == nil // True
var _ = struct{}{} == struct{}(nil) && struct{}{} == nil // True
```

## Generic abstractions

A **type variable** is a placeholder type that is substituted with another type called a **type argument**. A type variable can appear within type, variable, function, and method declarations; interface methods; and function literals. The type arguments permitted by a type variable must be assignable to an interface called its **constraint**. Type variables look like [identifiers](https://golang.org/ref/spec#Identifiers) with a `$` prefix, and cannot be [blank](https://golang.org/ref/spec#Blank_identifier). The case of the first character has no meaning, although type variables are case-sensitive. Type variables with identical names and constraints are identical.

Examples:

```go
var _ $a
var _ $b
var _ $c
var _ $A
var _ $B
var _ $C
var _ $foo
var _ $BAR
var _ $baz_BOZ
```

### Generic functions

A type variable is bound by the same or nearest outer function signature that contains it in a parameter type. Constraints default to an empty interface if unspecified. Constraints can optionally be specified for some or all type variables in a non-empty, unordered set.

A type variable bound by a function signature is part of its function type. One such type is assignable to another such type if they are identical; or if they are identical after [renaming](https://en.wikipedia.org/wiki/Lambda_calculus#Alpha_equivalence) the type variables in one of them; or if the corresponding constraints are assignable; or if a type variable in the source type consistently corresponds to a type in the target type that isn't a type variable.

To ensure [parametricity](https://en.wikipedia.org/wiki/Parametricity), all type arguments must be valid, and the function value must behave the same regardless of the type argument. A type variable permits use of only constraint methods, assignment operations, comparison operations, method expressions, and method values. A type variable doesn't permit use of type conversions, type assertions, or type switches. Reflection of a type variable reveals only its constraint.

Using type variables:

```go
// $A is constrained by C, which has method M(int) int

var x $A = nil // Valid because x and nil have identical type variables (via assignability)

var _ func(C, int) int = C.M // Method expression
var _ func(int) int = x.M // Method value

var _ = x == x // Valid because x and x have identical type variables
var _ = x == nil // Valid because x and nil have identical type variables (via assignability)
var _ = x == 123 // Invalid because x and 123 don't have identical type variables

var _ = $A(x) // Invalid because type conversions aren't permitted
var _ = C(x) // Same as above
var _ = MyType(x) // Same as above
var _ = interface{}(x) // Same as above
var _ = interface{ M(int) int }(x) // Same as above

var _ = x.($A) // Invalid because type assertions aren't permitted
var _ = x.(C) // Same as above
var _ = x.(MyType) // Same as above
var _ = x.(interface{}) // Same as above
var _ = x.(interface{ M(int) int }) // Same as above

var _ = func() { switch x.(type) {} } // Invalid because type switches aren't permitted
```

Specifying generic functions:

```go
// C, C1, and C2 are constraints
// [...] is a non-empty, unordered set of type variables and constraints

// Function declarations

func F($A) // $A is constrained by interface{}
func F[$A interface{}]($A) // Same as above

func F($A, $B) // $A and $B are constrained by interface{}
func F[$A interface{}]($A, $B) // Same as above
func F[$B interface{}]($A, $B) // Same as above
func F[$A, $B interface{}]($A, $B) // Same as above
func F[$B, $A interface{}]($A, $B) // Same as above

func F[$A C]($A) // $A is constrained by C

func F[$A C]($A, $B) // $A, $B are constrained by C, interface{}, respectively
func F[$B C]($A, $B) // $A, $B are constrained by interface{}, C, respectively

func F[$A C, $B C]($A, $B) // $A, $B are constrained by C
func F[$A, $B C]($A, $B) // Same as above

func F[$A C1, $B C2]($A, $B) // $A, $B are constrained by C1, C2, respectively

func F[$A, $B C1, $C, $D C2]($A, $B, $C, $D) // $A, $B and $C, $D are constrained by C1 and C2, respectively
func F[$D, $C C2, $B, $A C1]($A, $B, $C, $D) // Same as above

func F[$A, $B, $C C]($A, $B, $C) // $A, $B, $C are constrained by C

// Method declarations

func (T) M($A)
func (T) M[$A interface{}]($A)
// ...
// Same as function declarations above

// Interface methods

interface { M($A) }
interface { M[$A interface{}]($A) }
// ...
// Same as function declarations above

// Function literals

func($A) {}
func[$A interface{}]($A) {}
// ...
// Same as function declarations above

// Nested functions

func F(a $A) $A {
    // $A below is bound by the function literal
    return func(a $A) $A { return a }(a)
}

func F(a $A, b $B) ($A, $B) {
    // $A below is bound by the function literal
    // $B below is bound by F
    return func(a $A) ($A, $B) { return a, b }(a)
}

func F(a, b $A) $A {
    choose := func(b bool) $A { // $A here bound by F
        var result $A // $A here bound by F
        if b {
            result = a
        } else {
            result = b
        }
        return result
    }
    return choose(a == b)
}

// Shorthand for combining multiple constraints into one

func F[$A C]($A)
func F[$A interface{ C }]($A) // Same as above

func F[$A interface{ C1; C2 }]($A)
func F[$A (C1, C2)]($A) // Same as above

func F[$A interface{ C1; C2; C3 }]($A)
func F[$A (C1, C2, C3)]($A) // Same as above
```

Using generic functions:

```go
func F(x, y $A) $A {
    return x
}

var _ = F(1, 2) + 3
var _ = F(1i, 2i) + 3i
var _ = F(1.0, 2.0) + 3.0
var _ = F('1', '2') + '3'
var _ = F("1", "2") + "3"
var _ = F(false, true) && false
var _ = F([]int{1}, []int{2})[0]
var _ = F([1]int{1}, [1]int{2})[0]
var _ = F(map[int]int{1:1}, map[int]int{2:2})[1]
var _ = F(make(chan int), make(chan int))->
var _ = F(url.URL{}, url.URL{}).Scheme
var _ = *F(&url.URL{}, &url.URL{})
var _ = F(func(){}, func(){})()
var _ = F(net.IP{}, net.IP{}).String()
```

Type arguments are substituted for type variables using [type inference](https://en.wikipedia.org/wiki/Type_inference), perhaps some form of [unification](https://en.wikipedia.org/wiki/Unification_(computer_science)).

Generic function types:

```go
// Assignable because of identical types

var _ func($A) = func($A) {}
var _ func($A, $B) = func($A, $B) {}

var _ func[$A C]($A) = func[$A C]($A) {}
var _ func[$A C1, $B C2]($A, $B) = func[$A C1, $B C2]($A, $B) {}

// Assignable because of equivalent type variable names

var _ func($A) = func($B) {}
var _ func($A, $B) = func($C, $D) {}

var _ func[$A C]($A) = func[$B C]($B) {}
var _ func[$A C1, $B C2]($A, $B) = func[$C C1, $D C2]($C, $D) {}

// Assignable because of equivalent type variable constraints

var _ func[$A interface{}]($A) = func[$A interface{}]($A) {}
var _ func[$A interface{ M() }]($A) = func[$A interface{}]($A) {}
var _ func[$A interface{ M1(); M2() }]($A) = func[$A interface{ M1() }]($A) {}

// Assignable because of compatible behavior

var _ func(int) = func($A) {}
var _ func(int) int = func(a $A) $A { return a }

func F(g func($A) int, x int) int {
    return g(x)
}

func G($A) int {
    return 123
}

func H(a $A) $A {
    return a
}

var _ = F(G, 456) // == 123
var _ = F(H, 456) // == 456
```

### Generic types

A generic type declaration abstracts the underlying type. It's implied by specifying a non-empty, ordered list of type variables and, optionally, their constraints. Constraints default to an empty interface if unspecified. The identifier for such a declared type is called a **type constructor** when used to construct types, and a **value constructor** when used to construct values. The body cannot be a lone type variable or a lone recursive reference.

Declaring generic types:

```go
// [...] is a non-empty, ordered list of type variables and, optionally, constraints

type T[$A] int // Valid
type T[$A] $A // Invalid
type T[$A] T[$A] // Invalid

type T func($A) // T is not generic, but its underlying type is
type T[$A] func($A) // T is generic, but its underlying type is not
type T[$A] func($B) // T and its underlying type are generic

type T[$A] func($A) // $A is constrained by interface{}
type T[$A interface{}] func($A) // Same as above
type T[$A C] func($A) // $A is constrained by C

type T[$A C, $B C] func($A, $B) // $A and $B are constrained by C
type T[$A, $B C] func($A, $B) // Same as above

// $A and $B are constrained by C1
// $C is constrained by C2
// $D and $E are constrained by C3
// $F and $G are constrained by interface{}
type T[$A, $B C1, $C C2, $D, $E C3, $F, $G] func($A) ($A)
```

Using generic types:

```go
// Generic type construction

var _ A[B]
var _ A[$B]
var _ $A[B]
var _ $A[$B]

var _ A[B, C]
var _ A[B, C, D]

var _ A[B[C[D]]]
var _ A[A[A[B]]]

// Generic parameter types

type List[$A] struct {
    as []$A
}

var _ List // Invalid
var _ List[int] // Valid

func (l List[$A]) First() $A {
    return l.as[0]
}

// Method sets for List[$A] and List[int] are different
func (l List[int]) FirstNegated() int {
    return -l.First()
}

func (l List[$A]) Contains(a $A) bool {
    for _, z := range l.as {
        if z == a {
            return true
        }
    }
    return false
}

func (l List[int]) ContainsNegated(a int) bool {
    return l.Contains(-a)
}

func (l List[$A]) Equivalent(l2 List[$A]) bool {
    if len(l.as) != len(l2.as) {
        return false
    }
    for i := range l.as {
        if l.as[i] != l2.as[i] {
            return false
        }
    }
    return true
}

// Generic value construction

// Zero values should be useful
var _ List[int]
var _ List[int] = List[int]{}

// Invalid currently in this design, although arguably it can work with type inference
var _ = List{[]int{}}
var _ List[int] = List{[]int{}}

func NewListItem(a $A) *List[$A] {
    return &List[$A]{as: []$A{a}}
}

var _ *List[int32] = NewListItem(1)

func NewListItems(as []$A) *List[$A] {
    return &List[$A]{as: as}
}

var _ *List[int] = NewListItems([]int{1, 2, 3})

// Generic value initialization

func (l *List[$A]) Init() {
    l.as = make([]$A, 0, 16)
}

// Generic generic types

func F(f func($T[$A]) $A, x $T[$A]) $A {
    return f(x)
}

var _ int = F(func(xs []int) int { return xs[0] }, []int{123}) // == 123
```

## Generic interface method receivers

Instead of adding a new feature like type sets to enable interfaces to encompass predeclared types, we'll expand the expressive power of interfaces to describe methods that behave equivalent to operations, then declare methods for the operations of predeclared types. This unifies predeclared and user-declared types under methods and interfaces, which simplifies the Go language and user code.

Enable specifying interface method receivers:

```go
var _ interface { M() }
var _ interface { $A M() } // Same as above
var _ interface { ($A) M() } // Same as above
var _ interface { (a $A) M() } // Same as above

var _ interface { $A M($A) }
var _ interface { $A M() $A }
var _ interface { $A M($A) $A }

var _ interface { $A M($B) }
var _ interface { $A M($B) $A }
var _ interface { $A M($B) $B }

var _ interface { MyType M() } // Invalid because it's pointless
var _ interface { $A M($A) } = myValue // Invalid because it's pointless

var _ interface { M($A) } // $A does not match the receiver, it's separate
var _ interface { $B M($A) } // Same as above
```

Now we can do:

```go
type Number interface {
    $A Add($A) $A
}

type MyInt struct {
    my int
}

func (x MyInt) Add(y MyInt) MyInt {
    return MyInt{my: x + y}
}

type MyFloat struct {
    my float64
}

func (x MyFloat) Add(y MyFloat) MyFloat {
    return MyFloat{my: x + y}
}

func _() {
    var myInt = MyInt{1} // OK
    var myFloat = MyFloat{2} // OK
    var num1 Number = myInt // Makes sense
    var num2 Number = myFloat // Makes sense
    var num3 /* ??? */ = num1.Add(num2) // Invalid because the return type is unknown, because Number hides MyInt
    // An interface method with a receiver type variable in a parameter can only be used when its interface is used as a constraint
}

func Add[$A Number](a, b $A) $A {
    return a.Add(b)
}

var _ MyInt = Add(MyInt{1}, MyInt{2})
var _ MyFloat = Add(MyFloat{1}, MyFloat{2})
```

## Generalize and simplify the language

Declare and implement predeclared types and their operations in a core package in the standard library:

```go
package core // import "core"

// Details hidden
type Array[$N, $T] // $N allows integer constants only here
type Bool
type Channel[$T]
type Complex64
type Complex128
type Float32
type Float64
type Int
type Int8
type Int16
type Int32
type Int64
type Map[$K Comparable, $V]
type Rune
type Slice[$T]
type String
type Uint
type Uint8
type Uint16
type Uint32
type Uint64
type Uintptr

type Byte = Uint8
type Rune = Int32

// Operation methods

// ...

// Implementation of methods hidden
func (x Int) Add(y Int) Int
// ...
func (x Int8) Multiply(y Int8) Int8
// ...
func (x Int16) Quotient(y Int16) Int16
// ...
func (x Int32) Subtract(y Int32) Int32
// ...
// ...

func (s Slice[$T]) Append($T) Slice[$T]
func (s Slice[$T]) AppendSlice(Slice[$T]) Slice[$T]
func (s Slice[$T]) Cap() int
func (s Slice[$T]) Copy(Slice[$T]) int
func (s Slice[$T]) Get(int) $T
func (s Slice[$T]) Len() int
func (s Slice[$T]) Set(int, $T)
func (s Slice[$T]) Slice(int, int) Slice[$T]
func (s Slice[$T]) SliceCap(int, int, int) Slice[$T]
// ...

// ...

// Built-in functions

// ...

func (Channel) Cap() int
func (Channel) Close()
func (Channel) Len() int

func Append(Slice[$A], $A) Slice[$A]

func Len[$A interface { Len() int }](a $A) int {
    return a.Len()
}

func Cap[$A interface { Cap() int }](a $A) int {
    return a.Cap()
}

func (Map[$K, $V]) Delete($K)

func (Complex64) Imag() float32
func (Complex64) Real() float32

func (Complex128) Imag() float64
func (Complex128) Real() float64

// ...
```

Declare useful abstractions for those types:

```go
package core // import "core"

// ...

type Comparable interface {
    $N Equals($N) bool
    $N NotEquals($N) bool
}

type Orderable interface {
    Comparable
    $N Greater($N) bool
    $N GreaterEquals[$N Comparable]($N) bool
    $N Less($N) bool
    $N LessEquals[$N Comparable]($N) bool
}

type Number interface {
    Orderable
    $N Add($N) $N
    $N Multiply($N) $N
    $N Quotient($N) $N
    $N Subtract($N) $N
}

type Integer interface {
    Number
    $N Remainder($N) $N
    $N BitAnd($N) $N
    $N BitClear($N) $N
    $N BitLeft(int) $N
    $N BitOr($N) $N
    $N BitRight(int) $N
    $N BitXor($N) $N
}

// ...
```

Incorporate existing types into those new abstractions where applicable:

```go
package big // import "math/big"

// ...

func (*Int) Add(*Int) *Int
// Obviously pick a name other than Add for Number.Add so it doesn't conflict with existing methods, if possible

// ...

package mypackage

// ...

func Add[$N Number](x, y $N) $N {
    return x.Add(y)
}

func _() {
    var x1, y1, z1 big.Int
    var x2, y2, z2 core.Int
    z1 = Add(x1, y1)
    z2 = Add(x2, y2)
    // ...
}
```

Change every package to implicitly dot-import the core package:

```go
package mypackage

import . "core" // Implied, but allowed explicitly

var _ Int // core.Int

type Float32 // ...

var _ Float32 // mypackage.Float32
// Where conflicts arise, local declarations take precedence over core declarations
```

The core package can be qualified:

```go
package mypackage

import "core" // Qualified import, overrides the dot-import

var _ core.Int
```

The qualifier can be specified like usual:

```go
package mypackage

import thecore "core" // Qualified import, overrides the dot-import

var _ thecore.Int
```

Change the predeclared identifiers to refer to the core package where applicable:

```go
// Universe block
// Pseudocode

import "core"

type int = core.Int
type string = core.String
type bool = core.Bool
type int32 = core.Int32
// ...

func append = core.Append // Now a full function value
func len = core.Len // Now a full function value
func cap = core.Cap // Now a full function value
// ...

// ...
```

Optional: Change order operators to be consistent with method semantics:

```go
type B bool

var _ B = 1 < 2 // Invalid now because comparison operations now result in typed booleans

// Automatically fix existing code with "go fix" or "go fmt":

var _ B = B(1 < 2)
```

Change language constructs to use interfaces for user-declared types:

```go
package core // import "core"

// ...

type Iterable[$T] interface {
    Iterator() Iterator[$T]
}

type Iterator[$T] interface {
    HasNext() bool
    Next() $T
}

func (Slice[$T]) Iterator() Iterator[$T]

// ...

package mypackage

func _() {
    var xs []int = // ...

    for x, y := range xs {
        // ...
    } // Emits the usual instructions for iterating slices

    for x, i := 0, xs.Iterator(); i.HasNext(); x++ {
        y := i.Next()
        // ...
    } // Equivalent, but calls methods

    var ys Iterable[int] = MyStruct{/* ... */}

    for x, y := range ys {
        // ...
    } // Emits the method call equivalent if not a built-in iterable type
}

type MyInt struct {
    my int
}

func (m MyInt) Add(m2 MyInt) MyInt {
    return MyInt{my: m.my + m2.my}
}

// func (m MyInt) Subtract(MyInt) MyInt...
// func (m MyInt) Multiply(MyInt) MyInt...
// All core.Number methods...

func _() {
    var _ = MyInt{1}.Add(MyInt{2})
    var _ = MyInt{1} + MyInt{2} // Same as above because MyInt fully implements the core.Number interface
    var _ = 1 + 2 // Emits the usual instructions for adding integers

    // Same deal for all operations:
    // x << y : x.BitLeft(y)
    // x >> y : x.BitRight(y)
    // &^ : x.BitAndNot()
    // x & y : x.BitAnd(y)
    // x[y] : x.Get(y)
    // x[y] = z : x.Set(y, z)
    // &x[y] : x.GetAddressable(y)
    // ...
}
```

Emit efficient code for core methods corresponding to operations via function inlining or direct instructions:

```go
var n1, n2 core.Int = 1, 2
var n3 core.Int = n1 + n2
var n4 core.Int = n1.Add(n2) // Same implementation as above, no method call under the hood
var n5 int = 1.Add(2) // Same implementation as above, roughly, no method call under the hood
```

Add generic type aliases:

```go
type Map[$K Comparable, $V] struct {
    ks []$K
    vs []$V
}

type Dictionary = Map

var _ Dictionary[int, string]

type Callback[$A] = func() $A
type Coordinate[$A Number] = [2]$A
type Counter[$A Comparable] = Map[$A, Int]
type List[$A Orderable] = []$A
type Reference[$A] = *$A
type Set[$A Comparable] = Map[$A, struct{}]
```

Simplify the language specification by moving descriptions of semantics now specified by generics and the core package into the core package documentation where applicable:

```go
package core // import "core"

// ...

// Uint8 is the set of all unsigned 8-bit integers (0 to 255).
// The value of an n-bit integer is n bits wide and represented using two's complement arithmetic.
// ...
type Uint8

// Integer...
// ...
// For two integer values x and y, the integer quotient q = x / y and remainder r = x % y satisfy the following relationships:
//
//     x = q*y + r  and  |r| < |y|
//
// with x / y truncated towards zero ("truncated division").
// ...
type Integer interface {
    // ...
}

// Orderable...
// Comparison operators compare two operands and yield a typed boolean value.
// In any comparison, the first operand must be assignable to the type of the second operand, or vice versa.
// The equality operators == and != apply to operands that are comparable.
// ...
type Orderable interface {
    Comparable
    $N Greater($N) Bool
    $N GreaterEquals[$N Comparable]($N) Bool
    $N Less($N) Bool
    $N LessEquals[$N Comparable]($N) Bool
}

// ...
```

Go spec:

```
The Go Programming Language Specification

[...]

Integer literals

[...] Literals are syntactic sugar for values of types that implement the Integer interface in the core package. See that package for more information. [...] [Or you could list all the possible types that literals can represent, whatever presents best.]
```

That's it for generics. There might be more that could be done to simplify and generalize Go with generics, but not without horribly breaking compatibility.

## More ideas

While I have you, here are some additional ideas for how to strengthen Go.

### Sum types

Go has [product types](https://en.wikipedia.org/wiki/Product_type):

```go
type T struct {
    X int
    Y rune
    Z string
} // A value of T has a value for each field at a time

// NumPossibleValues(T) = NumPossibleValues(int) * NumPossibleValues(rune) * NumPossibleValues(string)
// Note the "*" for "product"
```

Go doesn't have [sum types](https://en.wikipedia.org/wiki/Tagged_union):

```go
type T sum {
    X int
    Y rune
    Z string
} // A value of T has a value for one field at a time

// NumPossibleValues(T) = NumPossibleValues(int) + NumPossibleValues(rune) + NumPossibleValues(string)
// Note the "+" for "sum"
```

Other names (more or less) for sum types are coproduct types, tagged unions, and algebraic data types.

Two useful features of sum types:

- Each field has a name to distinguish it, so there can be multiple fields with the same type
- Fields can't be added, changed, or removed without changing the meaning of the type, so code using the type can know which fields to expect

To see a need for sum types, look at how [go/ast.Expr](https://cs.opensource.google/go/go/+/refs/tags/go1.18:src/go/ast/ast.go;l=41) uses an unexported, useless method to limit which types can implement it. It does this so go/ast code only has to handle go/ast types. Grammars, enumerations, and other types that have distinct cases can all be modeled well by sum types. All built-in Go types are sum types that have multiple cases: all the numbers for numeric types, nil and non-nil channels/maps/slices/functions/pointers, empty and non-empty strings, etc.

Here's a way to add user-declared sum types to Go:

```go
type Expr switch {
    case Index struct {
        X *Expr
        Lbrack token.Pos
        Index *Expr
        Rbrack token.Pos
    }
    case KeyValue struct {
        Key *Expr
        Colon token.Pos
        Value *Expr
    }
    default Bad struct { // The default case is the zero/nil value
        From, To token.Pos
    }
}

func F(e Expr) Expr {
    switch e {
    case Expr.Index{}:
        // e has type Expr here
    case Expr.KeyValue{}:
        // e has type Expr here
    case Expr.Bad{}:
        // e has type Expr here
    default:
        // e has type Expr here
    }

    switch x := e {
    case Expr.Index{}:
        // x has type struct {
        //     X *Expr
        //     Lbrack token.Pos
        //     Index *Expr
        //     Rbrack token.Pos
        // } here
    default:
        // x has type Expr here
    }

    switch e {
    case Expr.Index{}: // Zero value for the case
    case Expr.Index{nil, 0, nil, 0}: // Same as above
    case Expr.Index{X: nil, Lbrack: 0, Index: nil, Rbrack: 0}: // Same as above
    }

    switch e { // "case var" matches patterns of literals or variables
    case var Expr.Index{}: // Any Expr.Index value
    case var Expr.Index{x, l, i, r}: // Same as above
        // x has type *Expr here
    case var Expr.Index{X: x, Lbrack: 55, Index: i, Rbrack: 75}:
        // x has type *Expr here
    }

    var Expr.Index{x, l, i, r} = e
    // x has type *Expr here

    // Structures are consistent:
    type S struct { X int }
    var _ = S{}
    var _ S = S{}
    var S{x} = S{}
    var S{x} S = S{}
    var S{X: x} = S{}
    var S{X: x} S = S{}
    var S{X: _} = S{}
    var S{X: _} S = S{}
    switch (S{}) {
    case var S{}: // Any S value
    case var S{x}: // Same as above
        // x has type int here
    case var S{_}: // Same as above
    case var S{X: x}: // Same as above
        // x has type int here
    case var S{X: _}: // Same as above
    case S{55}:
    }

    return Expr.Bad{123, 456}
}

var _ switch {
    case Red // The type defaults to struct{}
    case Green
    default Blue
} // Value is Blue{}

var _ switch {
    case Red
    case Green
    default Blue
} = switch {
    case Red
    case Green
    default Blue
}.Red{} // Value is Red{}

// Syntactic sugar for sum types
type Color {
    case Red
    case Green
    default Blue
}

// Syntactic sugar for sum-of-product types
type Expr {
    case Index {
        X *Expr
        Lbrack token.Pos
        Index *Expr
        Rbrack token.Pos
    }
    case KeyValue {
        Key *Expr
        Colon token.Pos
        Value *Expr
    }
    default Bad {
        From, To token.Pos
    }
}
```

Here's a way to adapt built-in core types into sum types where applicable:

```go
package core // import "core"

// ...

type Pointer[$T] {
    case Pointer {
        Value $T
    }
    default Nil
}

var _ *Int = &Int{}
var _ Pointer[Int] = &Int{}

func _() {
    var p *Int = // ...
    switch p {
    case nil:
    case Pointer[Int].Nil{}: // Same as above
    case Pointer[Int].Pointer{123}: // Forces dereference to compare values
    case var Pointer.Pointer{v}: // Forces dereference to assign value to v
        // v has type int here
    }
}

// For all built-in nil-able types:

type Map[$K Comparable, $V] {
    case Map // Details hidden
    default Nil
}
// ...

var _ map[int]int // == nil == Map[int, int].Nil{}
var _ Map[Int, Int] // == nil == Map[Int, Int].Nil{}
```

### Tuple types

Semantics:

```go
package core // import "core"

type Tuple0 struct{} // The unit type, the tuple equivalent of struct{}

// No Tuple1

type Tuple2[$A, $B] struct {
    A $A
    B $B
}

type Tuple3[$A, $B, $C] struct {
    A $A
    B $B
    C $C
}
// ...

type Tuple20 // ...

package mypackage

func _() {
    var _ = Tuple0{}
    var _ Tuple0 = Tuple0{}

    var x = Tuple2[int, int]{1, 2}
    var x Tuple2[int, int] = Tuple2[int, int]{1, 2}

    _, _ = x.A, x.B
    x.A, x.B = 3, 4 // x is changed

    switch x {
    case var Tuple2{y, z}:
        y, z = 0, 0 // x is unchanged
    case var Tuple2{y, _}:
    case var Tuple2{_, z}:
    case var Tuple2{_, _}:
    case var Tuple2{1, _}:
    case var Tuple2{_, 2}:
    case Tuple2{1, 2}:
    }

    switch i := interface{}(x); i.(type) {
    case Tuple2[int, int]:
    }

    type T Tuple2[int, int]
    var _ = T{1, 2}
}
```

Syntactic sugar:

```go
func _() {
    var _ = {} // Tuple0
    var _ {} = {} // Same as above

    var x = {1, 2} // Tuple2
    var x {int, int} = {1, 2} // Same as above

    switch x {
    case var {y, z}:
        y, z = 0, 0
    case var {y, _}:
    case var {_, z}:
    case var {_, _}:
    case var {1, _}:
    case var {_, 2}:
    case {1, 2}:
    }

    switch i := interface{}(x); i.(type) {
    case {int, int}:
    }

    type T {int, int}
    var _ = T{1, 2}
}
```

Constructing and deconstructing tuples:

```go
func _() {
    var {x, y} = {1, 2} // New variables x, y are assigned 1, 2, respectively
    var {x2, y2} {int, int} = {1, 2} // New variables x2, y2 are assigned 1, 2, respectively

    var {x3, y3} = {1, 2}
    var {x4, _} = {1, 2}
    var {_, y4} = {1, 2}
    var {_, _} = {1, 2}

    {x, y} = {1, 2}
    {x, _} = {1, 2}
    {_, y} = {1, 2}
    {_, _} = {1, 2}
}
```

Functions now take and give tuples where applicable:

```go
func T(x, y int) (int, int) {
    return {x, y}... // Same as "return x, y"
    // "..." deconstructs a tuple into multiple result values in this context
}

func _() {
    var t {int, int} = T(1, 2)... // "..." constructs a tuple from multiple result values in this context
    T(t...) // "..." deconstructs a tuple into multiple argument values in this context

    var {x1, y1} {int, int} = T({1, 2}...)...

    var x2, y2 = T(1, 2)
    var x3, y3 = {1, 2}...

    x3, y3 = T(1, 2)
    x3, y3 = {1, 2}...
}
```

Sum types work nicely with tuple types:

```go
type Expr switch {
    case Add {*Expr, *Expr}
    case Sub {*Expr, *Expr}
    default Num int
}

func Eval(e Expr) Expr {
    switch e {
    case var Expr.Add{l, r}:
        var Expr.Num{x}, Expr.Num{y} = Eval(*l), Eval(*r)
        return Expr.Num{x + y}
    case var Expr.Sub{l, r}:
        var Expr.Num{x}, Expr.Num{y} = Eval(*l), Eval(*r)
        return Expr.Num{x - y}
    case var Expr.Num{_}:
        return e
    }
}

var x, y, z Expr
var _ = Eval(Expr.Add{&x, &y}) == z // True
```

### Represent function signatures with structures

Last one. Let's get crazy.

Notice that structures:

```go
type Args struct {
    x, y int
    f, g float32
    s, t string
}
```

look like function signatures:

```go
func Args(
    x, y int,
    f, g float32,
    s, t string,
)
```

That's kinda weird, isn't it? Shouldn't different things be different, if we want feature orthogonality?

What happens if you make a function signature like a structure type, or vice versa?

You can have vararg structure values:

```go
type OneOrMore struct {
    One int
    More ...int
}

var _ = OneOrMore{1, 2, 3, 4}
var _ = OneOrMore{1, myInts...}
```

That's kinda cool.

You can have compact, one-line structure types like function signatures:

```go
type Args struct{ x, y int, f, g float32, s, t string }
```

That's kinda nice.

You can have value constructors that can act like real functions:

```go
var _ = Args{1, 2, 3.4, 5.6, "7", "8"} // Equivalent to:
var _ = Args(1, 2, 3.4, 5.6, "7", "8") // Looks like a function call, but implemented like Args{...}
var _ func(int, int, float32, float32, string, string) Args = Args // Acts like a function value if not called immediately
```

That's kinda neat.

You can use structures as bulk arguments and results:

```go
func F(x, y int) (z int, err error)

func _() (int, error) {
    var arg struct{ x, y int }
    var result struct{ z int, err error }
    arg.x = 1
    arg.y = 2
    result = F(arg...)... // "..." adapts between structures and signatures
    z := result.z
    err := result.err
    if z > 0 {
        return z + 1, err
    }
    return result...
}
```

That's kinda swell.

You can have keyword arguments:

```go
package mine

func F(x, y int) error

func G(X, Y int) error

func H(x, y int, MetricUnits bool, Length float64) error

var _ = F(1, 2)
var _ = F(x: 1, y: 2)

var _ = G(1, 2)
var _ = G(X: 1, Y: 2)

var _ = H(1, 2, true, 23.45)
var _ = H(x: 1, y: 2, MetricUnits: true, Length: 23.45)

package yours

import "mine"

var _ = mine.F(1, 2) // Can't use x, y keywords because they're unexported

var _ = mine.G(1, 2)
var _ = mine.G(X: 1, Y: 2)

var _ = mine.H(1, 2, true, 23.45)
var _ = mine.H(1, 2, MetricUnits: true, Length: 23.45) // Can't use x, y keywords because they're unexported
```

That's kinda interesting.

You can have no arguments mean the zero value for the function signature "structure" type:

```go
func F(x, y int) error

var _ = F(0, 0)
var _ = F(struct{ x, y int }{0, 0}...) // Same as above
var _ = F(struct{ x, y int }{}...) // Same as above
var _ = F() // Same as above
```

You can have a hybrid model, where zero or more arguments are specified positionally from left to right, and then zero or more of the remaining arguments are specified by keyword arguments:

```go
func H(x, y int, MetricUnits bool, Length float64) error

var _ = H()
var _ = H(1)
var _ = H(1, 2)
var _ = H(1, 2, true)
var _ = H(1, 2, true, 23.45)
var _ = H(1, 2, true, Length: 23.45)
var _ = H(1, 2, MetricUnits: true, Length: 23.45)
var _ = H(1, y: 2, MetricUnits: true, Length: 23.45)
var _ = H(x: 1, y: 2, MetricUnits: true, Length: 23.45)
var _ = H(Length: 23.45, MetricUnits: true, y: 2, x: 1)

type J struct { x, y int, MetricUnits bool, Length float64 }

var _ = J{}
var _ = J{1}
var _ = J{1, 2}
var _ = J{1, 2, true}
var _ = J{1, 2, true, 23.45}
var _ = J{1, 2, true, Length: 23.45}
var _ = J{1, 2, MetricUnits: true, Length: 23.45}
var _ = J{1, y: 2, MetricUnits: true, Length: 23.45}
var _ = J{x: 1, y: 2, MetricUnits: true, Length: 23.45}
var _ = J{Length: 23.45, MetricUnits: true, y: 2, x: 1}
```

While `H()` looks perhaps unsettling, is it really any different than `J{}`? If we can't be trusted to write `H()`, then shouldn't we not be trusted to write `J{}` either?

This could help alleviate the need for [optional parameters](https://dave.cheney.net/2014/10/17/functional-options-for-friendly-apis). Is it really any better to add an option structure parameter to your function that you add a dozen new fields to over time, instead of adding a dozen new optional parameters over time? At least this way, all the parameters and documentation are in one place, and adding new parameters at the end of the signature is backwards compatible.

This would also remove the need to avoid positional notation for structure literals, since currently they break when you add a new field.

I don't know if this is a good idea overall, but it seems interesting.

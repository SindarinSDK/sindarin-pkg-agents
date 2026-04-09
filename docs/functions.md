# Sindarin Functions & Lambdas

## Functions

```sindarin
# Single expression
fn add(a: int, b: int): int => a + b

# Multi-line
fn greet(name: str): void =>
    println($"Hello {name}")

# No parameters
fn now(): int => 0

# Returning arrays
fn range(start: int, count: int): int[] =>
    var result: int[] = {}
    for i in 0..count =>
        result.push(start + i)
    return result
```

## Lambdas

```sindarin
# Typed lambda variable
var add: fn(int, int): int = fn(a: int, b: int): int => a + b

# Single param
var double_it: fn(int): int = fn(x: int): int => x * 2

# No params
var greet: fn(): str = fn(): str => "hello"

# Calling
var result: int = add(3, 4)
```

Lambdas capture variables from their enclosing scope by reference. See `closures.md` for capture semantics, mutation, and struct field behavior.

## Recursion

Recursion works naturally — a function can call itself or other functions that call it back (mutual recursion). The compiler resolves all top-level functions before codegen, so call order doesn't matter.

## Native Functions

Declared with `native fn`, these map to a C function. See `native-interop.md` for the full mapping rules.

```sindarin
@alias "sin"
native fn sin(x: double): double

# Variadic: trailing ... declares a C variadic
native fn my_printf(fmt: str, ...): int

# Expression-bodied native (compiled as an inline C function)
native fn double_it(x: int): int => x * 2
```

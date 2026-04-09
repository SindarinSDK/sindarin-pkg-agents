# Sindarin Program Structure

## Entry Point

Every program needs a `main` function. Two signatures are valid:

```sindarin
fn main(): void =>
    println("hello")

fn main(): int =>
    println("hello")
    return 0
```

## Comments

```sindarin
// line comment
# also a line comment
```

## Global Variables

Variables declared at the top level (outside any function) are globals:

```sindarin
var counter: int = 0
var name: str = "default"

fn main(): void =>
    counter = counter + 1
```

Globals with constant initializers are set at program start. Globals with runtime initializers (function calls, computed values) are initialized at the top of `main()` in declaration order.

## Forward Declarations

Functions can be called before they are defined in the same file. The compiler resolves all top-level functions before generating code:

```sindarin
fn main(): void =>
    println(greet("world"))    # works — greet is defined below

fn greet(name: str): str => $"hello {name}"
```

## Return Statements

Use `return` to exit a function with a value:

```sindarin
fn classify(n: int): str =>
    if n == 0 => return "zero"
    if n < 10 => return "small"
    return "large"
```

Void functions can use bare `return` for early exit:

```sindarin
fn log(msg: str): void =>
    if msg.isBlank() => return
    println(msg)
```

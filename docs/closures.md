# Sindarin Closures

Lambdas capture variables from their enclosing scope **by reference**. The closure reads and writes the original variable, not a copy.

## Basic Capture

```sindarin
var multiplier: int = 3
var times: fn(int): int = fn(x: int): int => x * multiplier
times(5)    # 15
```

## Multiple Captures

```sindarin
var x: int = 10
var y: int = 20
var sum_xy: fn(int): int = fn(z: int): int => x + y + z
sum_xy(5)   # 35
```

## Mutation Through Closures

Closures mutate the captured variable directly:

```sindarin
var count: int = 0
var inc: fn(): int = fn(): int =>
    count = count + 1
    return count

inc()          # 1
inc()          # 2
println(count) # 2 — outer variable changed
```

Multiple closures can share the same captured variable:

```sindarin
var val: int = 0
var inc: fn(): int = fn(): int => val = val + 1
var dec: fn(): int = fn(): int => val = val - 1
var get: fn(): int = fn(): int => val
```

## Capture in Loops

The closure captures the loop variable's current value at each iteration:

```sindarin
for var i: int = 0; i < 3; i++ =>
    var capture_i: fn(): int = fn(): int => i
    println(capture_i())   # prints 0, 1, 2
```

## Closures as Struct Fields

Function-typed fields are stored as heap-allocated closures. On struct copy, closures are deep-copied. On struct drop, closures are freed automatically.

```sindarin
struct Handler =>
    callback: fn(int): int

var h: Handler = Handler { callback: fn(x: int): int => x * 2 }
h.callback(5)   # 10
```

## Closures and Threads

Closures can be passed to spawned threads. When a struct with closure fields is passed to `&fn()`, the thread borrows the struct — the caller retains ownership and cleanup runs after join.

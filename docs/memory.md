# Sindarin Memory Semantics

## as ref / as val

```sindarin
var s: MyStruct as ref         # heap-allocated, pointer semantics
var s: MyStruct as val         # stack-allocated, value semantics (default)

struct Config as ref =>        # ALL instances are heap-allocated
    host: str
    port: int

struct Point as val =>         # ALL instances are stack-allocated
    x: double
    y: double

fn process(data: str as ref): void     # parameter by reference
fn getData(): Config as val            # return by value
```

**Rule**: `as ref` params require an lvalue at the call site (a variable or field, not a literal or expression).

## Pointer Restrictions

Pointer types (`*T`), `addressOf()`, and pointer returns are **only allowed in `native fn`** declarations. Regular Sindarin functions cannot accept or return raw pointers. Use `as ref` for indirection in non-native code.

## copyOf / addressOf / valueOf

```sindarin
var p: Point = Point { x: 1.0, y: 2.0 }
var q: Point = copyOf(p)               # deep copy

var x: int = 42
var ptr: *int = addressOf(x)           # get pointer
var val: int = valueOf(ptr)            # dereference pointer
```

## Return Ownership

When a local variable is returned, the compiler transfers ownership to the caller and nullifies the local so the scope cleanup doesn't double-free. You can return locally-allocated structs, strings, and `as ref` handles freely — don't write manual "move" code.

```sindarin
fn makeNode(v: int): Node as ref =>
    var n: Node = Node { value: v }
    return n                     # ownership moves to caller; local n nullified
```

## Global Variables

Globals with compile-time-constant initializers are initialized at program start. Globals whose initializer is a runtime expression (function calls, struct literals with computed fields, etc.) are initialized at the **top of `main()`**, in declaration order. Do not reference one runtime-initialized global from another's initializer unless the declaration order is correct.

## Scoped Cleanup (using/dispose)

```sindarin
struct Resource =>
    name: str

    fn dispose(): void =>
        # cleanup logic here

using r = Resource { name: "db" } =>
    r.doWork()
# r.dispose() called automatically when block exits
```

The struct must have a `fn dispose(): void` method.

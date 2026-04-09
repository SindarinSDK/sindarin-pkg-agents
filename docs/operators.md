# Sindarin Operators

## Arithmetic

| Op | Description |
|----|-------------|
| `+` | Add (also string concatenation) |
| `-` | Subtract |
| `*` | Multiply |
| `/` | Divide |
| `%` | Modulo |

## Comparison

| Op | Description |
|----|-------------|
| `==` | Equal |
| `!=` | Not equal |
| `<` | Less than |
| `>` | Greater than |
| `<=` | Less or equal |
| `>=` | Greater or equal |

## Logical

| Op | Description |
|----|-------------|
| `&&` | Logical AND (short-circuit) |
| `\|\|` | Logical OR (short-circuit) |
| `!` | Logical NOT |

## Increment / Decrement

```sindarin
var x: int = 5
x++     # post-increment (x is now 6)
x--     # post-decrement (x is now 5)
++x     # pre-increment (x is now 6)
--x     # pre-decrement (x is now 5)
```

## Compound Assignment

Works on variables, array elements, and struct fields:

```sindarin
var a: int = 10
a += 5       # 15
a -= 3       # 12
a *= 2       # 24
a /= 4       # 6
a %= 4       # 2

arr[0] += 10
point.x -= 1

# String concatenation
var s: str = "Hello"
s += " World"    # "Hello World"
```

## Unary

| Op | Description |
|----|-------------|
| `-x` | Negation |
| `!x` | Logical NOT |

## Thread Operators

| Op | Description |
|----|-------------|
| `&fn()` | Spawn thread |
| `r!` | Sync (wait for thread) |
| `r~` | Detach thread |

See `threading.md` for details.

## Special Operators

| Op | Description |
|----|-------------|
| `..` | Range (exclusive end): `0..10` |
| `...arr` | Spread: expand array in literal |
| `as ref` / `as val` | Memory qualifier (NOT a cast) |

## Operator Overloading

Structs can define custom `==` and `<`. See `structs.md`.

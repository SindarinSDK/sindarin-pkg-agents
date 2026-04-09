# Sindarin Strings & Arrays

## String Literals

```sindarin
var greeting: str = "Hello, World!"
var empty: str = ""
var ch: char = 'A'                   # char uses single quotes
```

### Escape Sequences

| Escape | Character | Escape | Character |
|--------|-----------|--------|-----------|
| `\n` | Newline | `\\` | Backslash |
| `\t` | Tab | `\"` | Double quote |
| `\r` | Carriage return | `\'` | Single quote |
| `\0` | Null | | |

### Interpolation (`$"..."`)

```sindarin
var name: str = "world"
var msg: str = $"Hello {name}!"
var calc: str = $"Sum: {a + b}, Product: {a * b}"
```

Expressions inside `{}` are evaluated and stringified. Works with any type.

### Multi-line Blocks (`|` and `$|`)

Use `|` for raw multi-line strings, `$|` for interpolation. Leading whitespace is stripped based on the minimum indentation; inner relative indentation is preserved. The block ends when a line is indented less than the opening `|`.

```sindarin
var sql: str = |
    SELECT id, name
    FROM users
    WHERE active = true

var name: str = "Alice"
var greeting: str = $|
    Hello {name}!
    You are {age} years old.
```

## String Methods

```sindarin
s.length                    # character count (property, not a call)
s.trim()                    # strip whitespace from both ends
s.toLower() / s.toUpper()
s.substring(start, end)     # [start, end) extraction
s.charAt(i)                 # char at index
s.indexOf("sub")            # position or -1
s.contains("sub")
s.startsWith("pre") / s.endsWith("suf")
s.replace("old", "new")     # all occurrences
s.split(",")                # → str[]
s.splitWhitespace()         # split on any whitespace
s.splitLines()              # split on \n, \r\n, \r
s.isBlank()                 # empty or whitespace-only
s.toBytes()                 # → byte[] (UTF-8)
```

### `append` (mutating, must reassign)

`append` appends in place but may reallocate the buffer. **Always reassign the return value.**

```sindarin
var s: str = "Hello"
s = s.append(" World")        # correct
s = s.append("!")

# Efficient incremental building (amortized O(1)):
var out: str = ""
for i in 0..10 =>
    out = out.append($"{i} ")
```

Prefer `append` over repeated `+` concatenation in loops.

### String Equality

`==` and `!=` compare **contents** (strcmp-style), not pointers. Safe to use freely:

```sindarin
var a: str = "hello"
var b: str = "hello"
print(a == b)                 # true
```

For case-insensitive comparison, normalize with `.toLower()` first.

## Array Creation

```sindarin
var nums: int[] = {1, 2, 3}
var empty: str[] = {}
var matrix: int[][] = {{1, 2}, {3, 4}}

# Multi-line literal
var names: str[] = {
    "Alice",
    "Bob",
    "Charlie"
}
```

### Range Literals (`start..end`)

Exclusive end. Produces an `int[]`.

```sindarin
var r: int[] = 1..6           # {1, 2, 3, 4, 5}
var z: int[] = 0..10          # {0..9}

# Ranges compose inside array literals
var mixed: int[] = {0, 1..4, 10}        # {0, 1, 2, 3, 10}
```

### Spread Operator (`...`)

Expands one array inside another literal.

```sindarin
var a: int[] = {1, 2, 3}
var copy: int[] = {...a}                # {1, 2, 3}
var extended: int[] = {0, ...a, 4, 5}   # {0, 1, 2, 3, 4, 5}
var merged: int[] = {...a, ...b}
var mixed: int[] = {...a, 10..13}
```

## Array Methods

```sindarin
arr.length                  # element count (property)
len(arr)                    # same, as function
arr.push(item)              # append
arr.pop()                   # remove + return last
arr.insert(item, index)     # insert at position
arr.remove(index)           # remove at position
arr.reverse()               # in-place reversal
arr.clear()                 # empty the array
arr.clone()                 # shallow copy
arr.concat(other)           # new array, does not mutate
arr.indexOf(item)           # position or -1
arr.contains(item)
arr.join(", ")              # → str
```

### Array Equality

```sindarin
var a: int[] = {1, 2, 3}
var b: int[] = {1, 2, 3}
print(a == b)                 # true (element-wise)
```

## Indexing and Slicing

### Positive and Negative Indexing

```sindarin
var arr: int[] = {10, 20, 30, 40, 50}
arr[0]                        # 10
arr[-1]                       # 50 (last)
arr[-2]                       # 40
```

### Slicing (`arr[start..end]`, `arr[start..end:step]`)

Slices create a new array. `start` defaults to `0`, `end` defaults to `length`. Negative indices count from the end.

```sindarin
arr[1..4]                     # {20, 30, 40}
arr[..3]                      # {10, 20, 30}
arr[2..]                      # {30, 40, 50}
arr[..]                       # full copy
arr[-2..]                     # {40, 50}
arr[..-1]                     # all but last
arr[..:2]                     # every 2nd element
arr[1..:2]                    # odd indices
```

## Byte Array Methods

```sindarin
var bytes: byte[] = "Hello".toBytes()

bytes.toString()              # UTF-8 decode → str
bytes.toStringLatin1()        # Latin-1 decode → str
bytes.toHex()                 # "48656c6c6f"
bytes.toBase64()              # "SGVsbG8="
```

## For-Each Iteration

```sindarin
for x in arr =>
    println($"{x}")

for i in 0..10 =>             # range works as an iterable
    println($"{i}")
```

## Arrays of Lambdas — Parenthesization

Function types bind `[]` to the **return type** by default. To declare an array *of* functions, wrap the function type in parentheses:

```sindarin
# Array of (fn(int, int) → int)
var ops: (fn(int, int): int)[] = {}
ops.push(fn(a: int, b: int): int => a + b)
ops.push(fn(a: int, b: int): int => a * b)
var r: int = ops[0](10, 5)                    # 15

# Lambda returning an array — no parens needed
var getNames: fn(): str[] = fn(): str[] => {"Alice", "Bob"}
```

Without parentheses the compiler emits an ambiguity error.

## Element Cleanup

Arrays of strings or `as ref` structs automatically free old values on reassignment, `remove`, `pop`, `clear`, or array drop. You never need to manage element lifetimes manually.

```sindarin
var names: str[] = {"Alice", "Bob"}
names[0] = "Charlie"          # "Alice" freed automatically
```

Passing an array `as val` deep-copies every element.

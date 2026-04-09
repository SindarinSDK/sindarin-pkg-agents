# Sindarin Structs

## Definition

```sindarin
struct Point =>
    x: double
    y: double

    # Instance method — accesses fields via self
    fn distance(other: Point): double =>
        var dx: double = self.x - other.x
        var dy: double = self.y - other.y
        return Math.sqrt(dx * dx + dy * dy)

    # Static method — called on the type, not an instance
    static fn origin(): Point =>
        return { x: 0.0, y: 0.0 }
```

## Instantiation

When the variable type is specified, the struct name can be omitted from the literal:

```sindarin
# Shorthand — type is inferred from the variable declaration
var p: Point = { x: 1.0, y: 2.0 }

# Explicit — equivalent to the above
var p: Point = Point { x: 1.0, y: 2.0 }

# Static factory methods
var o: Point = Point.origin()

# Method calls
var d: double = p.distance(o)
```

The shorthand `{ field: value }` works anywhere the target type is known from context:

```sindarin
# Variable declarations
var config: GnnConfig = { inputDim: 5, hiddenDim: 128, numActions: 3, numLayers: 2, arch: "gat", dropoutRate: 0.1 }

# Multi-line
var obs: Observation = {
  id: "obs-1",
  source: "csv-replay",
  timestamp: 1000,
  features: {NumericField { key: "price", value: 100.5 }},
  metadata: {}
}
```

**Note:** The explicit form `Type { ... }` is still required when the type cannot be inferred — for example, inside array literals or when passing to a function that accepts multiple types:

```sindarin
# Array literal elements need the explicit type
var edges: Edge[] = {
  Edge { source: 0, target: 1, weight: 0.9, kind: "temporal" },
  Edge { source: 1, target: 2, weight: 0.8, kind: "temporal" }
}

# Nested struct fields inside a shorthand literal
var pipeline: GuardrailPipeline = {
  confidence: ConfidenceGuardrail { threshold: 0.7 },
  rateLimiter: RateLimiterGuardrail { maxActions: 100, windowMs: 60000, actionTimestamps: {} }
}
```

## Default Field Values

Fields may have defaults. Fields without defaults are required in the literal.

```sindarin
struct ServerConfig =>
    host: str = "localhost"
    port: int = 8080
    maxConnections: int = 100

var cfg: ServerConfig = {}                         # all defaults
var prod: ServerConfig = { port: 443, host: "api.example.com" }
```

## Struct Equality

Structs support byte-wise `==` / `!=` by default:

```sindarin
var p1: Point = { x: 1.0, y: 2.0 }
var p2: Point = { x: 1.0, y: 2.0 }
if p1 == p2 => println("equal")
```

## Operator Overloading

Use the `operator` keyword to define custom `==` and `<`. The compiler auto-derives the rest:

| You define | Compiler derives |
|------------|------------------|
| `==` | `!=` |
| `<` | `>`, `<=`, `>=` |

```sindarin
struct Weight =>
    kg: int

    operator == (other: Weight): bool =>
        return self.kg == other.kg

    operator < (other: Weight): bool =>
        return self.kg < other.kg
```

Overloaded operators work in `if`, `while`, boolean expressions, `assert`, etc. For multi-field ordering, implement lexicographic comparison in `<`:

```sindarin
struct Version =>
    major: int
    minor: int

    operator == (other: Version): bool =>
        return self.major == other.major && self.minor == other.minor

    operator < (other: Version): bool =>
        if self.major != other.major => return self.major < other.major
        return self.minor < other.minor
```

You can override an auto-derived operator by defining it explicitly.

## Handle Fields

Any field whose type is heap-managed (`str`, arrays, `as ref` structs) is a **handle field**. The compiler auto-generates copy and cleanup helpers so you never double-free or leak:

```sindarin
struct Person =>
    name: str     # handle field: deep-copied on assign, freed on drop
    age: int      # plain field: copied by value

var p1: Person = Person { name: "Alice", age: 30 }
var p2: Person = p1                  # p2.name is an independent strdup copy
```

Plain primitive-only structs (`int`, `double`, `bool`, `byte`) have no cleanup code.

## Packed Structs (`#pragma pack`)

For exact binary layouts (file formats, network protocols), disable alignment padding:

```sindarin
#pragma pack(1)
struct FileHeader =>
    magic: int32       # offset 0
    version: byte      # offset 4
    flags: byte        # offset 5
    size: int32        # offset 6
    # total: 10 bytes, no padding
#pragma pack()
```

`#pragma pack()` (no argument) restores default alignment.

## Nested Structs

```sindarin
struct Line =>
    start: Point
    end: Point

var line: Line = {
    start: { x: 0.0, y: 0.0 },
    end: { x: 10.0, y: 20.0 }
}
var startX: double = line.start.x
line.end.y = 30.0
```

## sizeof

```sindarin
sizeof(Point)                  # byte size of the type (includes alignment padding)
sizeof(p)                      # byte size of a variable
sizeof Point                   # parentheses optional
```

## Utility Class Pattern (Static-Only)

```sindarin
struct Path =>
    _unused: int32

    static fn join(a: str, b: str): str =>
        return sn_path_join(a, b)

    static fn exists(path: str): bool =>
        return sn_path_exists(path) != 0
```

## Limitations

- No inheritance — use composition (nested structs).
- No anonymous structs — all structs must be named.
- No positional constructors — struct literals use named fields only.
- All fields are public; there are no access modifiers.

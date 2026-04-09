# Sindarin Imports & Multi-file Projects

## Basic Import

```sindarin
import "path/to/module"         # relative to project root
import "../sibling/module"      # parent-relative path
```

Imports make all top-level functions and structs from the target file available in the importing file.

## Namespaced Import

Use `as` to qualify imported symbols and avoid collisions:

```sindarin
import "sdk/core/math" as math
import "utils/strings" as strings

var sum: int = math.add(10, 20)
var msg: str = strings.concat("a", "b")
```

## Diamond Imports

If A imports B and C, and both B and C import D, the compiler deduplicates D — it is only included once.

```
main.sn
├── mid_a.sn ──┐
└── mid_b.sn ──┴── base.sn  (included once)
```

Symbols from `base.sn` are available in `main.sn` through either import path.

## Transitive Import Protection

Sibling imports do **not** leak symbols. If `file_a.sn` imports `helper.sn`, and `file_b.sn` also imports `helper.sn`, `file_b` cannot use symbols from `file_a` unless it imports `file_a` directly. Using an unimported transitive symbol is a **compile error**.

## Collision Handling

When two imported modules define the same function name, use `import ... as` to disambiguate:

```sindarin
import "graphics/draw" as gfx
import "text/draw" as txt

gfx.render()
txt.render()
```

Without namespacing, the later import's symbols take precedence (but this is fragile — prefer `as` aliases).

## Import and Closures

Imported structs with function-typed fields work correctly — closures are deep-copied on struct copy and freed on struct drop, even across module boundaries.

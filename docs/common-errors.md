# Sindarin Common Compile Errors

Quick reference for what the compiler rejects. Knowing these boundaries prevents wasted iteration.

## Struct Errors

- **Missing required field** — all fields without defaults must be set at instantiation
- **Unknown field name** — field not declared in the struct
- **Duplicate field name** — same field declared twice in a struct
- **Circular self-reference** — struct cannot contain itself as a value field (use `as ref` for indirection)
- **Field type mismatch** — value assigned to field doesn't match declared type

## Pointer Errors

- **Pointer param in non-native function** — `*T` params only allowed in `native fn`
- **Pointer return in non-native function** — returning `*T` only allowed in `native fn`
- **addressOf in non-native context** — `addressOf()` restricted to native functions

## Interface / Generic Errors

- **Missing interface method** — struct used with constraint `T: Interface` must implement all methods
- **Wrong return type** — interface method implementation has wrong return type
- **Wrong type arg count** — generic instantiated with wrong number of type parameters
- **Constraint is not an interface** — constraint must be a declared `interface`

## Thread Errors

- **Sync after detach** — `r!` on a detached handle is a compile error
- **Detach non-pending** — `r~` on an already-synced handle is a compile error
- **Access pending handle** — reading a thread handle before `!` sync is a compile error
- **Reassign pending handle** — overwriting a pending handle before sync is a compile error

## Import Errors

- **Transitive symbol leak** — using a symbol from a sibling's import without importing it directly
- **Namespace collision** — ambiguous symbol when two imports export the same name (use `import ... as`)

## Other Errors

- **Missing dispose method** — `using` requires the struct to have `fn dispose(): void`
- **Static at top level** — `static` modifier only valid inside struct methods
- **Cross-type operator** — binary operators require matching types (use `.toX()` conversions)
- **Operator on struct without definition** — `==` / `<` on structs requires an `operator` definition or defaults

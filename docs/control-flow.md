# Sindarin Control Flow

## if / else

```sindarin
if x > 0 =>
    println("positive")
else if x == 0 =>
    println("zero")
else =>
    println("negative")
```

## match

`match` compares the subject against each arm top-to-bottom using equality. The first matching arm runs. No fallthrough.

### Statement context

`else` is **optional**. If nothing matches, execution continues past the block.

```sindarin
match n =>
    1 => println("one")
    2 => println("two")
    else => println("other")
```

### Expression context

All arms must produce the same type, and `else` is **required**.

```sindarin
var msg: str = match code =>
    200 => "OK"
    404 => "Not Found"
    500 => "Server Error"
    else => "Unknown"
```

### Multi-value arms

Comma-separated patterns act as logical OR:

```sindarin
var category: str = match code =>
    200, 201, 202 => "Success"
    301, 302      => "Redirect"
    400, 401, 403 => "Client Error"
    500, 502, 503 => "Server Error"
    else          => "Other"
```

### Multi-statement arms

Arm bodies can be indented blocks. In expression context, the last expression is the arm's value.

```sindarin
var result: str = match code =>
    200 =>
        log("hit")
        "OK"
    else =>
        log("miss")
        "Unknown"
```

### Supported subject types

`int`, `long`, `byte`, `char`, `bool`, and `str` (string equality). Arm patterns are expressions, not destructuring patterns.

## for loops

```sindarin
# C-style
for var i: int = 0; i < 10; i++ =>
    println($"{i}")

# Range (exclusive end)
for i in 0..10 =>
    println($"{i}")

# Iteration
for item in array =>
    println($"{item}")

# Iterating a collection (calls .iter() automatically)
for entry in myMap =>
    println($"{entry.key}: {entry.value}")
```

## while

```sindarin
while condition =>
    body
```

## break / continue

```sindarin
for i in 0..100 =>
    if i == 50 => break
    if i % 2 == 0 => continue
    println($"{i}")
```

# Sindarin Serialization

The `@serializable` annotation auto-generates `encode()` / `decode()` methods:

```sindarin
@serializable
struct Address =>
    street: str
    city: str

@serializable
struct Person =>
    name: str
    age: int
    address: Address
    tags: str[]
```

## Field Aliasing

Use `@alias` on fields to map to different JSON keys:

```sindarin
@serializable
struct ApiResponse =>
    @alias "status_code"
    statusCode: int
    @alias "error_message"
    errorMessage: str
```

Encodes as `{"status_code":200,"error_message":""}` instead of using the Sindarin field names.

## Generated Methods

```sindarin
# Encode to JSON (via Encoder vtable)
var enc: Encoder = getJsonEncoder()
person.encode(enc)
var json: str = enc.result()

# Decode from JSON (via Decoder vtable)
var dec: Decoder = getJsonDecoder(jsonString)
var person: Person = Person.decode(dec)

# Array encode/decode
Person.encodeArray(people, enc)
var people: Person[] = Person.decodeArray(dec)
```

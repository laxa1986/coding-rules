# Method Naming Conventions

| Prefix                    | Intent                                      | Side Effects  | Returns           | Example                                    |
|---------------------------|---------------------------------------------|---------------|-------------------|--------------------------------------------|
| `is` / `has`              | Boolean check                               | ❌             | `bool`            | `isEmpty()`, `hasAccess()`                 |
| `check`                   | Assert valid, throw if not                  | ❌             | `void`            | `checkNotNull()`                           |
| `ensure`                  | Assert valid + retrieve, throw if not       | ❌             | entity or throws  | `ensureUser()`                             |
| `validate`                | Full validation, return result, no throw    | ❌             | validation result | `validateRequest()`                        |
| `get`                     | Lookup, throw if not found                  | ❌             | entity or throws  | `getUser()`                                |
| `find`                    | Lookup, return `None` if not found          | ❌             | entity or `None`  | `findUser()`                               |
| `fetch`                   | Retrieve from external source (network, DB) | ✅ network/I-O | entity            | `fetchConfig()`                            |
| `load`                    | Read + deserialize into memory              | ✅ I/O         | structured object | `loadTemplate()`                           |
| `parse`                   | Convert raw input into structured object    | ❌             | structured object | `parseRequest()`                           |
| `serialize`               | Convert structured object to raw output     | ❌             | string / bytes    | `serializeUser()`                          |
| `from` / `to` (not verbs) | Used in converters / mappters               | ❌             | varies            | `fromDto()`, `toDto()`                     |
| `format`                  | Format string template or Date into string  | ❌             | string            | `formatDate()`,                            |
| `build`                   | Construct object without persisting         | ❌             | new object        | `buildRequest()`                           |
| `create`                  | Construct object with persisting            | ✅ storage     | new object        | `createOrder()`                            |
| `save` / `persist`        | Write to storage                            | ✅ storage     | void or entity    | `saveUser()`, `persistEvent()`             |
| `enrich`                  | Augment/modify object passed in             | ✅ mutates     | void or entity    | `enrichRequest()`                          |
| `apply`                   | Overwrite fields on existing object         | ✅ mutates     | void or entity    | `applyPatch()`, `applyDefaults()`          |
| `process`                 | Multi-step business logic                   | ✅             | varies            | `processPayment()`, `processEvent()`       |
| `handle`                  | Respond to event or request                 | ✅             | varies            | `handleRequest()`, `handleEvent()`         |
| `on*`                     | Event listener / callback                   | ✅             | void              | `onConnect()`, `onMessageReceived()`       |
| `register` / `subscribe`  | Add to registry or listener list            | ✅             | void              | `registerHandler()`, `subscribeToEvents()` |
| `reset` / `clear`         | Remove state without destroying object      | ✅             | void              | `resetContext()`, `clearCache()`           |

## Mapper method names

Use short method names for mappers, such as `fromDto`, `toDto`, `fromEntity`, `toEntity`.
Avoid long names like `convertFromDtoToEntity` or `mapFromDtoToEntity`.
Rational: even dynamic languages like Python or JavaScript have type hints, so you can see the types of input and output,
so there is no need to repeat them in method name.
In most scenarios prefer `toTargetType`. Use `fromSourceType` when there are multiple source types for the same target type.

Bad:
```python
def convertFromDtoToEntity(dto: UserDto) -> UserEntity:
    ...
```

Good:
```python
def toEntity(dto: UserDto) -> UserEntity:
    ...
```

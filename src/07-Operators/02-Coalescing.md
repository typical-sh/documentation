# Coalescing

Coalescing operators choose between a primary expression and a fallback expression without opening a full conditional block.

They are for value-level fallback, not broad JavaScript-style truthiness. Numbers, strings, arrays, and objects do not become truthy or falsy just because they are present. Each operator names the exact value condition it handles.

| Operator | Falls through when the left side is... | Use for |
| -------- | -------------------------------------- | ------- |
| `and` | `true` | Continue only when a boolean condition succeeds |
| `or` | `false` | Provide a fallback for a failed boolean condition |
| `??` | `null` | Provide a fallback for a missing nullable value |
| `else` | `false` or `null` | Provide a fallback for an optional boolean-like result |
| `catch` | `bomb` | Recover from a bomb value |

## Null Coalescing

Use `??` when only `null` should trigger the fallback.

```typical
name is string or null

label = name ?? "Anonymous"
```

If `name` is a string, `label` receives that string. If `name` is `null`, `label` receives `"Anonymous"`.

The operator narrows the result by removing `null` when the fallback also produces the non-null value type.

```typical
port is int or null

actualPort = port ?? 8080
// actualPort is int
```

## Boolean Coalescing

Use `and` and `or` when the left side is boolean.

```typical
isReady is boolean

message = isReady and "Ready"
```

`and` falls through when the left side is `true`. It short-circuits like JavaScript `&&`, but uses Typical spelling.

```typical
enabled is boolean

label = enabled or "Disabled"
```

`or` falls through when the left side is `false`. It short-circuits like JavaScript `||`, but uses Typical spelling.

These operators do not make numbers, strings, arrays, or objects usable as conditions. Use an explicit comparison when the condition is not already boolean.

```typical
label = count or "Empty"
// Invalid when count is an int

label = count != 0 and "Has items"
// Valid
```

## False Or Null Coalescing

Use `else` when either `false` or `null` should trigger the fallback. This mirrors the exact behavior of the `if` control flow statement.

```typical
enabled is boolean or null

label = enabled else false
// label is boolean
```

This is the compact form for the common case where a nullable boolean should collapse to a definite value.

```typical
canSend is boolean or null

status = canSend else false
```

`else` is broader than `??`: it treats `false` as absent for the purpose of fallback. Use `??` when `false` is a meaningful value that must be preserved.

```typical
enabled is boolean or null

keepFalse = enabled ?? true
// false stays false

defaultFalse = enabled else true
// false becomes true
```

## Bomb Coalescing

Use `catch` when only a bomb should trigger the fallback.

```typical
handle() (
	🥚 str = getStringMaybe() 💣 catch "Bomb"
	return str + "!"
)

getStringMaybe() returns string 💣 (
	return Math.random() > 0.5 ? "Hello" : throw "Bomb"
)
```

`catch` is an expression operator. It does not open a `try` block and it does not catch arbitrary control flow. It falls through only when the left side evaluates to a bomb value.

```typical
value = getStringNullBomb() 💣 ?? "Null" catch "Bomb"
// Handles null before bombs.

value = getStringNullBomb() 💣 catch "Bomb" ?? "Null"
// Handles bombs before null.
```

Ordering matters because each coalescing operator handles only its own condition.

## Evaluation

The left expression is evaluated first. The fallback expression is evaluated only when the operator's fallback condition is met.

```typical
name = cachedName ?? loadName()
```

`loadName()` runs only when `cachedName` is `null`.

Coalescing operators group left-to-right with other coalescing operators unless parentheses specify otherwise.

```typical
label = name ?? nickname ?? "Anonymous"
```

This is equivalent to:

```typical
label = (name ?? nickname) ?? "Anonymous"
```

Use parentheses when mixing coalescing with arithmetic, concatenation, ternaries, or calls where the intended grouping is not visually obvious.

## Open Design Notes

The language map currently describes `and` as "coalesce loose true" and `or` as "coalesce loose false." The rest of the documentation rejects general JavaScript-style truthiness, so this page treats "loose" as boolean fallback behavior, not coercion from arbitrary values.

# Function Expressions

Function expressions create callable values. Typical uses TypeScript-style arrow syntax with Typical type annotations.

```ty
numbers.map(value => value + 1)

addFn = (a is int, b is int) => a + b
```

Use `=>` for executable function-expression bodies. See [[15-Aliases]] for callable types, assignment compatibility, and type extraction.

## Parameters

A single untyped parameter may omit parentheses.

```ty
names.map(name => name.length)
```

Use parentheses for zero parameters, multiple parameters, typed parameters, optional parameters, default values, rest parameters, and explicit result types.

```ty
readFn = () => readValue()

addFn = (a is int, b is int) => a + b

chooseFn = (value is int = nextNumber()) => value
```

Generic function expressions follow [[03-Generics]].

When a parameter type is omitted, Typical infers it from a unique expected callable signature. If there is no unique expected signature, the parameter is `unknown`.

```ty
words is string[] = ["", "hello"]
nonempty = words.filter(word => word.length > 0)
// word is inferred as string.

inspectFn = value => value.length
// value is unknown without an expected callable type.
```

Function expressions do not support parameter destructuring.

## Bodies

An expression body returns its expression.

```ty
squareFn = (value is int) => value * value
```

A parenthesized single expression is still an expression body.

```ty
sumFn = (a is int, b is int) => (a + b)
```

A body with declarations, control transfer, or multiple fragments is a scope body. Scope bodies require `return` to supply a value.

```ty
loggedSumFn = (a is int, b is int) => (
	console.log(a)
	return a + b
)
```

An empty body is valid.

```ty
emptyFn = () => ()
```

## Result Types

An explicit result type is written before `=>`.

```ty
readIntFn = () returns int => 1
```

When the result type is omitted, Typical infers it from the expression body or from reachable `return` operations targeting the function expression. If a reachable path falls through without returning, `null` is included in the inferred result and that path returns `null`.

```ty
maybeOneFn = (ready is boolean) => (
	if (ready) (
		return 1
	)
)
// Inferred int or null.
```

When an explicitly typed function expression falls through, it returns the declared result type's zero value.

```ty
zeroFn = () returns int => ()
// > 0
zeroFn()
```

`declare noImplicitNullReturns` reports unannotated function expressions whose fall-through implicitly returns `null`. It does not change inference or runtime behavior.

## Defaults And Rest

Function expressions support optional parameters, default values, and a final rest parameter.

```ty
connectFn = (port is int = 8080) => port
collectFn = (...values is int[]) => values
```

Default expressions run on every invocation, even when the caller supplies that argument. The supplied argument still wins; the evaluated default is discarded.

```ty
chooseFn = (a is int = nextNumber(), b is int = a + 1) => b

chooseFn(10)
// nextNumber runs, a remains 10, b becomes 11.
```

## Captures

Function expressions capture immutable surrounding values lexically.

```ty
offset is int = 10
addOffsetFn = (value is int) => value + offset
```

Hidden shared mutable captures are invalid for escaping function expressions. Put mutable state in an explicit field or object, or pass it as an argument.

```ty
count is var int = 0
readCountFn = () => count
// Invalid if readCountFn can escape: hidden shared mutable capture.
```

A function expression may edit through an explicitly captured editable object when that object is the state container.

```ty
editFn = () => (
	box.value = 3
)
```

Arrows capture their lexical receiver. Calling the stored function through another object does not rebind `this`.

```ty
readNameFn = () => this.name

other.readNameFn = readNameFn
other.readNameFn()
// Reads the original lexical receiver.
```

## Function Values

Function expressions are ordinary values. They may be stored, passed, returned, and placed in containers.

```ty
makeAdderFn = (offset is int) => (value is int) => value + offset
addTenFn = makeAdderFn(10)
// > 12
addTenFn(2)
```

Each evaluation of an arrow creates a distinct callable identity. Copying a function value preserves that identity. Callable equality uses identity; ordering is unsupported.

```ty
makeFn = () => () => 1

firstFn = makeFn()
secondFn = makeFn()
copyFn = firstFn

// > true
firstFn == copyFn
// > false
firstFn == secondFn
```

Ordinary named functions may also be selected as callable values. Selecting an instance function binds the receiver.

```ty
operationFn = double
operationFn(3)

readNameFn = user.makeDisplayName
readNameFn()
```

## Async, Yield, And Recovery

Async function expressions use `returns async`.

```ty
readAsyncFn = () returns async => readRemote()
```

Function expressions cannot be generators and cannot contain function-targeting `yield`.

```ty
valuesFn = () returns yield int => (
	yield 1
)
// Invalid.
```

Malformed arrows do not parse. The editor should not create them. If an external tool writes one, recovery ignores the malformed line rather than synthesizing a callable fallback.

```ty
brokenFn = () =>
// Invalid: missing body.
```

An explicitly empty body is different and remains valid.

```ty
emptyFn = () => ()
```

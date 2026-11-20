Functions define reusable behavior.

Typical uses the word function for top-level functions, space functions, and functions written inside classes. Their call behavior is the same. The only difference is where the function is found and which surrounding names are available to it.

## Function Declarations

A stable function declaration starts with a lowercase function name, followed by a parameter list, an optional return type, and an optional body.

```ty
greet() (
	console.log("Hello")
)
```

Function bodies use parentheses. This follows the general Typical rule that parentheses are used for control groups and declaration bodies where TypeScript-like languages often use curly braces.

When a body is present, the opening parenthesis attaches to the complete function header on the same physical line. A newline after the final header token ends eligibility for that body attachment; a later parenthesized group is parsed in its own context.

## Optional Bodies

A named function may omit its implementation body. Body omission is available only in declaration contexts; inside executable code, `name()` remains an ordinary call.

```ty
displayName() returns string
```

An omitted body, an empty body, and a body containing only anchors have the same executable behavior. Calling the function still evaluates the receiver, arguments, and parameter defaults normally, then produces the function result according to the ordinary return rules.

```ty
displayName() returns string

// Same executable result as:
displayName() returns string ()

// Also the same executable result, with owned anchors:
displayName() returns string (
	- Names the visible user.
)
```

For an explicitly typed ordinary function, normal fall-through supplies the zero value of the declared result type silently. If the result annotation is invalid or unresolved, the compiler reports the type notice and the call returns `null` for recovery.

Body omission does not create declaration/definition pairs. A second same-named function is still a duplicate declaration. A function signature may instead attach to an implementation partition under the partition rules.

```ty
add(a is int, b is int) returns int #123
```

## Parameters

Parameters are written as `name is Type`.

```ty
greet(name is string) (
	console.log("Hello, " + name)
)
```

Multiple parameters are separated with commas.

```ty
fullName(first is string, last is string) returns string (
	return first + " " + last
)
```

Typical does not support TypeScript-style parameter destructuring in function declarations.

This is invalid:

```ty
greet({ name } is User) (
	console.log(name)
)
```

Destructuring, where supported, belongs to value and pattern syntax rather than the function parameter form.

## Return Types

A function can declare its return type after the parameter list using `returns`.

```ty
add(a is int, b is int) returns int (
	return a + b
)
```

Parameter type annotations may omit `is Type`. A bare parameter is equivalent to a parameter annotated as `unknown`.

```ty
log(message) (
)
```

This shorthand is only for unknown-typed parameters. Parameters with defaults or optional markers still use their ordinary initializer syntax.

When the return type is omitted, the compiler infers it from reachable `return` operations that target the function. Returns captured by nested matches or standalone scopes do not contribute to the function's return type. If any reachable path falls through without returning, `null` is included in the inferred result type and that path returns `null`. An explicit `return null` also contributes `null`.

```ty
add(a is int, b is int) (
	return a + b
)
```

```ty
maybeLabel(ready is boolean) (
	if (ready) (
		return "ready"
	)
)
// Inferred string or null; returns null when ready is false.
```

`declare noImplicitNullReturns` reports an implicit `null` path in an unannotated function. It does not change the inferred type or runtime result, and it does not report an explicit `return null`. The declaration is off by default.

`return` may instead target a captured match or captured standalone scope inside the function. Numeric suffixes select outward return destinations. See [[06-Return]].

Every Typical type has a zero value for typed fallback when one can be synthesized. Scalars use their ordinary empty representation: numeric zero in the declared representation, `false`, empty string, empty byte string, Unicode scalar `U+0000` for characters, and empty variable-length arrays. Fixed-shape aggregates zero each component. Nominal primitive types preserve their nominal type while zeroing their representation. Reference-class zero synthesis is described with construction in [[08-Constructors]].

## Optional Parameters

A parameter can be marked optional with `= ?`.

```ty
find(id is string, fallback is User = ?) (
)
```

An optional parameter may be omitted by the caller.

```ty
find("current")
```

Optional parameters use the `= ?` form. Typical does not use `T?` to mark an optional parameter type.

## Default Values

A parameter can provide a default value.

```ty
connect(port is int = 8080) (
)
```

When the caller omits the argument, the default value is used.

```ty
connect()
connect(3000)
```

## Rest Parameters

Rest parameters collect remaining arguments into an array.

```ty
sum(...values is int[]) returns int (
	🐝 total is var int = 0
	
	values each value (
		total = total + value
	)
	
	return total
)
```

A function can have at most one rest parameter, and it must be the final parameter.

## Function Calls

Function calls use the function name followed by an argument list.

```ty
greet("Ada")
add(1, 2)
connect()
```

Arguments are matched to parameters in order.

## Functions In Classes

Classes can contain functions.

```ty
User (
	constructor
	
	displayName() returns string (
		return firstName + " " + lastName
	)
)
```

A function written inside a class is still a function. Typical does not require a separate method concept for the ordinary case.

Inside a class-contained function, `this` refers to the current class value. The same rule applies to a function inside a space owned by that class; the space does not introduce another receiver. Members may be accessed without `this.` when their names are not hidden by local bindings or parameters, while grouped members keep their space path. See [Member Name Lookup](02-Spaces-And-Classes.md#member-name-lookup).

From outside the class value, the function is accessed through the value that provides it.

```ty
user.displayName()
```

The access path is different from a top-level call, but the function declaration and call mechanics are the same.

## Generic Functions

Functions may be generic. Generic declaration, inference, specialization, and application rules are defined in [[03-Generics]].

## Function Expressions

Function expressions use related syntax, but they are covered separately. This page only describes named function declarations and ordinary function calls.

## Ping-Pong Functions

A runnable `yield` targeting its current function statically makes that function a generator. Typical presents generator functions as 🏓 ping-pong functions.

A function may also declare generator status without an implementation by writing `returns yield Type` in the signature.

```ty
values() returns yield int
```

A bodyless generator returns an empty generator. Its first `next()` reports completion and emits no element. When an implementation body is present, generator classification can still be inferred from a runnable function-targeting `yield`.

Calling one returns a lazy `Generator(TYieldType)` whose body begins on its first `next()` call. Typist draws the 🏓 treatment and presents the function's primary result using a non-editable `yields` chip rather than the ordinary `returns` chip. These chips are derived editor presentation, not serialized keywords.

Typical generator terminology and protocol types remain `Generator`, `Iterator`, and `Iterable`; ping-pong is the human-facing name. See [[07-Yield]].

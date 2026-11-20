Properties provide computed access using a field-shaped declaration. The member type uses `is`; function return annotations use `returns`.

```typical
User (
	name is string (
		get firstName + " " + lastName
		set firstName = value
	)
)
```

## Accessors

The property body recognizes every anchor, the first top-level `get`, and the first top-level `set`, in source order. Either accessor may be omitted. Duplicate accessors and every other statement in this body produce notices and do not execute. A later accessor never replaces the first one. Anchors remain recognized even after unwanted content.

Each accessor prefixes exactly one statement or expression. The getter supplies the property's value; the setter performs an assignment or another operation. Multiple statements require an explicit scope:

```typical
name is string (
	- The display name of this user.
	get (
		logRead()
		return firstName + " " + lastName
	)
	set (
		validate(value)
		firstName = value
	)
)
```

`value` is a contextual keyword in the setter: it denotes the incoming value and has the property's declared type. It is not a declared parameter or a globally reserved identifier. Both accessors use the same property type. Setters do not supply a result.

## Fields And Partitions

There are no automatic properties or bodyless accessor declarations. A stored member is a field, such as `name is string`. A body containing only anchors remains a field annotation body.

Fields and properties are compatible in architectural contracts. A lower partition can provide a property implementation for a field declared in an ancestor partition. Access follows architectural partition rules; properties do not introduce separate getter/setter visibility modifiers. See [[18-Partitions]].

Properties can appear in classes, spaces, and extensions. Space properties have no implicit instance.

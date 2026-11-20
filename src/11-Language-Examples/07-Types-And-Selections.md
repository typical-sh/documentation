# Types And Selections

## Selecting A Day

This selection type is consumed by an exhaustive match.

```typical
Day is one of (
	monday
	tuesday
	wednesday
	thursday
	friday
	saturday
	sunday
)

isWeekend(day is Day) returns bool (
	return day matches (
		Day.saturday
		Day.sunday true
		else false
	)
)
```

## Explicit Selection Values

This example assigns protocol-style numeric values to named cases.

```typical
Status is one of (
	ok = 200
	created = 201
	badRequest = 400
	notFound = 404
)

isSuccess(status is Status) returns bool (
	return status matches (
		Status.ok
		Status.created true
		else false
	)
)
```

## A Generic Identity Function

Generic type parameters appear first in the ordinary parameter list. Their uppercase names distinguish them from runtime parameters.

```typical
identity(T, value is T) returns T (
	return value
)

retainName(name is string) returns string (
	return identity(name)
)
```

## Iterating A Selection Type

This example combines a selection declaration with type-level iteration.

```typical
Priority is one of (
	low = 1
	normal = 2
	high = 3
	urgent = 4
)

priorityNames() returns string[] (
	names is editable string[]
	
	Priority each name value (
		names.push(`{value}: {name}`)
	)
	
	return names
)
```

## Composition And Numbering

```typical
Base is one of (
	a
	b
	c = 2
)
// Base: a = 3, b = 4, c = 2.

Extended is Base or one of (
	d = 10
	e
)
// Extended: a = 11, b = 12, c = 2, d = 10, e = 13.
```

## Qualified Literal Values

```typical
Settings (
	small = 16

	Width is one of (
		this.small
		32
	)
)
```

## Literal Member Access

```typical
Offset is one of (-1, null)
Reply is one of ({ code = 1 })

startup (
	x = Offset.-1
	y = Offset.null
	z = Reply.{ code = 1 }
)
```

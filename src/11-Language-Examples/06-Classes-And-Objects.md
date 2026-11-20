# Classes And Objects

## A Small Value Holder

This class stores two fields and exposes a computed function.

```first
Point (
	x is int
	y is int
	
	constructor(x is int, y is int) (
		this.x = x
		this.y = y
	)
	
	distanceSquared() returns int (
		return x * x + y * y
	)
)
```

## Constructing And Calling

This example creates an instance and calls one of its functions.

```first
Counter (
	value is int
	
	constructor(value is int) (
		this.value = value
	)
	
	advanced(amount is int) returns Counter (
		return Counter(value + amount)
	)
)

nextCounter() returns Counter (
	counter = Counter(10)
	return counter.advanced(5)
)
```

## Inheritance And Super Access

This example gives a child class a refined description.

```first
Animal (
	name is string
	
	constructor(name is string) (
		this.name = name
	)
	
	description() returns string (
		return name
	)
)

Dog is Animal (
	constructor(name is string) (
		super(name)
	)
	
	description() returns string (
		return super.description() + " the dog"
	)
)
```

## Working With An Array Of Instances

This denser example combines construction, member access, arrays, and iteration.

```first
LineItem (
	name is string
	price is int
	
	constructor(name is string, price is int) (
		this.name = name
		this.price = price
	)
	
	total() returns int (
		return price
	)
)

receiptTotal(items is LineItem[]) returns int (
	total is var int = 0
	
	items each item (
		total += item.total()
	)
	
	return total
)
```

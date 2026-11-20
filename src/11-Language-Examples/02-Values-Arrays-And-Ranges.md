# Values, Arrays, And Ranges

## Summing A Range

This function adds the integers from one through a supplied limit.

```ty
sumThrough(limit is int) returns int (
	total is var int = 0
	
	1 to limit each value (
		total += value
	)
	
	return total
)
```

## Finding A Value

This function searches an array and returns the first matching index.

```ty
indexOf(values is int[], wanted is int) returns uint or null (
	values each value index (
		if (value == wanted) (
			return index
		)
	)
	
	return null
)
```

## Measuring Adjacent Changes

This example combines array access, a range, arithmetic, and an array result.

```ty
changes(values is int[]) returns int[] (
	result is editable int[]
	
	1 til values.length each index (
		previous = values[index - 1]
		current = values[index]
		result.push(current - previous)
	)
	
	return result
)
```

## Building A Multiplication Table

This naturally nested example produces a two-dimensional array.

```ty
multiplicationTable(size is int) returns int[][] (
	rows is editable int[][]
	
	1 to size each rowNumber (
		row is editable int[]
		
		1 to size each columnNumber (
			row.push(rowNumber * columnNumber)
		)
		
		rows.push(row)
	)
	
	return rows
)
```

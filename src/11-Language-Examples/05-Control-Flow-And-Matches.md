# Control Flow And Matches

## Classifying A Number

This function uses sequential conditions with early returns.

```ty
classify(value is int) returns string (
	if (value < 0) (
		return "negative"
	)
	
	if (value == 0) (
		return "zero"
	)
	
	return "positive"
)
```

## Matching A Numeric Range

This captured match assigns a label according to the first matching arm.

```ty
temperatureLabel(celsius is int) returns string (
	return celsius matches (
		-50 to 0 "freezing"
		1 to 15 "cold"
		16 to 24 "comfortable"
		25 to 35 "warm"
		else "extreme"
	)
)
```

## Sharing A Match Body

Several command names intentionally select the same behavior.

```ty
normalizedCommand(command is string) returns string (
	return command matches (
		"q"
		"quit"
		"exit" "stop"
		
		"h"
		"help" "show help"
		
		else "unknown"
	)
)
```

## Searching With Nested Control Flow

This composed example uses a loop, a condition, and a captured match.

```ty
firstPassingGrade(grades is int[]) returns string (
	grades each grade index (
		label = grade matches (
			90 to 100 "excellent"
			70 to 89 "passing"
			else "retry"
		)
		
		if (grade >= 70) (
			return `{index}: {label}`
		)
	)
	
	return "none"
)
```


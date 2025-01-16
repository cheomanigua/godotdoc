---
weight: 3050
title: "Bit Flags"
description: "Bit flags can add variety with minimal memory use"
icon: "article"
date: "2024-09-18T23:59:43+02:00"
lastmod: "2024-09-18T23:59:43+02:00"
draft: false
toc: true
---

# Basics

Bit flags were used in the past to save memory. An integer in GDscript has 64 bits, so you can store 64 boolean values in a single integer.

Bit flags let you create multiple choice selections. They are stored in binary order (power of 2). For instance:

```gdscript

@export_flags ("FIRE", "WATER", "EARTH") var elements: int = 0
```

We have the variable `elements` with the flags: `FIRE`, `WATER` and `EARTH`. Changing the value of the variable `element`, which is of type `int`, will set the flags:


```
__4___2___1__			__4___2___1__			__4___2___1__			__4___2___1__			__4___2___1__
| 0 | 0 | 1 |			| 0 | 1 | 0 |			| 0 | 1 | 1 |			| 1 | 0 | 0 |			| 1 | 0 | 1 |
¯¯¯¯¯¯¯¯¯¯¯¯¯			¯¯¯¯¯¯¯¯¯¯¯¯¯			¯¯¯¯¯¯¯¯¯¯¯¯¯			¯¯¯¯¯¯¯¯¯¯¯¯¯			¯¯¯¯¯¯¯¯¯¯¯¯¯
0 + 0 + 1 = 1			0 + 2 + 0 = 2			0 + 2 + 1 = 3			4 + 0 + 0 = 4			4 + 0 + 1 = 5
```

Note the the flags are read from right to left, so `FIRE` is on the right, `WATER` is on the center and `EARTH` is on the left.

In the five examples above, we have set the value of `element` to the following:

- 1: FIRE has been selected
- 2: WATER has been selected
- 3: FIRE and WATER has been selected
- 4: EARTH has been selected
- 5: FIRE and EARTH has been selected

... and so on.

Note that we have only used 3 bits of the 64 bits availables with an `int` variable. We could have added 61 extra elements.

[Ref](https://docs.godotengine.org/en/stable/tutorials/scripting/gdscript/gdscript_exports.html#exporting-bit-flags)


### Example 

```gdscript
@export_flags ("FIRE", "WATER", "EARTH") var elements: int = 0


func _ready() -> void:
	show_elements(elements)


func show_elements(type: int):
	match(type):
		0:
			print("N/A")
		1:
			print("Fire")
		2:
			print("Water")
		3:
			print("Fire and Water")
		4:
			print("Earth")
		5:
			print("Fire and Earth")
		6:
			print("Water and Earth")
		7:
			print("Fire, Water and Earth")
```

# Bitwise operations

There are two types of bitwise operations: Left Shift/Right Shift operators and direct operators. Both accomplish the same goal, but use slightly different approach.

## Direct bitwise operations

In the following examples, we are only working with 3 bits, although you could use up to 64 bits. `bit_index` is a made up index. It has to be replaced by an actual value: 1, 2 or 4 and represents the bit position. In this particular example, only one bit is affected by the bitwise operations.

```
__4___2___1__		__4___2___1__		__4___2___1__
| 0 | 0 | 1 |		| 0 | 1 | 0 |		| 1 | 0 | 0 |
¯¯¯¯¯¯¯¯¯¯¯¯¯		¯¯¯¯¯¯¯¯¯¯¯¯¯		¯¯¯¯¯¯¯¯¯¯¯¯¯
bit_index = 1		bit_index = 2		bit_index = 4
```

Check if all of the bits are set:

```gdscript
if elements != 0:
```

Set a bit:

```gdscript
# Replace bit_index by 1, 2 or 4
elements |= bit_index
```

Clear a bit:

```gdscript
# Replace bit_index by 1, 2 or 4
elements &= ~bit_index
```

Flip a bit:

```gdscript
# Replace bit_index by 1, 2 or 4
elements ^= bit_index
```

Check if a bit is set:

```gdscript
# Replace bit_index by 1, 2 or 4
if elements & bit_index:
```

Check if a bit is not set:

```gdscript
# Replace bit_index by 1, 2 or 4
if (elements & (bit_index == 0)):
```

### Example 1

```gdscript

func _ready() -> void:
	print("User selected:")
	show_elements(elements)
	print("")
	elements = 7
	print(("Setting flags to: %d or %s") % [elements, String.num_int64(elements,2,false)])
	show_elements(elements)
	print("")
	elements &= ~2
	print(("Clearing bit index 2: %d or %s") % [elements, String.num_int64(elements,2,false)])
	show_elements(elements)
	print("")
	elements |= 2
	print(("Setting bit index 2: %d or %s") % [elements, String.num_int64(elements,2,false)])
	show_elements(elements)
	print("")
	elements ^= 2
	print(("Flipping bit index 2: %d or %s") % [elements, String.num_int64(elements,2,false)])
	show_elements(elements)
	print("")
	var usage: String
	if elements & 2:
		usage = "You can use Water"
	else:
		usage = "You cannot use Water"
	print(("Checking if bit index 2 is set: %s") % [usage])
	print("")
	if (elements & (2 == 0)):
		usage = "You cannot use Water"
	else:
		usage = "You can use Water"
	print(("Checking if bit index 2 is not set: %s") % [usage])
```

### Example 2

```gdscript

const FIRE = 1
const WATER = 2
const EARTH = 4

func _ready() -> void:
	print("User selected:")
	show_elements(elements)
	print("")
	elements = 7
	print(("Setting flags to: %d or %s") % [elements, String.num_int64(elements,2,false)])
	show_elements(elements)
	print("")
	clear_flag(WATER)
	print(("Clearing bit index 2: %d or %s") % [elements, String.num_int64(elements,2,false)])
	show_elements(elements)
	print("")
	set_flag(WATER)
	print(("Setting bit index 2: %d or %s") % [elements, String.num_int64(elements,2,false)])
	show_elements(elements)
	print("")
	flip_flag(WATER)
	print(("Flipping bit index 2: %d or %s") % [elements, String.num_int64(elements,2,false)])
	show_elements(elements)
	print("")
	print("Checking if bit index 2 is set:")
	print("You can use Water") if is_flag_set(WATER) else print("You cannot use Water")
	print("")
	print("Checking if bit index 2 is not set:")
	print("You cannot use Water") if is_flag_not_set(WATER) else print("You can use Water")


	# Function to set a flag (use bitwise OR)
	func set_flag(flag):
		elements |= flag


	# Function to clear a flag (use bitwise AND with negation)
	func clear_flag(flag):
		elements &= ~flag


	# Function to flip a flag
	func flip_flag(flag):
		elements ^= flag


	# Function to check if a flag is set (use bitwise AND)
	func is_flag_set(flag) -> bool:
		return (elements & flag) != 0


	# Function to check if a flag is not set (use bitwise AND)
	func is_flag_set(flag) -> bool:
		return (elements & flag) == 0
```


Both example code above will print:

```
User selected:
Water and Earth

Setting flags to: 7 or 111
Fire, Water and Earth

Clearing bit index 2: 5 or 101
Fire and Earth

Setting bit index 2: 7 or 111
Fire, Water and Earth

Flipping bit index 2: 5 or 101
Fire and Earth

Checking if bit index 2 is set: You cannot use Water

Checking if bit index 2 is not set: You cannot use Water
```


## Left Shift bitwise operations

In the following examples, we are only working with 3 bits, although you could use up to 64 bits. `bit_index` is a made up index. It has to be replaced by an actual value: 0, 1 or 2 and represents the bit position. In this particular example, only one bit is affected by the bitwise operations.

```
__4___2___1__		__4___2___1__		__4___2___1__
| 0 | 0 | 1 |		| 0 | 1 | 0 |		| 1 | 0 | 0 |
¯¯¯¯¯¯¯¯¯¯¯¯¯		¯¯¯¯¯¯¯¯¯¯¯¯¯		¯¯¯¯¯¯¯¯¯¯¯¯¯
bit_index = 0		bit_index = 1		bit_index = 2
```


Check if all of the bits are set:

```gdscript
if elements != 0:
```

Set a bit:

```gdscript
# Replace bit_index by 1, 2 or 3
elements |= 1 << bit_index
```

Clear a bit:

```gdscript
# Replace bit_index by 1, 2 or 3
elements &= ~(1 << bit_index)
```

Flip a bit:

```gdscript
# Replace bit_index by 1, 2 or 3
elements ^= 1 << bit_index
```


Check if a bit is set:

```gdscript
# Replace bit_index by 1, 2 or 3
if elements & (1 << bit_index):
```


Check if a bit is not set:

```gdscript
# Replace bit_index by 1, 2 or 3
if (elements & (1 << bit_index == 0)):
```

### Example 1

```gdscript
func _ready() -> void:
	print("User selected:")
	show_elements(elements)
	print(elements)
	print("")
	elements = 7
	print(("Setting flags to: %d or %s") % [elements, String.num_int64(elements,2,false)])
	show_elements(elements)
	print("")
	elements &= ~(1 << 1)
	print(("Clearing bit index 1: %d or %s") % [elements, String.num_int64(elements,2,false)])
	show_elements(elements)
	print("")
	elements |= 1 << 1
	print(("Setting bit index 1: %d or %s") % [elements, String.num_int64(elements,2,false)])
	show_elements(elements)
	print("")
	elements ^= 1 << 1
	print(("Flipping bit index 1: %d or %s") % [elements, String.num_int64(elements,2,false)])
	show_elements(elements)
	print("")
	var usage: String
	if elements & (1 << 1):
		usage = "You can use Water"
	else:
		usage = "You cannot use Water"
	print(("Checking if bit index 1 is set: %s") % [usage])
	print("")
	if (elements & (1 << 1 == 0)):
		usage = "You cannot use Water"
	else:
		usage = "You can use Water"
	print(("Checking if bit index 1 is not set: %s") % [usage])
```


### Example 2

```gdscript

const FIRE = 0
const WATER = 1
const EARTH = 2

func _ready() -> void:
	print("User selected:")
	show_elements(elements)
	print("")
	elements = 7
	print(("Setting flags to: %d or %s") % [elements, String.num_int64(elements,2,false)])
	show_elements(elements)
	print("")
	clear_flag(WATER)
	print(("Clearing bit index 2: %d or %s") % [elements, String.num_int64(elements,2,false)])
	show_elements(elements)
	print("")
	set_flag(WATER)
	print(("Setting bit index 2: %d or %s") % [elements, String.num_int64(elements,2,false)])
	show_elements(elements)
	print("")
	flip_flag(WATER)
	print(("Flipping bit index 2: %d or %s") % [elements, String.num_int64(elements,2,false)])
	show_elements(elements)
	print("")
	print("Checking if bit index 2 is set:")
	print("You can use Water") if is_flag_set(WATER) else print("You cannot use Water")
	print("")
	print("Checking if bit index 2 is not set:")
	print("You cannot use Water") if is_flag_not_set(WATER) else print("You can use Water")


# Function to set a flag (use bitwise OR)
func set_flag(flag):
	elements |= 1 << flag


# Function to clear a flag (use bitwise AND with negation)
func clear_flag(flag):
	elements &= ~(1 << flag)


# Function to flip a flag
func flip_flag(flag):
	elements ^= 1 << flag


# Function to check if a flag is set (use bitwise AND)
func is_flag_set(flag) -> bool:
	return elements & (1 << flag)


# Function to check if a flag is not set (use bitwise AND)
func is_flag_not_set(flag) -> bool:
	return (elements & (1 << flag) == 0)
```


Both example code above will print:

```
User selected:
Water and Earth

Setting flags to: 7 or 111
Fire, Water and Earth

Clearing bit index 1: 5 or 101
Fire and Earth

Setting bit index 1: 7 or 111
Fire, Water and Earth

Flipping bit index 1: 5 or 101
Fire and Earth

Checking if bit index 1 is set: You cannot use Water

Checking if bit index 1 is not set: You cannot use Water
```

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

### Basics

Bit flags were used in the past to save memory. An integer in GDscript has 64 bits, so you can store 64 boolean values in a single integer.

Bit flags let you create multiple choice selections. They are stored in binary order (power of 2). For instance:

```gdscript

@export_flags ("FIRE", "WATER", "EARTH") var elements: int = 0
```

We have the variable `elements` with the flags: `FIRE`, `WATER` and `EARTH`. Changing the value of the variable `element`, which is of type `int`, will set the flags:


```
__1___2___4__			__1___2___4__			__1___2___4__			__1___2___4__			__1___2___4__
| 1 | 0 | 0 |			| 0 | 1 | 0 |			| 1 | 1 | 0 |			| 0 | 0 | 1 |			| 1 | 0 | 1 |
¯¯¯¯¯¯¯¯¯¯¯¯¯			¯¯¯¯¯¯¯¯¯¯¯¯¯			¯¯¯¯¯¯¯¯¯¯¯¯¯			¯¯¯¯¯¯¯¯¯¯¯¯¯			¯¯¯¯¯¯¯¯¯¯¯¯¯
1 + 0 + 0 = 1			0 + 2 + 0 = 2			1 + 2 + 0 = 3			0 + 0 + 4 = 4			1 + 0 + 4 = 5
```

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
	add_attack("magical",  elements)


func add_attack(attack_name: String, type: int):
	match(type):
		0:
			print("No magical attack")
		1:
			print("Attack by Fire")
		2:
			print("Attack by Water")
		3:
			print("Attack by Fire and Water")
		4:
			print("Attack by Earth")
		5:
			print("Attack by Fire and Earth")
		6:
			print("Attack by Water and Earth")
		7:
			print("Attack by Fire, Water and Earth")
```

### Bitwise operations

Checking if any of the bits are 1:

```gdscript
if flags != 0:
```


Reading a single bit:

```gdscript
# bit_index is a value from 0 to 63 inclusive
if flags & (1 << bit_index):
```

Flipping a bit:

```gdscript
flags ^= 1 << bit_index
```

Setting a bit to 1 or 0:

```gdscript
# value is either 1 or 0, and the bit needs to be 0 before setting its value
flags |= value << bit_index
```

Setting a bit to 1:

```gdscript
# In this case the bit doesn't need to be 0 before setting its value
flags |= 1 << bit_index
```

Setting a bit to 0:

```gdscript
flags &= ~(1 << bit_index)
```

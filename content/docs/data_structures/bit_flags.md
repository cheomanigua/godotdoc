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

## Basics

Bit flags are boolean values stored in each bit of an integer variable, in binary order (power of 2). In Godot, an integer has 64 bits, which means you can hold 64 different boolean values in one single integer variable.

Since one bit can only be set to 1 or 0, bit flags are used for dual state parameters: on/off, enable/disable, true/false. Bit flags are good for creating multiple choice selections or sets of common parameters, with one single integer variable, each selection being one bit of the variable.



```gdscript

@export_flags ("FIRE", "WATER", "EARTH") var elements: int = 0
```

[Ref](https://docs.godotengine.org/en/stable/tutorials/scripting/gdscript/gdscript_exports.html#exporting-bit-flags)

In the above code, we have created the integer variable `elements` which contains the flags: `FIRE`, `WATER` and `EARTH`. Each flag can be either `true` or `false`, or better said, set or unset. There are two ways to set the bits of the flags: using the `elements` variable value of type `int`, or using a left shift bit operator (LSO):

```
_____________		_____________		_____________		_____________		__E___W___F__   E: Earth, W: Water, F: Fire
| 0 | 0 | 1 |		| 0 | 1 | 0 |		| 0 | 1 | 1 |		| 1 | 0 | 0 |		| 1 | 0 | 1 |
¯¯¯¯¯¯¯¯¯¯¯¯¯		¯¯¯¯¯¯¯¯¯¯¯¯¯		¯¯¯¯¯¯¯¯¯¯¯¯¯		¯¯¯¯¯¯¯¯¯¯¯¯¯		¯¯¯¯¯¯¯¯¯¯¯¯¯
  4   2   1			  4   2   1			  4   2   1			  4   2   1			  4   2   1		bit index by value
  2   1   0			  2   1   0			  2   1   0			  2   1   0			  2   1   0		bit index by LSO

1					2					3					4					5				variable value
1<<0				1<<1				1<<0 + 1<<1			1<<2				1<<0 + 1<<2		LSO
```

{{< alert text="Flags are read from right to left, so `FIRE` is on the right, `WATER` is on the center and `EARTH` is on the left." />}}

In the graphic above, the **bit index by value/LSO** is a visual representation that help us see which **variable/LSO** to use. The **value** is the sum of the **indexes**. I say **indexes** to help visualize the graph above, but there are no actual indexes in the variable.

In the five examples above, we have set the variable `elements` and the left shift operator to the following values (note that `1<<0` can also be written as `1`):

| | variable | LSO v1 | LSO v2 |
|-|-|-|-|
| FIRE | `1` | `1` | `1<<0`
| WATER | `2` | `1<<1` | `1<<1` |
| FIRE and WATER | `3` | `1 + (1<<1)` | `(1<<0) + (1<<1)` |
| EARTH | `4` | `1<<2` | `1<<2` |
| FIRE and EARTH | `5` | `1 + (1<<2)` | `(1<<0) + (1<<2)` |

... and so on.

At the example script at the end of the page you can see how to use integer variable values and left shift operators in combination with constants, custom functions and the built-in match function.


## Single bits operations

There are two ways to access/target a single bit in the variable: using the variable value, or using left shift bitwise operations. They both accomplish the same goal, but use slightly different approaches.

### Variable value

`bit_index` is a made up index, there are no actual indexes in bit flags. `bit_index` is to be replaced by an actual variable value: 1, 2 or 4 and represents the bit to be operated with.

```
__4___2___1__		__4___2___1__		__4___2___1__
| 0 | 0 | 1 |		| 0 | 1 | 0 |		| 1 | 0 | 0 |
¯¯¯¯¯¯¯¯¯¯¯¯¯		¯¯¯¯¯¯¯¯¯¯¯¯¯		¯¯¯¯¯¯¯¯¯¯¯¯¯
bit_index = 1		bit_index = 2		bit_index = 4
```


### Left Shift bitwise operations

`left_shift` is a made up index. It has to be replaced by an actual value: 0, 1 or 2 and represents the bit shifting towards the bit to be operated with.

```
__4___2___1__		__4___2___1__		__4___2___1__
| 0 | 0 | 1 |		| 0 | 1 | 0 |		| 1 | 0 | 0 |
¯¯¯¯¯¯¯¯¯¯¯¯¯		¯¯¯¯¯¯¯¯¯¯¯¯¯		¯¯¯¯¯¯¯¯¯¯¯¯¯
left_shift = 0		left_shift = 1		left_shift = 2
```

### Operations

| Operation | Variable Value | Left Shift Operator |
|-|-|-|
| Set a bit | `elements \|= bit_index` | `elements \|= 1 << left_shift` |
| Clear a bit | `elements &= ~bit_index` | `elements &= ~(1 << left_shift)` |
| Flip a bit | `elements ^= bit_index` | `elements ^= 1 << left_shift` |
| are all bits set? | `if elements != 0:` | `if elements != 0:` |
| is bit set? | `if elements & bit_index:` | `if elements & (1 << left_shift):` |
| is bit not set? | `if (elements & (bit_index == 0)):` | `if (elements & (1 << left_shift == 0)):` |

### Custom functions

| Name and parameter | Body | Flag |
|-|-|-|
| `func set_flag(flag):` | `elements \|= flag` | `1` or `1<<0` |
| `func clear_flag(flag):` | `elements &= ~flag` | `2` or `1<<1` |
| `func flip_flag(flag):` | `elements ^= flag` | `3` or `(1<<0)+(1<<1)` |
| `func is_flag_set(flag) -> bool:` | `return (elements & flag)` | `4` or `1<<2` |
| `func is_flag_unset(flag) -> bool:` | `return (elements & flag) == 0` | `5` or `(1<<0)+(1<<2)` |

Example:

```gdscript
func set_flag(flag):
    elements |= flag
```

We can run the function: `set_flag(4)` or `set_flag(1<<2)` to set the bit.

### Queries

Bit flags can be used to query small or large bit sets. Continuing with our example set:

```gdscript
@export_flags ("FIRE", "WATER", "EARTH") var elements: int
```

As per `elements`, **Fire** and **Water** are represented by the value `011` in binary and `3` in decimal (Remember we read from right to left in binary).


- In order to check if **AT LEAST** `Fire` and `Water` are set in `elements`, we perform a bitwise `AND` operation between the value of `elements` againts the value `011` using the bitwise operator `&`
- In order to check if **ONLY** `Fire` and `Water` are set in `elements`, we compare the value of `elements` with the value `011` using the comparison operator `==`


||||| | | |
|-|-|-|-|-|-|-|
|| Scenario 1 | Scenario 2 | Scenario 3 | Scenario 4 | Scenario 5.1 | Scenario 5.2 |
| elements | `111` | `110` | `011` | `111` | `11011` | |
| query | `011` | `011` | `011` | `011` | `01110` | `01110` |
|| Bitwise AND | Bitwise AND | Comparison == | Comparison == | Bitwise AND | Bitwise XOR |
| result | `011` | `010` | N/A | N/A | `01010` | `01010` |
| | true | false | true | false | false | `00100` result |

- In scenario 1, `elements` has bits **Fire**, **Water** and **Earth** set, and we are querying for at least **Fire** and **Water**, hence, the query is true.
- In scenario 2, `elements` has bits **Water** and **Earth** set, and we are querying for at least **Fire** and **Water**, hence, the query is false.
- In scenario 3, `elements` has bits **Fire** and **Water** set, and we are querying only for **Fire** and **Water**, hence, the query is true.
- In scenario 4, `elements` has bits **Fire**, **Water** and **Earth** set, and we are querying only for **Fire** and **Water**, hence, the query is false.
- In scenario 5.1, `elements` has five bits: **A**, **B**, **C**, **D**, **E**, with **A**, **B**, **D** and **E** set. We are querying for at least **B**, **C** and **D**, hence, the query is false. In scenario 5.1 we find out which elements of the query are `true`.
- In scenario 5.2 we want to find out which bits are making the query `false` in scenario 5.1, so we query again **B**, **C** and **D** with `XOR` againts the result of scenario 5.1. The final result is **C**, which is the bit that triggered `false` in scenario 5.1

#### Scenario 1 & 2 - Efficient version

```gdscript
var query: int = 0b011 # or `var query: int = 3`
if elements & query == query:
	print("True")
else:
    print("False")
```

#### Scenario 1 & 2 - Inefficient version

```gdscript
if (elements & 1<<0 && elements & (1<<0 + 1<<1)):
	print("True")
else:
    print("False")
```

With both examples shown above, you may have noticed that we could query large sets very easily using the efficient version. For instance, we could query for eight set bits easily:

```gdscript
var query: int = 0b00110010100110011 # or `var query: int = 25907`
if elements & query == query:
	print("True")
else:
    print("False")
```

Imagine querying the above using the inefficient version.

#### Scenario 3

```gdscript
if (elements == query):
	print("True")
else:
    print("False")
```

#### Scenario 5

This is useful if we want to list which elements we've got and which elements are missing to fulfill whatever condition presented by query.

```gdscript
var elements: int = 0b11011
var query: int = 0b01110
if elements & query == query:
	print("True")
else:
    print("False")
	print(elements)						# 11011		<- all the bits currently set
	print(query)						# 01110		<- bits asked/queried to fulfill condition
	print(elements & query)				# 01010		<- bits currently set (achieved) to fulfill condition
	print((elements & query) ^ query)	# 00100		<- bits currently unset (missing) to fulfill condition
```

- With `elements & query` we find out which elements of the query are `true`.
- With `(elements & query) ^ query` we find out which elements of the query are `false`.




## Example

If the user has enabled all elements (set all flags): Fire, Water and Earth in the Godot editor, the following script is an example of how to use bit flags.

You could use just left shift operators for the whole script, but in this case we use a combination of direct operators, left shift operators, custom functions and constants just to show how they can be used. In real life you should stick to only one style.


```gdscript

extends Node

@export_flags ("FIRE", "WATER", "EARTH") var elements: int = 0

const lsoFIRE = 1<<0
const lsoWATER = 1<<1
const lsoEARTH = 1<<2
const vFIRE = 1
const vWATER = 2
const vEARTH = 4


func _ready() -> void:
	print(("User selected: %d / %s") % [elements, String.num_int64(elements,2,false)])
	print(("%d is the 'elements' variable value and %s represents the 'elements' variable binary base conversion") % [elements, String.num_int64(elements,2,false)])
    print_elements()
	show_elements(elements)
	print("")
	
	# Clear bit 2 (WATER) using direct operator
	elements &= ~2
	print(("a) Clearing bit %d (WATER) using direct operator: %d / %s") % [2,elements, String.num_int64(elements,2,false)])
	
	# Set bit 2 (WATER) using left shift operator
	elements |= 1 << 1
	print(("b) Setting bit %d (WATER) using left shift operator: %d / %s") % [1<<1,elements, String.num_int64(elements,2,false)])
	
	# Flip bit 2 (WATER) using lso constant
	elements ^= lsoWATER
	print(("c) Flipping bit %d (WATER) using lso constant: %d / %s") % [lsoWATER, elements, String.num_int64(elements,2,false)])
	
	# Flip bit 2 (WATER) using v constant
	elements ^= vWATER
	print(("d) Flipping bit %d (WATER) using v constant: %d / %s") % [vWATER, elements, String.num_int64(elements,2,false)])
	
	# Set bit 2 (WATER) using custom function
	set_flag(2)
	print(("e) Setting bit %d (WATER) using custom function: %d / %s") % [2,elements, String.num_int64(elements,2,false)])
	
	# Clear bit 2 (WATER) using custom function
	clear_flag(1<<1)
	print(("f) Clearing bit %d (WATER) using custom function: %d / %s") % [1<<1,elements, String.num_int64(elements,2,false)])
	
	# Flip bit 2 (WATER) using custom function
	flip_flag(lsoWATER)
	print(("g) Flipping bit %d (WATER) using custom function: %d / %s") % [lsoWATER,elements, String.num_int64(elements,2,false)])
	print("")

	print("h) Checking if bit index 2 (WATER) is set using lso:")
	print("Water is set") if elements & (1<<1) else print("Water is not set")
	print("")
	
	print("i) Checking if bit index 2 (WATER) is not set using custom fuction:")
	print("Water is not set") if is_flag_unset(1<<1) else print("Water is set")


func print_elements():
	var elementsArray : Array[String] = ["Fire","Water","Earth"]
	var elementsPrint : Array[String] = []
	var elementsBinary: String = String.num_int64(elements,2,false).reverse()
	for i in elementsBinary.length():
		if elementsBinary[i] == "1":
			elementsPrint.append(elementsArray[i])
			print(elementsArray[i])
	print(elementsPrint)


# This function shows how direct operators, lso and constants can be used.
# Normaly you would stick to the same style and do not combine in order to avoid confussion.
func show_elements(type: int):
	match(type):
		0:
			print("N/A")
		1<<0:
			print("Fire")
		2:
			print("Water")
		vFIRE + vWATER:
			print("Fire and Water")
		4:
			print("Earth")
		1 + (1<<2):
			print("Fire and Earth")
		lsoWATER + lsoEARTH:
			print("Water and Earth")
		7:
			print("Fire, Water and Earth")


# Function to set a flag
func set_flag(flag):
	elements |= flag


# Function to clear a flag
func clear_flag(flag):
	elements &= ~flag


# Function to flip a flag
func flip_flag(flag):
	elements ^= flag


# Function to check if a flag is set
func is_flag_set(flag) -> bool:
	return (elements & flag) != 0


# Function to check if a flag is unset
func is_flag_not_set(flag) -> bool:
	return (elements & flag) == 0
```



Running the script will print:

```
User selected: 7 / 111
7 is the 'elements' variable value and 111 represents the 'elements' variable binary base conversion.
Fire
Water
Earth
["Fire", "Water", "Earth"]
Fire, Water and Earth

a) Clearing bit 2 (WATER) using direct operator: 5 / 101
b) Setting bit 2 (WATER) using left shift operator: 7 / 111
c) Flipping bit 2 (WATER) using lso constant: 5 / 101
d) Flipping bit 2 (WATER) using v constant: 7 / 111
e) Clearing bit 2 (WATER) using custom function: 5 / 101
f) Setting bit 2 (WATER) using custom function: 7 / 111
g) Flipping bit 2 (WATER) using custom function: 5 / 101

h) Checking if bit index 2 (WATER) is set using lso:
Water is not set

i) Checking if bit index 2 (WATER) is not set using custom fuction:
Water is not set
```

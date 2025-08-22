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

{{< tabs tabTotal="2">}}
{{% tab tabName="GDScript" %}}

```gdscript

@export_flags ("FIRE", "WATER", "EARTH") var elements: int
```

[Ref](https://docs.godotengine.org/en/stable/tutorials/scripting/gdscript/gdscript_exports.html#exporting-bit-flags)

{{% /tab %}}
{{% tab tabName="C#" %}}

```csharp
[Flags]
enum Elements { Fire = 1<<0, Water = 1<<1, Earth = 1<<2 } // Fire = 1, Water = 2, Earth = 4
```

{{% /tab %}}
{{< /tabs >}}


In the above code, we have created the integer variable `elements` which contains the flags: `FIRE`, `WATER` and `EARTH`. Each flag can be either `true` or `false`, or better said, set or unset.

In the graphic below there are five different representations of the first three bits of the `element` variable where the three flags (E, W, F) are contained.

{{< alert text="Flags are read from right to left, so `FIRE` is on the right, `WATER` is on the center and `EARTH` is on the left." />}}

```
__E___W___F__		__E___W___F__		__E___W___F__		__E___W___F__		__E___W___F__
| 0 | 0 | 1 |		| 0 | 1 | 0 |		| 0 | 1 | 1 |		| 1 | 0 | 0 |		| 1 | 0 | 1 |
¯¯¯¯¯¯¯¯¯¯¯¯¯		¯¯¯¯¯¯¯¯¯¯¯¯¯		¯¯¯¯¯¯¯¯¯¯¯¯¯		¯¯¯¯¯¯¯¯¯¯¯¯¯		¯¯¯¯¯¯¯¯¯¯¯¯¯
  4   2   1			  4   2   1			  4   2   1			  4   2   1			  4   2   1		variable "index"
  2   1   0			  2   1   0			  2   1   0			  2   1   0			  2   1   0		LSO "index"

1					2					3					4					5				variable value
1<<0				1<<1				1<<0 + 1<<1			1<<2				1<<0 + 1<<2		LSO
```


There are two ways to set the bits of the flags: using the `elements` variable value of type `int`, or using the left shift bit operator (LSO):

#### Variable Value

We can set 1, 2 or 3 bits at the same time by using the variable value. For the first three bits, values range from 1 to 7. The **value** is the sum of the *indexes*. I said *indexes* to help visualize the graphic above, but there are no actual indexes in the variable. So, if we want to set the first two bits, we sum 1 + 2. To set all three bits, we sum 1 + 2 + 4.

Example:

{{% alert context="light" %}}
To set **elements** variable flags to Fire and Water, which correspond to the first two bits:

- In decimal numbers: `elements = 3`
- In binary numbers: `elements = 0b011` or `elements = 0b11`
{{% /alert %}}

#### Left Shift Operator (LSO)

The LSO are better suited for setting individual bits. In order to set a bit with a LSO (among other operations), we need to use bitwise operators. In the next section we explain bitwise operators.

In the five examples above, we have set the variable `elements` to values from 1 to 5. In a bitwise operation, the equivalent LSOs to those elements variable values are the following:


| Flags affected | variable | LSO v1 | LSO v2 |
|-|-|-|-|
| FIRE | `1` | `1` | `1<<0` |
| WATER | `2` | `1<<1` | `1<<1` |
| FIRE and WATER | `3` | `1 + (1<<1)` | `(1<<0) + (1<<1)` |
| EARTH | `4` | `1<<2` | `1<<2` |
| FIRE and EARTH | `5` | `1 + (1<<2)` | `(1<<0) + (1<<2)` |



<br>
If we complete the table:

| Flags affected | variable | LSO v1 | LSO v2 |
|-|-|-|-|
| WATER and EARTH | `6` | `(1<<1) + (1<<2)` | `(1<<1) + (1<<2)` |
| FIRE, WATER and EARTH | `7` | `1 + (1<<1) + (1<<2)` | `(1<<0) + (1<<1) + (1<<2)` |


<br>
{{% alert context="light" %}}
In a bitwise operation, to affect the **elements** variable flag Fire, which correspond to the first bit, we use:

- In decimal numbers: `1`
- In LSO: `1<<0`
{{% /alert %}}
<br>
At the example script at the end of the page you can see how to use integer variable values and left shift operators in combination with constants, custom functions and the built-in match function.

## Bitwise Operations

| | AND | OR | XOR| NOT |
|-|-|-|-|-|
| | `x & y` | `x \| y` | `x ^ y` | `~x` |
| `x` | `11100` | `11100` | `11100` | `11100` |
| `y` | `10101` | `10101` | `10101` |  |
| **Result** | `10100` | `11101` | `01001` | `00011` |

<br>

The power of bit flags comes with bitwise operations. Depending on now we set the bit (variable value or LSO), it is performed slightly different. For this section, we are only setting one individual bit. Some explanation first:

`bitx` and `lshift` are made up indexes. There are no actual indexes in bit flags. `bitx` is to be replaced by an actual variable value: 1, 2 or 4 and represents the bit to be operated with. Since we are only setting one single bit, we don't take into account the values 3, 5, 6 and 7. `lshift` is to be replaced by an actual value: 0, 1 or 2 and represents the bit shifting towards the bit to be operated with.

```
__4___2___1__		__4___2___1__		__4___2___1__
| 0 | 0 | 1 |		| 0 | 1 | 0 |		| 1 | 0 | 0 |
¯¯¯¯¯¯¯¯¯¯¯¯¯		¯¯¯¯¯¯¯¯¯¯¯¯¯		¯¯¯¯¯¯¯¯¯¯¯¯¯
bitx = 1		    bitx = 2		    bitx = 4
lshift = 0		    lshift = 1		    lshift = 2
```


| Operation | Variable Value | Left Shift Operator | Same as |
|-|-|-|-|
| Set a bit | `elements \|= bitx` | `elements \|= 1 << lshift` | `elements = elements \| value` |
| Clear a bit | `elements &= ~bitx` | `elements &= ~(1 << lshift)` | `elements = elements & ~value`|
| Toggle a bit | `elements ^= bitx` | `elements ^= 1 << lshift` | `elements = elements ^ value`|
| are all bits set? | `if elements != 0:` | `if elements != 0:` | |
| is bit set? | `if elements & bitx:` | `if elements & (1 << lshift):` | |
| is bit not set? | `if (elements & (bitx == 0)):` | `if (elements & (1 << lshift == 0)):` | |


## Bitmasks (like queries)

Knowing how bit operations work, we can use bitmasks to query small or large bit sets. Continuing with our example set:

{{< tabs tabTotal="2">}}
{{% tab tabName="GDScript" %}}

```gdscript

@export_flags ("FIRE", "WATER", "EARTH") var elements: int
```

[Ref](https://docs.godotengine.org/en/stable/tutorials/scripting/gdscript/gdscript_exports.html#exporting-bit-flags)

{{% /tab %}}
{{% tab tabName="C#" %}}

```csharp
[Flags]
enum Elements { Fire = 1, Water = 2, Earth = 4 } // Fire = 1<<0, Water = 1<<1, Earth = 1<<2
```

{{% /tab %}}
{{< /tabs >}}

As per `elements`, **Fire** and **Water** are represented by the value `011` in binary and `3` in decimal (remember that we read from right to left in binary).


- In order to check if **AT LEAST** `Fire` and `Water` are set in `elements`, we perform a bitwise `AND` operation between the value of `elements` againts the value `011` using the bitwise operator `&`. In this operation, `011` is the bitmask or "query".
- In order to check if **ONLY/EXACTLY** `Fire` and `Water` are set in `elements`, we compare the value of `elements` with the value `011` using the comparison operator `==`. In this operation, `011` is the bitmask or "query".

From now on, "query" and "querying" are used instead of **bitmask** and **bitmasking**. It's easier to understand as a concept for game development.

||||| | | |
|-|-|-|-|-|-|-|
|| Scenario 1 | Scenario 2 | Scenario 3 | Scenario 4 | Scenario 5.1 | Scenario 5.2 |
| elements | `111` | `110` | `011` | `111` | `11011` | |
| query | `011` | `011` | `011` | `011` | `01110` | `01110` |
|| Bitwise AND | Bitwise AND | Comparison == | Comparison == | Bitwise AND | Bitwise XOR |
| result | `011` | `010` | N/A | N/A | `01010` | `01010` |
| | true | false | true | false | false | `00100` result |


<br>

- In scenario 1, `elements` has bits **Fire**, **Water** and **Earth** set, and we are querying for at least **Fire** and **Water**, hence, the query is true.
- In scenario 2, `elements` has bits **Water** and **Earth** set, and we are querying for at least **Fire** and **Water**, hence, the query is false.
- In scenario 3, `elements` has bits **Fire** and **Water** set, and we are querying only for **Fire** and **Water**, hence, the query is true.
- In scenario 4, `elements` has bits **Fire**, **Water** and **Earth** set, and we are querying only for **Fire** and **Water**, hence, the query is false.
- In scenario 5.1, `elements` has five bits: **A**, **B**, **C**, **D**, **E**, with **A**, **B**, **D** and **E** set. We are querying for at least **B**, **C** and **D**, hence, the query is false. In scenario 5.1 we find out which elements of the query are `true`.
- In scenario 5.2 we want to find out which bits are making the query `false` in scenario 5.1, so we query again **B**, **C** and **D** with `XOR` againts the result of scenario 5.1. The final result is **C**, which is the bit that triggered `false` in scenario 5.1

#### Scenario 1 & 2
{{< tabs tabTotal="2">}}
{{% tab tabName="GDScript" %}}

```gdscript
var query: int = 0b011 # or `var query: int = 3`
if query & elements == query:
	print("True")
else:
    print("False")
```
You may have noticed that we could query large sets very easily. For instance, we could query for nine particular bits in a 17 bits group:

```gdscript
var query: int = 0b10110010100110011 # or `var query: int = 91443`
if query & elements == query:
	print("True")
else:
    print("False")
```

{{% /tab %}}
{{% tab tabName="C#" %}}

```csharp
int query = 0b011; // or `int query = 3`
if ((query & (int)elements) == query) {
	GD.Print("True");
}
else {
	GD.Print("False");
}
```
You may have noticed that we could query large sets very easily. For instance, we could query for nine particular bits in a 17 bits group:

```csharp
int query = 0b10110010100110011; // or `int query = 91443`
if ((query & (int)elements) == query) {
	GD.Print("True");
}
else {
	GD.Print("False");
}
```
{{% /tab %}}
{{< /tabs >}}


#### Scenario 3

{{< tabs tabTotal="2">}}
{{% tab tabName="GDScript" %}}

```gdscript
if (query == elements):
	print("True")
else:
    print("False")
```

{{% /tab %}}
{{% tab tabName="C#" %}}

```csharp
if (query == (int)elements) {
	GD.Print("True");
}
else {
	GD.Print("False");
}
```

{{% /tab %}}
{{< /tabs >}}

#### Scenario 5

This is useful if we want to list which elements we've got and which elements are missing to fulfill whatever condition presented by query.

{{< tabs tabTotal="2">}}
{{% tab tabName="GDScript" %}}

```gdscript
var elements: int = 0b11011
var query: int = 0b01110
if query & elements == query:
	print("True")
else:
    print("False")
	print(elements)						# 11011		<- all the bits currently set
	print(query)						# 01110		<- bits asked/queried to fulfill condition
	print(query & elements)				# 01010		<- bits currently set (achieved) to fulfill condition
	print((query & elements) ^ query)	# 00100		<- bits currently unset (missing) to fulfill condition
```


{{% /tab %}}
{{% tab tabName="C#" %}}

```csharp
int elements = 0b11011;
int query = 0b01110;
if ((query & elements) == query) {
	GD.Print("True");
}
else {
	GD.Print("False");
	GD.Print(elements);                    // 11011		<- all the bits currently set
	GD.Print(query);                       // 01110		<- bits asked/queried to fulfill condition
	GD.Print(query & elements);            // 01010		<- bits currently set (achieved) to fulfill condition
	GD.Print((query & elements) ^ query);  // 00100		<- bits currently unset (missing) to fulfill condition
}
```

{{% /tab %}}
{{< /tabs >}}

- With `query & elements` we find out which elements of the query are `true`.
- With `(query & elements) ^ query` we find out which elements of the query are `false`.




## Example

If the user has enabled all elements (set all flags): Fire, Water and Earth in the Godot editor, the following script is an example of how to use bit flags.

You could use just left shift operators for the whole script, but in this case we use a combination of variable values, left shift operators, custom functions and constants just to show how they can be used. In real life you should stick to only one style.


{{< tabs tabTotal="2">}}
{{% tab tabName="GDScript" %}}

```gdscript

extends Node

@export_flags ("FIRE", "WATER", "EARTH") var elements: int = 7

const lsoFIRE = 1<<0
const lsoWATER = 1<<1
const lsoEARTH = 1<<2
const vFIRE = 1  # or 0b001
const vWATER = 2 # or 0b010
const vEARTH = 4 # or 0b100

func _ready() -> void:
	print(("User selected: %d / %s") % [elements, String.num_int64(elements,2,false)])
	print(("%d is the 'elements' variable value and %s represents the 'elements' variable binary base conversion") % [elements, String.num_int64(elements,2,false)])
	print_elements()
	show_elements(elements)
	print("")

	# Clear bit 2 (WATER) using variable value in decimal
	elements &= ~2
	print(("a) Clearing bit %d (WATER) using decimal variable value: %d / %s") % [2,elements, String.num_int64(elements,2,false)])

	# Set bit 2 (WATER) using left shift operator
	elements |= 1 << 1
	print(("b) Setting bit %d (WATER) using left shift operator: %d / %s") % [1<<1,elements, String.num_int64(elements,2,false)])

	# Toggle bit 2 (WATER) using lso constant
	elements ^= lsoWATER
	print(("c) Toggling bit %d (WATER) using lso constant: %d / %s") % [lsoWATER, elements, String.num_int64(elements,2,false)])

	# Setting bit 2 (WATER) using variable value in binary
	elements |= 0b010
	print(("d) Setting bit %d (WATER) using binary variable value: %d / %s") % [0b010, elements, String.num_int64(elements,2,false)])

	# Clearing bit 2 (WATER) using custom function with decimal variable value
	clear_flag(2)
	print(("e) Clearing bit %d (WATER) using custom function with decimal variable value: %d / %s") % [2,elements, String.num_int64(elements,2,false)])

	# Setting bit 2 (WATER) using custom function with lso
	set_flag(1<<1)
	print(("f) Setting bit %d (WATER) using custom function with lso: %d / %s") % [1<<1,elements, String.num_int64(elements,2,false)])

	# Toggle bit 2 (WATER) using custom function with variable value constant
	toggle_flag(vWATER)
	print(("g) Toggling bit %d (WATER) using custom function with variable value constant: %d / %s") % [vWATER,elements, String.num_int64(elements,2,false)])
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


# This function shows how variable values, lso and constants can be used.
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
		0b100:
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


# Function to toggle a flag
func toggle_flag(flag):
	elements ^= flag


# Function to check if a flag is set
func is_flag_set(flag) -> bool:
	return (elements & flag) != 0


# Function to check if a flag is unset (clear)
func is_flag_unset(flag) -> bool:
	return (elements & flag) == 0
```

<br>

Running the script will print:

```
User selected: 7 / 111
7 is the 'elements' variable value and 111 represents the 'elements' variable binary base conversion
Fire
Water
Earth
["Fire", "Water", "Earth"]
Fire, Water and Earth

a) Clearing bit 2 (WATER) using decimal variable value: 5 / 101
b) Setting bit 2 (WATER) using left shift operator: 7 / 111
c) Toggling bit 2 (WATER) using lso constant: 5 / 101
d) Setting bit 2 (WATER) using binary variable value: 7 / 111
e) Clearing bit 2 (WATER) using custom function with decimal variable value: 5 / 101
f) Setting bit 2 (WATER) using custom function with lso: 7 / 111
g) Toggling bit 2 (WATER) using custom function with variable value constant: 5 / 101

h) Checking if bit index 2 (WATER) is set using lso:
Water is not set

i) Checking if bit index 2 (WATER) is not set using custom fuction:
Water is not set
```

{{% /tab %}}
{{% tab tabName="C#" %}}

```csharp

using Godot;
using System;

[Flags]
enum Elements { Fire = 1<<0, Water = 1<<1, Earth = 1<<2 }

public partial class World : Node
{
	Elements elements = (Elements)7;
	const int lsoWATER = 1<<1;
	
    public override void _Ready()
    {
		GD.Print($"User selected {(int)elements} / {Convert.ToString((int)elements,2).PadLeft(3, '0')}");
		GD.Print($"{(int)elements} is the 'elements' variable value and {Convert.ToString((int)elements,2).PadLeft(3, '0')} represents the 'elements' variable binary base conversion");
		GD.Print(elements);
		GD.Print("");

		// Clear bit 2 (Water) using variable value in decimal
		elements &= ~(Elements)2;
		GD.Print($"a) Clearing bit 2 (Water) using decimal variable value. {(int)elements} / {Convert.ToString((int)elements,2)}");

		// Set bit 2 (Water) using enum flag
		elements |= Elements.Water;
		GD.Print($"b) Setting bit 2 (Water) using enum flag. {(int)elements} / {Convert.ToString((int)elements,2)}");

		// Toggle bit 2 (Water) using lso constant
		elements ^= (Elements)lsoWATER;
		GD.Print($"c) Toggling bit 2 (Water) using lso constant. {(int)elements} / {Convert.ToString((int)elements,2)}");

		// Set bit 2 (Water) using binary variable value
		elements |= (Elements)0b010;
		GD.Print($"d) Setting bit 2 (Water) using binary variable value. {(int)elements} / {Convert.ToString((int)elements,2)}");

		// Clear bit 2 (WATER) using custom function with decimal variable value
		ClearFlag(2);
		GD.Print($"e) Clearing bit 2 (Water) using custom function with decimal variable value. {(int)elements} / {Convert.ToString((int)elements,2)}");

		// Set bit 2 (Water) using custom function with lso
		SetFlag(1<<1);
		GD.Print($"f) Setting bit 2 (Water) using custom function with lso. {(int)elements} / {Convert.ToString((int)elements,2)}");

		// Toggle bit 2 (Water) using custom function with variable value constant
		ToggleFlag(vWater);
		GD.Print($"g) Toggling bit 2 (Water) using custom function with variable value constant. {(int)elements} / {Convert.ToString((int)elements,2)}");
    }

   	void SetFlag(int flag) { elements |= (Elements)flag; }
	void ClearFlag(int flag) { elements &= ~(Elements)flag; }
	void ToggleFlag(int flag) { elements ^= (Elements)flag; }
	bool IsFlagSet(int flag) { return (elements & (Elements)flag) != 0; }
	bool IsFlagUnset(int flag) { return (elements & (Elements)flag) == 0; }
}

```

<br>

Running the script will print:

```
User selected 7 / 111
7 is the 'elements' variable value and 111 represents the 'elements' variable binary base conversion
Fire, Water, Earth

a) Clearing bit 2 (Water) using decimal variable value. 5 / 101
b) Setting bit 2 (Water) using enum flag. 7 / 111
c) Toggling bit 2 (Water) using lso constant. 5 / 101
d) Setting bit 2 (Water) using binary variable value. 7 / 111
e) Clearing bit 2 (Water) using custom function with decimal variable value. 5 / 101
f) Setting bit 2 (Water) using custom function with lso. 7 / 111
g) Toggling bit 2 (Water) using custom function with variable value constant. 5 / 101
```

{{% /tab %}}
{{< /tabs >}}




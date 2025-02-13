---
weight: 3200
title: "Arrays"
description: "Working with arrays"
icon: "article"
date: "2024-09-06T00:32:33+02:00"
lastmod: "2024-09-06T00:32:33+02:00"
draft: false
toc: true
---

[Godot Documentation](https://docs.godotengine.org/en/stable/classes/class_array.html)

### Methods

Some methods:

- `append(value)`: Appends `value` at the end of the array.
- `push_back(value)`: Appends `value` at the end of the array.
- `push_front(value)`: Adds `value` at the beginning of the array.
- `erase(value)`: Finds and removes the first occurrence of `value` from the array.
- `front()`: Returns the first element of the array.
- `back()`: Returns the last element of the array.
- `pop_back()`: Removes the last element of the array.
- `pop_front()`: Removes the first element of the array.
- `clear()`: Removes all elements of the array.
- `count(value)`: Returns the number of times an element is in the array.
- `has(value)`: Returns `true` if the array contains the given `value`.
- `is_empty()`: Returns `true` if the array is empty ([]).
- `pick_random()`: Returns a random element from the array. Generates an error and returns `null` if the array is empty.
- `reverse()`: Reverses the order of all elements in the array.
- `shuffle()`: Shuffles all elements of the array in a random order.
- `size()`: Returns the number of elements in the array.
- `sort()`: Sorts the array in ascending order. The final order is dependent on the "less than" (<) comparison between elements.

### Callable Methods

Some callable methods:

- [all(method: Callable)](https://docs.godotengine.org/en/stable/classes/class_array.html#class-array-method-all)
- [any(method: Callable)](https://docs.godotengine.org/en/stable/classes/class_array.html#class-array-method-any)
- [filter(method: Callable)](https://docs.godotengine.org/en/stable/classes/class_array.html#class-array-method-filter)
- [sort_custom(func: Callable)](https://docs.godotengine.org/en/stable/classes/class_array.html#class-array-method-sort-custom)


### Basic inventory

```gdscript
...

var inventory: Array[String] = ["Coin", "Potion", "Potion", "Coin", "Potion", "Gem", "Potion", "Coin", "Gem"]

...

func show_inventory():
	var a: String
	inventory.sort()
	for i in inventory:
		if a != i: 
			print(i + " x" + str(inventory.count(i)))
		a = i 
```

It will print:
```
Coin x3
Gem x2
Potion x4
```

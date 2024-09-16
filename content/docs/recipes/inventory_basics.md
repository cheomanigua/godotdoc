---
weight: 5175
title: "Inventory - Basics"
description: "Create a very basic inventory"
icon: "article"
date: "2024-09-03T17:05:47+02:00"
lastmod: "2024-09-03T17:05:47+02:00"
draft: false
toc: true
---

We are going to implement the inventory using two methods. The first method uses an **Array** as an inventory, and the second method uses an **Dictionary** as an inventory.

## 1. Array inventory

### Setup
- The input action **inventory** has been mapped to key `I`.
- The code is meant to be appended to the [previous chapter](items_basics) **player.gd** script.

### Interaction
- When player presses key `I`, an inventory dialog is shown. Pressing key `I` again will close the inventory dialog.

### Node Hierarchy
```
[Node] "Main"
		|-[CharacterNode2D] "Player"
		|-[CanvasLayer]
				|-[ItemList]
				|-[Label] "InventoryList"
```

#### player.gd

```gdscript
extends CharacterBody2D

...

var inventory: Array[String]
var inventory_activated: bool = false
@onready var item_list: ItemList = %ItemList
@onready var inventory_list: Label = %InventoryList

func _ready():
	item_list.hide()

func _input(event: InputEvent) -> void:
	...
	if event.is_action_pressed("inventory"):
		inventory_activated = !inventory_activated
		show_inventory()
	...

	...

func show_inventory():
	if inventory_activated:
		for i in inventory:
			item_list.add_item(i)
		item_list.show()
	else:
		item_list.hide()
		item_list.clear()
```

Another version of **show_inventory()**, where **inventory_list** is a **Label**, instead of a **ItemList**:

```gdscript

func show_inventory():
	inventory_list.text = ""
	if inventory_activated:
		if inventory.is_empty():
			inventory_activated = false
		else:
			inventory_list.show()
			var a: String
			inventory.sort()
			for i in inventory:
				if a != i:
					inventory_list.text += "%s x%d\n" % [i, inventory.count(i)]
				a = i
	else:
		inventory_list.hide()
```

## 2. Dictionary inventory

The code below only has the part related to make it work with a **Dictionary** instead of an **Array**. Everything else is the same as the previous code above.

#### player.gd

```gdscript

var inventory: Dictionary

...

func pickup(item: String) -> void:
	if inventory.has(item):
		inventory[item] += 1
	else:
		inventory[item] = 1

...

func show_inventory():
	inventory_list.text = ""
	if inventory_activated:
		if inventory.is_empty():
			inventory_activated = false
		else:
			inventory_list.show()
			for key in inventory:
				inventory_list.text += "%s : %d\n" % [key, inventory[key]]
	else:
		inventory_list.hide()
```

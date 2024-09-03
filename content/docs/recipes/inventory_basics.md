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
```

#### player.gd

```gdscript
extends CharacterBody2D

...

var inventory: Array[String]
var inventory_activated: bool = false
@onready var item_list: ItemList = %ItemList

func _ready():
	item_list.hide()

func get_input():
	...
	if Input.is_action_just_pressed("inventory"):
		inventory_activated = !inventory_activated
		show_inventory()
	...

	...

func show_inventory():
	if inventory_activated:
		item_list.show()
		for i in inventory:
			item_list.add_item(i)
	else:
		item_list.hide()
		item_list.clear()
```

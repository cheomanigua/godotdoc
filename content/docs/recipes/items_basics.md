---
weight: 5150
title: "Items - Basics"
description: "How to manage items: pickup, inventory, interactivity, drop"
icon: "article"
date: "2024-09-03T09:51:45+02:00"
lastmod: "2024-09-03T09:51:45+02:00"
draft: false
toc: true
---

## Interaction
- There is a player, a door and a key.
- The player can pickup the key.
- The key is unique.
- The door can only be open if the Player has the unique key.


## Implementation

We are going to implement the solution in two different ways. The first solution uses a **String** array inventory. The second solution uses an **Area2D** array inventory.

## Nodes

- [CharacterBody2D] **"Player"**, with a [CollisionShape2D]
- [Area2D] **"Door"**, with a [CollisionShape2D]
- [Area2D] **"Iron Key"**, with a [CollisionShape2D]

### Solution 1. Interacting with String array

#### Key
- The key is an **Area2D** node.
- The **Area2D** name is what gives uniquenes to the object.
- When picked up, the **Area2D** node name is added to a **String** array.

#### Door
- The door can only be opened if the key node has a particular name. 

#### Example
- In Solution 1, the Key node is named **"Iron Key"**.
- In Solution 1, the Door can only be opened if the key node is named **"Iron Key"**.

#### player.gd
```gdscript
extends CharacterBody2D

var inventory: Array[String]

func _input(event):
	if Input.is_action_pressed("ui_select"):
		print_inventory()
	#if Input.is_action_pressed("drop"):
	#	drop_item()
...

func pickup(item:String):
	inventory.append(item)

func print_inventory():
	for i in inventory:
		print(i)
	if inventory.is_empty():
		print("Your inventory is empty")

#func drop_item():
#	for i in inventory:
#		if i.item_name == "Iron Key":
#			i.global_position = global_position + Vector2(0, -50)
#			i.show()
#			inventory.erase(i)
```

#### door.gd

```gdscript
extends Area2D

func _ready() -> void:
	body_entered.connect(_on_body_entered)

func _on_body_entered(body):
	if body.inventory.has("Iron Key"):
		print("You've got the key")
```

#### key.gd

```gdscript
extends Area2D

func _ready() -> void:
	body_entered.connect(_on_body_entered)

func _on_body_entered(body):
	if body.has_method("pickup"):
		body.pickup(name)
		queue_free()
```



### Solution 2. Interacting with Object array

#### Key
- The key is an **Area2D** with a **String** variable called `item_name`
- `item_name` is what gives uniquenes to the object and can be changed in the Godot editor.

#### Door
- The door can only be opened if the key has a particular `item_name`.

#### Example
- In Solution 2, the Key has an `item_name` with the value of **"Iron Key"**.
- In Solution 2, the Door can only be opened if the key as an `item_name` with the value of **"Iron Key"**.

#### player.gd

```gdscript
extends CharacterBody2D

var inventory: Array[Area2D]

func _input(event):
	if Input.is_action_pressed("ui_select"):
		print_inventory()
	#if Input.is_action_pressed("drop"):
	#	drop_item()
...

func pickup(item:Area2D):
	inventory.append(item)

func print_inventory():
	for i in inventory:
		print(i.item_name)
	if inventory.is_empty():
		print("Inventory is empty")
#	
#func drop_item():
#	for i in inventory:
#		if i.item_name == "Iron Key":
#			i.global_position = global_position + Vector2(0, -50)
#			i.show()
#			inventory.erase(i)
```

#### door.gd

```gdscript
extends Area2D

func _ready() -> void:
	body_entered.connect(_on_body_entered)

func _on_body_entered(body):
	for i in body.inventory:
		if i.item_name == "Iron Key":
			print("You've got the key")
```

#### key.gd

```gdscript
extends Area2D

@export var item_name:String

func _ready() -> void:
	body_entered.connect(_on_body_entered)

func _on_body_entered(body):
	if body.has_method("pickup"):
	body.pickup(self)
	self.hide()
```

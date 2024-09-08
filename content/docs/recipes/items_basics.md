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

- There is a player, a key and a door.
- The player can pickup the key.
- The key has a unique name.
- The door can only be open if the Player has the key with the unique name.

#### Key
- The **Key** is an **Area2D** node.
- The **Key** has an editable **String** variable called `item_name`. We must type a name in the editor for the `item_name`.
- The **Key** `item_name` is what gives uniquenes to the item.
- When picked up, the **Key** `item_name` is added to a **String** array inventory.
- When used, the **Key** is eliminated from the inventory.

#### Door
- The **Door** is an **Area2D** node with a child **StaticBody2D** for collision purposes. The **Area2D** is slighty bigger than the **StaticBody2D**.
- The **Door** has an editable **String** variable called `door_key`. We mus type a name in the edit or for the `door_key`.
- The **Door** can only be open if the **Key** `item_name` string matches the **Door** `door_key` string. 
- When opened, the **Door** is eliminated.

#### Examples
In the implementation section, take into account:
- The **Key** `item_name` is **"Iron Key"**.
- The **Door** `door_key` is **"Iron Key"**. 


## Node Structure

#### Player
```
[CharacterBody2D] "Player"
		|-[CollisionShape2D]
```

#### Key
```
[Area2D] "Iron Key"
		|-[CollisionShape2D]
```

#### Door
```
[Area2D] "Door"
		|-[CollisionShape2D]
		|-[StaticBody2D]
				|-[CollisionShape2D]
```


## Implementation

We are going to implement the solution in two different ways. The first solution uses a **String** array inventory. The second solution uses an **Area2D** array inventory.

### Solution 1. Interacting with String array

The **Key** `item_name` is stored in a **String** array.

#### player.gd
```gdscript
extends CharacterBody2D

var inventory: Array[String]

func _unhandled_key_input(event: InputEvent) -> void:
	if event.is_action_pressed("ui_select"):
		print_inventory()
	#if event.is_action_pressed("drop"):
	#	drop_item()
...

func pickup(item:String):
	inventory.append(item)

func print_inventory():
	for item in inventory:
		print(item)
	if inventory.is_empty():
		print("Your inventory is empty")

#func drop_item():
#	for i in inventory:
#		if i.item_name == "Iron Key":
#			i.global_position = global_position + Vector2(0, -50)
#			i.show()
#			inventory.erase(i)
```

#### key.gd

```gdscript
extends Area2D

@export var item_name:String # Don'f forget to use the Godot editor to add a unique name for the key

func _ready() -> void:
	body_entered.connect(_on_body_entered)

func _on_body_entered(body):
	if body.has_method("pickup"):
		body.pickup(item_name)
		queue_free()
```

#### door.gd

```gdscript
extends Area2D

@export var door_key: String

func _ready() -> void:
	body_entered.connect(_on_body_entered)

func _on_body_entered(body):
	if body.name == "Player":
		if body.inventory.has(door_key):
			print("The door has been open with the %s" % [door_key])
			body.inventory.erase(door_key)
			queue_free()
		else:
			print("You need the %s" % [door_key])
```


### Solution 2. Interacting with Area2D array

The **Key** node is stored in an **Area2D** array.

#### player.gd

```gdscript
extends CharacterBody2D

var inventory: Array[Area2D]

func _unhandled_key_input(event: InputEvent) -> void:
	if event.is_action_pressed("ui_select"):
		print_inventory()
	#if event.is_action_pressed("drop"):
	#	drop_item()
...

func pickup(item:Area2D):
	inventory.append(item)

func print_inventory():
	for item in inventory:
		print(item.item_name)
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

#### key.gd

```gdscript
extends Area2D

@export var item_name:String # Don'f forget to use the Godot editor to add a unique name for the key

func _ready() -> void:
	body_entered.connect(_on_body_entered)

func _on_body_entered(body):
	if body.has_method("pickup"):
		body.pickup(self)
		hide()
```

#### door.gd

```gdscript
extends Area2D

@export var door_key: String

func _ready() -> void:
	body_entered.connect(_on_body_entered)

func _on_body_entered(body):
	if body.name == "Player":
		if body.inventory.is_empty():
			print("You need the %s to pass" % [door_key])
		else:
			for i in body.inventory:
				if i.item_name == door_key:
					print("The door has been open with the %s" % [door_key])
					queue_free()
					body.inventory.erase(i)
					i.queue_free()
				else:
					print("You need the %s" % [door_key])
```


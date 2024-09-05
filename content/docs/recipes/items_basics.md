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
- The **Key** has a editable **String** variable called `item_name`. We must type a name in the editor for the key.
- The **Key** `item_name` is what gives uniquenes to the object.
- When picked up, the **Key** `item_name` is added to a **String** array.
- When used, the **Key** is eliminated.

#### Door
- The **Door** is a **StaticBody2D** with and child **Area2D** for detection purposes. The **Area2D** is slighty bigger than the **StaticBody2D**.
- The **Door** can only be opened if the **Key** has a specific `item_name`. 
- When opened, the **Door** is eliminated.

#### Examples
In the implementation section, take into account:
- The **Key** `item_name` is **"Iron Key"**.
- The **Door** can only be opened if the **Key** `item_name` is **"Iron Key"**.

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
[StaticBody2d] "Door"
		|-[CollisionShape2D]
		|-[Area2D] "door.gd"
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

func _ready() -> void:
	body_entered.connect(_on_body_entered)

func _on_body_entered(body):
	if body.name == "Player":
		if body.inventory.has("Iron Key"):
			print("You've got the key. You may pass")
			owner.queue_free()
		else:
			print("You shall not pass. You need the Iron Key")
```


### Solution 2. Interacting with Object array

The **Key** node is stored in a **Area2D** array.

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

func _ready() -> void:
	body_entered.connect(_on_body_entered)

func _on_body_entered(body):
	if body.name == "Player":
		if body.inventory.is_empty():
			print("You need the Iron Key. You shall not pass")
		else:
			for i in body.inventory:
				if i.item_name == "Iron Key":
					print("You've got the Iron Key. You shall pass")
					owner.queue_free()
					body.inventory.erase(i)
					i.queue_free()
				else:
					print("You need the Iron Key. You shall not pass")
```


---
weight: 5200
title: "Pickup & Grab"
description: "In this recipe I’ll show two ways to pickup objects"
icon: "article"
date: "2024-08-29T10:17:13+02:00"
lastmod: "2024-08-29T10:17:13+02:00"
draft: false
toc: true
---

## 1. Picking up items by moving on top of it and pressing a key

Let's see how we can detect all objects:

### How to detect all objects within Player's Area2D
```gdscript
for body in $Area2D.get_overlapping_bodies():
```
or
```gdscript
for body in $Area2D.get_overlapping_areas():
```

Knowing this, we implement the following node structures and scripts:

### Item

> - Sprite (*Item*) (*item.gd*)
>   - Area2D
>	    - CollisionShape2D

```gdscript
tool

extends Sprite

export (String) var item_name
export (int) var item_quantity = 1

func _ready():
	add_to_group("items")
```

### Player

> - CharacterBody2D (*Player*) (*player.gd*)
>   - Area2D
>	    - CollisionShape2D

```gdscript
var inventory:Dictionary = {}

func add_item(name,quantity):
	if inventory.has(name):
		inventory[name] += quantity
	else:
		inventory[name] = quantity

func _unhandled_input(event):
	if (event.is_action_pressed("pickup")):
		pickup()

func pickup():
	for body in $Area2D.get_overlapping_areas():
		if body.get_parent().is_in_group("items"):
			var name = body.get_parent().item_name
			var quantity = body.get_parent().item_quantity
			add_item(name,quantity)
			body.get_parent().queue_free()
```

## 2. Grabbing items by moving on top of it

### Item

> - Sprite (*Item*) (*item.gd*)
>   - Area2D
>	    - CollisionShape2D

```gdscript
tool

extends Sprite

export (String) var item_name
export (int) var item_quantity = 1

func pickup(item):
# warning-ignore:return_value_discarded
	body_entered.connect(_on_body_entered)

func _on_body_entered(body):
	if body.name == "Player":
		body.add_item(item_name,item_quantity)
		queue_free()
```

### Player

> - CharacterBody2D (*Player*) (*player.gd*)
>   - Area2D
>	    - CollisionShape2D

```gdscript
var inventory:Dictionary = {}

func add_item(name,quantity):
	if inventory.has(name):
		inventory[name] += quantity
	else:
		inventory[name] = quantity
```

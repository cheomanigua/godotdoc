---
weight: 3100
title: "Enums"
description: "Enums are mistake-free attributes"
icon: "article"
date: "2024-09-16T12:01:20+02:00"
lastmod: "2024-09-16T12:01:20+02:00"
draft: false
toc: true
---

- In Godot, enums are useful for states, status, roles, game modes.
- By using enums, the compiler will instantly catch an error when checking againts an enum constant.


## @export vs @export_enum

#### @export

The enum type is a dictionary and the @export type is an integer:

```gdscript

enum attributes { STRENGTH, INTELLIGENCE, DEXTERITY }
@export var attribute: attributes = attributes.STRENGTH
```

#### @export_enum

The @export_enum type can be either an integer or a string:


```gdscript

@export_enum ("strength", "intelligence", "dexterity") var attributes: int = 0
@export_enum ("strength", "intelligence", "dexterity") var attributes: String = "Health"
```
[Ref](https://docs.godotengine.org/en/stable/tutorials/scripting/gdscript/gdscript_exports.html#exporting-enums)

In the following examples, when the player pickups an item, it will increase the player's attribute.

Exporting the **enum** will let the developer choose the attribute for the player from a drop down menu in the Godot editor.


## Examples

### 1. @export

#### item.gd

```gdscript

extends Area2D

enum attributes {STRENGTH, INTELLIGENCE, DEXTERITY}
@export var attribute: attributes = attributes.STRENGTH
@export var item_name: String
@export var attribute_value: float = 0


func _ready() -> void:
	self.body_entered.connect(_on_body_entered)


func _on_body_entered(body):
	var attribute_name: String = str(attributes.keys()[attribute]).capitalize()
	if body.has_method("pickup"):
		body.pickup(item_name) # Just to show how to pickup an item. It is not necessary as the item is consumed instantly
		body.change_attribute(attribute_name, attribute_value)
		print("You've picked a %s" % [item_name])
		print("Your %s has been increased by %s points" % [attribute_name, attribute_value])
		queue_free()
```


#### player.gd

```gdscript

extends CharacterBody2D

var inventory: Array[String]
const SPEED = 300.0

@export var attributes: Dictionary = {
	"Strength" : 5,
	"Intelligence" : 5,
	"Dexterity" : 5
}


func pickup(item: String) -> void:
	inventory.append(item) # Item should not be added to inventory as it has been consumed
	print(inventory) # but I add it anyway just for an inventory's sake


func change_attribute(attribute: String, value: float) -> void:
	attributes[attribute] += value
	print(attributes)


func _physics_process(_delta: float) -> void:
	var input_direction = Input.get_vector("left", "right", "up", "down")
	velocity = input_direction * SPEED
	move_and_slide()
```



### 2. @export_enum

#### item.gd

```gdscript
extends Area2D

@export_enum("strength", "intelligence", "dexterity") var attributes: String = "strength"
@export var item_name: String
@export var attribute_value: int = 0


func _ready() -> void:
	self.body_entered.connect(_on_body_entered)


func _on_body_entered(body):
	if "pickup" in body:
		body.pickup(item_name)
		body.change_attribute(attributes, attribute_value)
		print("You've picked up a %s" % [item_name])
		print("Your %s has been increased by %s points" % [attributes, attribute_value])
		queue_free()
```

#### player.gd

```gdscript

extends CharacterBody2D

var inventory: Array[String]
const SPEED = 300.0

@export var attributes: Dictionary = {
	"strength" : 5,
	"intelligente" : 5,
	"dexterity" : 5,
}


func pickup(item: String) -> void:
	inventory.append(item) # Item should not be added to inventory as it has been consumed
	print(inventory) # but I add it anyway just for an inventory's sake


func change_attribute(attribute: String, attribute_value: int):
		attributes[attribute] += attribute_value
		print(attributes)


func _physics_process(_delta):
	var input_direction = Input.get_vector("left", "right", "up", "down")
	velocity = input_direction * SPEED
	move_and_slide()
```


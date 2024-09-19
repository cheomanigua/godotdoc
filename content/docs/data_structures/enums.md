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

The enum values are integers:

```gdscript

enum attributes { HEALTH, STRENGTH, INTELLIGENCE, DEXTERITY }
@export var attribute: attributes = attributes.HEALTH
```

#### @export_enum

The enum values can be integers or strings:


```gdscript

@export_enum ("health", "strengh", "intelligence", "dexterity") var attributes: int = 0
@export_enum ("health", "strengh", "intelligence", "dexterity") var attributes: String = "Health"
```
[Ref](https://docs.godotengine.org/en/stable/tutorials/scripting/gdscript/gdscript_exports.html#exporting-enums)

In the following examples, when the player pickups an item, it will increase the player's attribute.

Exporting the **enum** will let the player choose from a drop down menu the attribute in the Godot editor.


## Examples

### 1. @export_enum

#### item.gd

```gdscript
extends Area2D

@export_enum("max_health", "health", "strength", "intelligence", "dexterity") var attributes: String = "health"
@export var item_name: String
@export var attribute_value: int = 0


func _ready() -> void:
	body_entered.connect(_on_body_entered)


func _on_body_entered(body):
	if "pickup" in body:
		body.pickup(item_name)
		if attribute_value > 0:
			body.change_attribute(attributes, attribute_value)
			print("%s picked up a %s with a value of %d points" % [body.name, item_name, attribute_value])
		queue_free()
```

#### player.gd

```gdscript

extends CharacterBody2D

var inventory: Array[String]
const SPEED = 300.0

@export var attributes: Dictionary = {
	"max_health" : 100,
	"health" : 90,
	"strength" : 5,
	"intelligente" : 5,
	"dexterity" : 5,
}


func pickup(item: String):
	inventory.append(item)


func change_attribute(attribute: String, attribute_value: int):
		attributes[attribute] += attribute_value
		if attributes["health"] > attributes["max_health"]:
			attributes["health"] = attributes["max_health"]


func _physics_process(_delta):
	var input_direction = Input.get_vector("left", "right", "up", "down")
	velocity = input_direction * SPEED
	move_and_slide()
```

### 2. @export

#### item.gd

```gdscript

extends Area2D

enum attributes { MAX_HEALTH, HEALTH, STRENGTH, INTELLIGENCE, DEXTERITY }
@export var attribute: attributes
@export var attribute_value: float
@export var item_name: String


func _ready() -> void:
	item_label.text = item_name
	body_entered.connect(_on_body_entered)


func _on_body_entered(body):
	if body.has_method("pickup"):
		var message: String
		body.pickup(item_name)
		if attribute_value > 0:
			body.change_attribute(attribute, attribute_value)
			message = "Your %s has been increased by %s points" % [attributes.keys()[attribute], attribute_value]
		else:
			message = ""
		Signals.log.emit("%s picked up a %s. %s" % [body.name, item_name, message])
		queue_free()
```


#### player.gd

```gdscript

extends CharacterBody2D

var inventory: Array[String]
const SPEED = 300.0

@export var attributes: Dictionary = {
	"max_health" : 100,
	"health" : 90,
	"strength" : 5,
	"intelligence" : 5,
	"dexterity" : 5
}


func pickup(item: String):
	inventory.append(item)


func change_attribute(attribute: int, value: float):
	match (attribute) :
		0:
			attributes.max_health += value
		1:
			attributes.health += value
			if attributes.health > attributes.max_health:
				attributes.health = attributes.max_health
		2:
			attributes.strength += value
		3:
			attributes.intelligence += value
		4:
			attributes.dexterity += value


func _physics_process(_delta):
	var input_direction = Input.get_vector("left", "right", "up", "down")
	velocity = input_direction * SPEED
	move_and_slide()
```

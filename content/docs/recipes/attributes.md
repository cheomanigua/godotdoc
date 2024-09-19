---
weight: 5185
title: "Attributes - Player"
description: "How to manage the player's attributes when picking up powered-up items"
icon: "article"
date: "2024-09-18T16:49:18+02:00"
lastmod: "2024-09-18T16:49:18+02:00"
draft: false
toc: true
---


## 1. Preferred. Dictionary with @export_enum

#### player.gd

```gdscript

extends CharacterBody2D


var inventory: Array[String]
const SPEED = 300.0


var attributes: Dictionary = {
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


## 2. Array within a Dictionary

#### player.gd

```gdscript

extends CharacterBody2D

var inventory: Array[String]
const SPEED = 300.0
var attributes: Dictionary = {
	0 : ["max_health", 100],
	1 : ["health", 90],
	2 : ["str", 5],
	3 : ["int", 5],
	4 : ["dex", 5],
}


func pickup(item: String) -> void:
	inventory.append(item)


func increase_attribute(attribute: int, attribute_value: int):
	attributes[attribute][1] += attribute_value

	# Checking if health is bigger than max_health
	if attributes[1][1] > attributes[0][1]:
		attributes[1][1] = attributes[0][1]

	for key in attributes:
		print("%s: %d" % [attributes[key][0], attributes[key][1]])
```


#### item.gd

```gdscript

extends Area2D

enum attributes { HEALTH = 1, STR, INT, DEX }
@export var item_name: String
@export var attribute : attributes = attributes.HEALTH
@export var attribute_value: int = 0


func _ready() -> void:
	body_entered.connect(_on_body_entered)


func _on_body_entered(body):
	if body.has_method("pickup"):
		body.pickup(item_name)
		if value > 0:
			body.increase_attribute(attribute, value)
			message = "Your %s has been increased by %s points" % [attributes.keys()[attribute], value]
		else:
			message = ""
		queue_free()
```

## 3. Simple Dictionary

### 3.1 Health inside Dictionary

#### player.gd

```gdscript

extends CharacterBody2D

var attributes: Dictionary = {
	"max_health" : 100,
	"health" : 90,
	"strength" : 5,
	"intelligence" : 5,
	"dexterity": 5 }


func pickup(item: String) -> void:
	inventory.append(item)


func increase_attribute(attribute: int, attribute_value: int):
		match(attribute):
			0:
				attributes.health += attribute_value
				if attributes.health > attributes.max_health:
					attributes.health = attributes.max_health
			1:
				attributes.strength += attribute_value
			2:
				attributes.intelligence += attribute_value
			3:
				attributes.dexterity += attribute_value
```

#### item.gd

Same script as previous **item.gd** script above


### 3.2 Health outsite Dictionary

#### player.gd

```gdscript

extends CharacterBody2D

@export var health: float = 90:
	set(value):
		health = clamp(value, 0, max_health)
		#health_changed.emit(health)
@export var max_health: float = 100
var attributes: Dictionary = {
	"strength" : 5,
	"intelligence" : 5,
	"dexterity": 5 }


func pickup(item: String) -> void:
	inventory.append(item)


func increase_attribute(attribute: int, attribute_value: int):
		match(attribute):
			0:
				health += attribute_value
			1:
				attributes.strength += attribute_value
			2:
				attributes.intelligence += attribute_value
			3:
				attributes.dexterity += attribute_value
```

#### item.gd

```gdscript

extends Area2D

enum attributes { HEALTH, STRENGTH, INTELLIGENCE, DEXTERITY }
@export var attribute: attributes = attributes.HEALTH
@export var attribute_value: int = 0
@export var item_name: String


func _ready() -> void:
	item_label.text = item_name
	body_entered.connect(_on_body_entered)


func _on_body_entered(body):
	if body.has_method("pickup"):
		body.pickup(item_name)
		if value > 0:
			body.increase_attribute(attribute, value)
			message = "Your %s has been increased by %s points" % [attributes.keys()[attribute], value]
		queue_free()
```

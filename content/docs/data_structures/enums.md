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

### @export vs @export_enum

#### @export

The enum values are integers:

```gdscript

enum attributes { HEALTH, STRENGTH, INTELLIGENCE, DEXTERITY }
@export var attribute: attributes = attributes.HEALTH
```

#### @export_enum

The enum values can be integers or strings:


```gdscript

export_enum ("health", "strengh", "intelligence", "dexterity") var attributes: int = 0
export_enum ("health", "strengh", "intelligence", "dexterity") var attributes: String = "Health"
```
[Ref](https://docs.godotengine.org/en/stable/tutorials/scripting/gdscript/gdscript_exports.html#exporting-enums)


### Example

In this example, when the player pickups an item, it will increase the player's attribute.

Exporting the **enum** will let the player choose from a drop down menu the attribute in the Godot editor.

#### item.gd

```gdscript

extends Area2D

enum attributes { HEALTH, STRENGTH, INTELLIGENCE, DEXTERITY }
@export var attribute: attributes
@export var value: float
@export var item_name: String


func _ready() -> void:
	item_label.text = item_name
	body_entered.connect(_on_body_entered)


func _on_body_entered(body):
	if body.has_method("pickup"):
		var message: String
		body.pickup(item_name)
		if value > 0:
			body.increase_attribute(attribute, value)
			message = "Your %s has been increased by %s points" % [attributes.keys()[attribute], value]
		else:
			message = ""
		Signals.log.emit("%s picked up a %s. %s" % [body.name, item_name, message])
		queue_free()
```


#### player.gd

```gdscript

...

@export var attributes: Dictionary = {"health" : 0, "strength" : 0, "intelligence" : 0, "dexterity" : 0 }

...

func increase_attribute(attribute: int, value: float):
	match (attribute) :
		0:
			attributes.health += value
		1:
			attributes.strength += value
		2:
			attributes.intelligence += value
		3:
			attributes.dexterity += value
```



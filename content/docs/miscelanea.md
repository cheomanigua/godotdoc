---
weight: 950
title: "Miscelanea"
description: ""
icon: "article"
date: "2024-08-28T13:24:17+02:00"
lastmod: "2024-08-28T13:24:17+02:00"
draft: false
toc: true
---


## Changing Node instances textures

### In the editor

Add this script to the parent node where the Sprite node is a child:

```gdscript
tool

extends Area2D

@export(Texture) onready var texture setget texture_set, texture_get

func texture_set(newtexture):
	$Sprite.texture = newtexture

func texture_get():
	return $Sprite.texture
```

You will now be able to select a different texture for each node instance in the editor. This is great for level creation, as you will be able to differentiate between several types of potions, keys, etc.

### In a script

##### At run time

```gdscript

func _ready():
	var ctexture = load("%s" % creature_stats.Texture)
	%Sprite.texture = ctexture
```

##### At compile time

```gdscript

func _ready():
	var ctexture = preload("res://Images/Characters/orc.png")
	%Sprite.texture = ctexture
```

## Canvas Layer

If you want the GUI to be independent from the Viewport camera (the GUI will remain in screen regardless of camera movement), create/change the parent node of the GUI scene to **CanvasLayer**.

## Pause Game

```gdscript
var is_paused: bool = false

func _ready():
	pause_mode = Node.PAUSE_MODE_PROCESS # On player node, for instance

func _unhandled_input(event):
    if (event.is_action_pressed("pause")):
		 is_paused = !is_paused
		 pause()

func pause():
	if is_paused:
		get_tree().paused = true
		set_physics_process(false)
	else:
		get_tree().paused = false
		set_physics_process(true)
```

## Creating "variables" dynamically

```gdscript
	var vars: Dictionary
	var bonus_index := 9 # this is the value of the JSON file, starting in strength_bonus

	for key in Player.stats:
		# Dynamically creating variables of Player stats keys and assigning JSON file value
		vars[key] = Data.item_data[item_name].values()[bonus_index]
		if vars[key] != null:
			$Label.text += "\n%s %s%d" % [key.capitalize(), vars[key]]
		bonus_index += 1
```


## Components - A Design Pattern

A good game arquitecture is to structure the game in small components

##### 1. What are components

- Small blocks of useful functionality
- Work independently of another component
- Can be re-configured for easy prototyping
- Ideally know as little as possible outside of the component

##### 2. Accessing components

Can be done with noticeable coupling using:

- get_node("SomeNode") or $SomeNode
- %SomeNode
- @export var my_node: Node
- creating a class
- @export NodePath

##### 3. Component communication

Can be done with minimal coupling using:

- signals
- groups
- autoloads
- using the physics engine (colliders)
- propagate_call

---
weight: 200
title: "Basics"
description: ""
icon: "article"
date: "2024-08-28T13:24:17+02:00"
lastmod: "2024-08-28T13:24:17+02:00"
draft: false
toc: true
---


## Input
The most common input types are:

- [InputEvent](https://docs.godotengine.org/en/stable/classes/class_inputevent.html) Use them inside `func _input(event)`. The best use of InputEvent is when a single event is needed, like showing the inventory, pausing the game, jumping, etc. 

  - [is_action_pressed](https://docs.godotengine.org/en/stable/classes/class_inputevent.html#class-inputevent-method-is-action-pressed) *
  - [is_action_released](https://docs.godotengine.org/en/stable/classes/class_inputevent.html#class-inputevent-method-is-action-released) *
  - [is_pressed](https://docs.godotengine.org/en/stable/classes/class_inputevent.html#class-inputevent-method-is-pressed)

```gdscript

func _input(event: InputEvent) -> void:
	if event.is_action_pressed("ui_select"):
		show_inventory()
```

- [Input](https://docs.godotengine.org/en/stable/classes/class_input.html) Use them inside `func _physics_process(delta)`. The best use of Input is when a continous event is needed, like moving a character with keyboard keys.
  - [get_vector](https://docs.godotengine.org/en/stable/classes/class_input.html#class-input-method-get-vector)
  - [get_axis](https://docs.godotengine.org/en/stable/classes/class_input.html#class-input-method-get-axis)
  - [is_action_just_pressed](https://docs.godotengine.org/es/stable/classes/class_input.html#class-input-method-is-action-just-pressed) *
  - [is_action_just_released](https://docs.godotengine.org/es/stable/classes/class_input.html#class-input-method-is-action-just-released) *
  - [is_action_pressed](https://docs.godotengine.org/es/stable/classes/class_input.html#class-input-method-is-action-pressed) *
  - [is_key_pressed](https://docs.godotengine.org/es/stable/classes/class_input.html#class-input-method-is-key-pressed)
  - [is_mouse_button_pressed](https://docs.godotengine.org/es/stable/classes/class_input.html#class-input-method-is-mouse-button-pressed)
  - [get_mouse_button_mask](https://docs.godotengine.org/es/stable/classes/class_input.html#class-input-method-get-mouse-button-mask)

```gdscript

func _physics_process(_delta):
	var input_direction = Input.get_vector("ui_left", "ui_right", "ui_up", "ui_down")
	velocity = input_direction * speed
	move_and_slide()
```

\* **is_action** is specified by a name (such as **"ui_right"**) defined in the **Project->Project Settings->Input Map** panel of the Editor. As well as the default actions, we may redefine them and add more of our own.

### event/processes in order of execution

- [_input](https://docs.godotengine.org/en/stable/classes/class_node.html#class-node-private-method-input)
	- This is the first input method that gets called.
	- For gameplay input, **_unhandled_input** and **_unhandled_key_input** are usually a better fit as they allow the GUI to intercept the events first.
- [_gui_input](https://docs.godotengine.org/es/stable/classes/class_control.html#class-control-private-method-gui-input)
	- Second input method to be called.
	- It can only be used by **Control** nodes.
- [_shortcut_input](https://docs.godotengine.org/en/stable/classes/class_node.html#class-node-private-method-shortcut-input)
	- This method can be used to handle shortcuts.
	- For generic GUI events, use **_input** instead.
	- Gameplay events should usually be handled with either **_unhandled_input** or **_unhandled_key_input**.
- [_unhandled_key_input](https://docs.godotengine.org/en/stable/classes/class_node.html#class-node-private-method-unhandled-key-input)
	- This method can be used to handle Unicode character input with `Alt`, `Alt + Ctrl`, and `Alt + Shift` modifiers, after shortcuts were handled.
	- For gameplay input, this and **_unhandled_input** are usually a better fit than _input, as GUI events should be handled first.
	- This method also performs better than **_unhandled_input**, since unrelated events such as InputEventMouseMotion are automatically filtered, hence, mouse movements do not activate this function
	- For shortcuts, consider using **_shortcut_input** instead.
- [_unhandled_input](https://docs.godotengine.org/en/stable/classes/class_node.html#class-node-private-method-unhandled-input)
	- The same as **unhandle_key_input**, but this methods also handles mouse events.
	- To handle only keyboard events, consider using **_unhandled_key_input** for performance reasons.
- [_process](https://docs.godotengine.org/en/stable/classes/class_node.html#class-node-private-method-process)
	- Called during the processing step of the main loop. Processing happens at every frame and as fast as possible, so the `delta` time since the previous frame is not constant. `delta` is in seconds.
- [_physics_process](https://docs.godotengine.org/en/stable/classes/class_node.html#class-node-private-method-physics-process)
	- Called during the physics processing step of the main loop. Physics processing means that the frame rate is synced to the physics, i.e. the `delta` variable should be constant. `delta` is in seconds.

### func _input(event)

In the example below, a square will  switch colors between blue and red when the left mouse button in clicked over it.

```gdscript
extends Area2D

var clicked=false

func _draw():
	var r = Rect2(Vector2(), Vector2(40,40))
	if (clicked):
		draw_rect(r, Color(1,0,0))
	else:
		draw_rect(r, Color(0,0,1))
	set_process_input(true)

func _input(event):
	if (event.is_action_pressed("mouse_left_button")):
		clicked=!clicked
		queue_redraw()

# For the left click to work, add "mouse_left_button" to Project Settings -> Input Map
```

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
- contracts
- signal relays



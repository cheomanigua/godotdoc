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

## Clickeable Area2D

When left clicking in an Area2D, the message "Object has been clicked" will appear in the debug window. Create the following script to an Area2D node:

```gdscript
func _input_event(viewport, event, shape_idx):
	if (event is InputEventMouseButton and event.button_index == MOUSE_BUTTON_LEFT and event.pressed):
		print("Object has been clicked") #debugging
```
Note that if we don't add "event.pressed", the event will detect both the press and the release. If you only want to detect the first click, add "event.pressed".

## Input
The most common input types are:

- Input
  - [is_action_just_pressed](https://docs.godotengine.org/es/stable/classes/class_input.html#class-input-method-is-action-just-pressed) *
  - [is_action_just_released](https://docs.godotengine.org/es/stable/classes/class_input.html#class-input-method-is-action-just-released) *
  - [is_action_pressed](https://docs.godotengine.org/es/stable/classes/class_input.html#class-input-method-is-action-pressed) *
  - [is_key_pressed](https://docs.godotengine.org/es/stable/classes/class_input.html#class-input-method-is-key-pressed)
  - [is_mouse_button_pressed](https://docs.godotengine.org/es/stable/classes/class_input.html#class-input-method-is-mouse-button-pressed)
  - [get_mouse_button_mask](https://docs.godotengine.org/es/stable/classes/class_input.html#class-input-method-get-mouse-button-mask)
- [InputEvent](https://docs.godotengine.org/en/stable/classes/class_inputevent.html) (better used when a single event is needed, like showing the inventory, pausing the game, etc)

  - [is_action_pressed](https://docs.godotengine.org/en/stable/classes/class_inputevent.html#class-inputevent-method-is-action-pressed) *
  - [is_action_released](https://docs.godotengine.org/en/stable/classes/class_inputevent.html#class-inputevent-method-is-action-released) *
  - [is_pressed](https://docs.godotengine.org/en/stable/classes/class_inputevent.html#class-inputevent-method-is-pressed)

\* is_action is specified by a name (such as "ui_right") defined in the Project->Project Settings->Input Map panel of the Editor. As well as the default actions, we may redefine them and add more of our own.

Inputs and InputEvents run in the following event/processes:

- [_input](https://docs.godotengine.org/en/stable/classes/class_node.html#class-node-method-input): This is the first input method that gets called
- [_gui_input](https://docs.godotengine.org/es/stable/classes/class_control.html#class-control-method-gui-input) Second input method to be called. It can only be used by Control nodes
- [_unhandled_input](https://docs.godotengine.org/en/stable/classes/class_node.html#class-node-method-unhandled-input): Best input method to use for player controls. This way does not interfere with Control node input handlers.
- [_unhandled_key_input](https://docs.godotengine.org/en/stable/classes/class_node.html#class-node-method-unhandled-key-input): The same as above, but mouse movements do not activate this function
- [_process](https://docs.godotengine.org/en/stable/classes/class_node.html#class-node-method-process)
- [_physics_process](https://docs.godotengine.org/en/stable/classes/class_node.html#class-node-method-physics-process)

## _input(event)

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

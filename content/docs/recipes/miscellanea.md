---
weight: 5400
title: "Miscellanea"
description: ""
icon: "article"
date: "2024-08-30T13:28:51+02:00"
lastmod: "2024-08-30T13:28:51+02:00"
draft: false
toc: true
---

### Clickeable Area2D

When left clicking in an Area2D, the message "Object has been clicked" will appear in the debug window. Create the following script to an Area2D node:

```gdscript
func _input_event(viewport, event, shape_idx):
	if (event is InputEventMouseButton and event.button_index == MOUSE_BUTTON_LEFT and event.pressed):
		print("Object has been clicked") #debugging
```
Note that if we don't add "event.pressed", the event will detect both the press and the release. If you only want to detect the first click, add "event.pressed".

### Boolean switch
We can use a bool variable as a switch. Check the example above and see how bool variable **'clicked'** is used.


### Camera
Add a camera node as children to the player and set the Current property to **'On'** in the Inspector.

### Position a node in the center of the screen

```gdscript
position = get_viewport_rect().size / 2
```

### Change window size

```gdscript
func _ready(): 
    OS.set_window_size(Vector2(1024, 780))
```

### Full screen manual

```gdscript
# get device resolution
var max = OS.get_screen_size()

# adjust game resolution
    OS.set_window_size(max)
    OS.set_window_position(Vector2(0,0))
```

### Full screen automatic

```gdscript
OS.set_window_fullscreen(true)
OS.set_borderless_window(true) #unable to move
```

### Disable CollisionShape2D

```gdscript
$CollisionShape2D.set_deferred("disabled", true)
```

### Add a node to a Group

```
func _ready():
    add_to_group("players")
```

[Ref](http://docs.godotengine.org/en/latest/getting_started/step_by_step/scripting_continued.html#groups)

### Timer

```gdscript
$Label.text = message
yield(get_tree().create_timer(5.0), "timeout")
$Label.text = ""
```
The code above shows a message during 5 seconds

### Autoload (Singleton)

- When autoloading the Player, ALWAYS use the Player scene, and not the Player script. Otherwise, you will get `get_node: "node not found"` error when trying to call the children nodes from a script.

- It is not possible to export ENUMs from a singleton. For instance, if **Global** is a singleton and has an ENUM called **SlotType**, this export won't work:

```gdscript
export(Global.SlotType) var slotType = Global.SlotType;
```

The workaround is to declare singleton in a separate script, add ENUMS there and declare as class. For instance, we create **Global.gd**:

```gdscript
extends Node
class_name Global

enum SlotType {
	SLOT_DEFAULT = 0,
	SLOT_HEAD,
	SLOT_FEET
}
```

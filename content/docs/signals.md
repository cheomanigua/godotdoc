---
weight: 500
title: "Signals"
description: ""
icon: "article"
date: "2024-08-28T16:18:53+02:00"
lastmod: "2024-08-28T16:18:53+02:00"
draft: false
toc: true
---

Signals are Godot's implementation of the observer pattern. They allow a node to send out a message that other nodes can listen for and respond to. For example, rather than continuously checking a button to see if it's being pressed, the button can emit a signal when it's pressed.

There are to types of signals in Godot:


1. **Built-in**. These signals comes with Godot and you don't need to create them. They emit the signal by default and you can connect to them in two ways:

- From the editor (*Scene/Node -> Node/Signal -> <signal> -> Connect*)

- From code: `<source_node>.<signal_name>.connect(target_function_name>)`

Example of connecting to the `body_entered` signal from a **CharacterBody2D** scene node called **Zone**:

`$Zone.body_entered.connect(_on_zone_body_entered)`

**Note 1**: If the node that connect to the signal is in the same scene, we can reference it by $ or %.

**Note 2**: If the node that connect to the signal is in a different scene 


2. **Custom**. You can create your own signals. They need the following:

- A name: `signal my_signal`
- An emitter: `my_signal.emit()`

Example:

```gdscript
signal my_signal

func _ready():
	my_signal.emit()
```

You can connect to your custom signal as you will do with a built in signal:
```gdscript
node_name.my_signal.connect(_on_my_signal_function)
```

### Passing arguments
```gdscript
signal my_signal
...
my_signal.emit(arg1)
```
```gdscript
node_name.my_signal.connect(_on_my_signal)
...
func _on_my_signal(arg1):
```


### Connecting from a different scene

Preferred:

```gdscript
@onready var projectile = preload("res://projectile.tscn")

func _ready():
	var new_projectile = projectile.instantiate()
	new_projectile.my_signal.connect(_on_my_signal_function)
```
Less preferred:

```gdscript
get_parent().get_node("Zone").body_entered.connect(_on_Zone_body_entered)
```
or
```gdscript
get_tree().get_root().get_find("Zone", true, false).body_entered.connect(_on_Zone_body_entered)
```
You can use either `get_node()` or `find_node()`. Note that `get_node()` doesn't need the three arguments that get_find needs.

### Example of a connected function:

```gdscript
func _on_Zone_body_entered():
if body.name == "Player":
	print("%s detected" % body.name)
```
You can also use either of these:

```gdscript
if body.is_in_group("players"):
if body.has_method("player_spotted"):
if body.variable == "holy"
```

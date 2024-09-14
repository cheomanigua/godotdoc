---
weight: 300
title: "Signals"
description: "A decoupled way to communicate between nodes"
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

**Note 2**: If the node that connect to the signal is in a different scene, check some sections below.


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
if body is MyCustomClass
```

### Singleton (Autoload)

You can create a signal manager that creates multiple signals that are globally accesible to any node.

#### 1. Create the Singleton

- Create a script and give it a name. In this example the name is `signal_manager.gd`.
- Create a single signal for the example. Remember that you can create as many signals as you wish:
```gdscript
signal event_message
```
- Add the script as **Autoload**:
	- **Project** -> **Project Settings** -> **Globals**
	- Click on the folder icon
	- Select the script `signal_manager.gd` and click **Open**
	- Click the **Add** button
	- Be sure that **Global variable** is enabled
	- A node called `SignalManager` is created

#### 2. Emit the signal from a different node

Now, any script can emit the signal `event_message`. For instance, an **Area2D** node:

```gdscript

func _on_body_entered():
	if body.name == "Player":
		SignalManager.event_message.emit(body.name + " has collided with " + name)
```

#### 3. Connect to the signal from a different node

Now, any script can connect to the emitted signal. For instance, a **Label** node that shows event messages throughout the game:
```gdscript
func _ready():
	SignalManager.event_message.connect(_on_message_received)

func _on_message_received(message):
	text = message
```

This way, you can show lots of event messages from different nodes. For instance, an **Area2D** node instantiating a bullet. Upon inflicting damage, the **Label** node above will connect and show the message:

#### 4. Emit the signal from a Bullet node

```gdscript

func _on_body_entered():
	if body.has_method("take_damage"):
		body.take_damage(damage)
		SignalManager.event_message.emit(body.name + " has received " + str(damage) + " points of damage from " + name)
```

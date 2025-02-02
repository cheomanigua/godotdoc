---
weight: 600
title: "Composition"
description: "A good game arquitecture is to structure the game in small components"
icon: "article"
date: "2024-08-28T13:51:43+02:00"
lastmod: "22024-08-28T13:51:43+02:00"
draft: false
toc: true
---

![alt](/images/components.webp)


1. What are components
    - Small blocks of useful functionality
    - Work independently of another component
    - Can be re-configured for easy prototyping
    - Ideally known as little as possible outside of the component

2. Accessing components. Can be done with noticeable coupling using:
    - get_node("SomeNode") or $SomeNode
    - %SomeNode
    - @export var my_node: Node
    - creating a class
    - using the physics engine (Areas, etc.)
    - @export NodePath
3. Component communication. Can be done with minimal coupling using:
    - signals
    - groups
    - autoloads
    - contracts
    - signal relays
    - propagate_call

## Accessing a node from the same scene

### Node Paths

- You can access a node in the same scene by calling the node using `$Node` or `get_node("Node")`. Knowing this, then:
| | |
|-|-|
| `$NodeA/NodeB`            | access children |
| `$".."` or `get_parent()` | access parent |
| `$".."/NodeA`             | access sibling |
| `$"."` or `self`          | access current node |
|`%Node`                    | Unique node, access node everywhere in current scene|

<br>

There is another way to access a node in the same scene:

```gdscript
@export var my_node: Node
```

The above code works by dragging the node to the property panel in the Inspector. It is like using the unique node `%Node`, but with the added benefit that if we **move** or **rename** the node later, the reference won't be affected and still works. However, changing the name of the variable `my_node` will break the path in all instances. You will need to rename all the variables and re-drag the node to the property panel in the Inspector for the new variable name to work.


#### Call down, Signal up

As a general rule, nodes should manage their children, not the other way around. If you’re using `get_parent()` or `get_node("..")`, then you’re probably headed for trouble.

- If a node is calling a child (i.e. going “down” the tree), then `get_node()` is appropriate.
- If a node needs to communicate “up” the tree, it should probably use a signal.

References:
- [https://kidscancode.org/godot_recipes/4.x/basics/node_communication/](https://kidscancode.org/godot_recipes/4.x/basics/node_communication/)
- [https://kidscancode.org/godot_recipes/4.x/img/node_access_theduriel.png](https://kidscancode.org/godot_recipes/4.x/img/node_access_theduriel.png)


## Accessing a node from a different scene

All of what we saw earlier works for nodes in the same scene. However, for different scenes, not all of what we saw earlier works.

When trying to access data from a node in a different scene avoiding the need to instantiate the node (because we only want the original instance, like the player), we can use the following:

- Signals
- Groups
- Autoload (like the player scene)
- Using the physics engine (Areas, etc.)

### Signals

If we want any node to access Player and its properties, we can create the signal `body_entered` in the node that wants access to Player.

```gdscript
var player: RigidBody2D

func _ready():
	body_entered.connect(_on_body_entered) # body could be the Player

_on_body_entered(body):
	if body == "Player":
		player = body

func calculate_angle_to_player():
	var angle: float = (player.position - position).angle()
```

### Groups

If we want any node to access Player and its properties, we can create a *Project Settings -> Globals -> Group* called **Player** and add the node `Player` to it. Then, we can run this code in a completely different node to access Player:

```gdscript
var player: RigidBody2D

func _ready():
	for node in get_tree().get_nodes_in_group("Player"):
		if node.name == "Player":
			player = node

func calculate_angle_to_player():
	var angle: float = (player.position - position).angle()
```

### Autoload

If we want any node to access Player and its properties, we can create a *Project Settings -> Globals -> Autoload* with the Player scene and call it **PlayerGlobal**. Then, we can run this code in a completely different node to access Player:

```gdscript

func calculate_angle_to_player():
	var angle: float = (PlayerGlobal.position - position).angle()
```

{{< alert context="warning" text="When using autoload, don't instantiate the Player using the GUI editor. Use the Player attached script to setup position, rotation, etc in code. Otherwise, you'll end up with two instances of Player. For this reason, Autoload is better suited for scripts rather than scenes." />}}

## Get name of scene

If you want to get the name of a scene called `Key.tscn`, run the following code in the scene script:

```gdscript

func _ready() -> void:
    var path = scene_file_path
    print (path.right(-path.rfind("/") - 1).left(-5))
```

It will print:
```
key
```

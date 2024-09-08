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

2. Accessing components
    - get_node("SomeNode") or $SomeNode
    - %SomeNode
    - creating a class
    - using the physics engine (Areas, etc.)
    - groups
    - autoloads
    - @export NodePath

3. Component communication
Can be done with minimal coupling using:
    - signals
    - contracts
    - signal relays
    - propagate_call


### Node Paths
- You can access nodes by using `$Node` or `get_node("Node")`. Knowing this, then:
| | |
|-|-|
| `$NodeA/NodeB` | access children |
| `$".."` or `get_parent()` | access parent |
| `$".."/NodeA` | access sibling |
| `$"."` or `self` | access current node |

- Scene's Unique Nodes can be found with `%Node` from anywhere in the scene:
| | |
|-|-|
| `%Node` | access node everywhere |


### Call down, Signal up

As a general rule, nodes should manage their children, not the other way around. If you’re using `get_parent()` or `get_node("..")`, then you’re probably headed for trouble.

- If a node is calling a child (i.e. going “down” the tree), then get_node() is appropriate.
- If a node needs to communicate “up” the tree, it should probably use a signal.

References:
- [https://kidscancode.org/godot_recipes/4.x/basics/node_communication/](https://kidscancode.org/godot_recipes/4.x/basics/node_communication/)
- [https://kidscancode.org/godot_recipes/4.x/img/node_access_theduriel.png](https://kidscancode.org/godot_recipes/4.x/img/node_access_theduriel.png)


### Get name of scene

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

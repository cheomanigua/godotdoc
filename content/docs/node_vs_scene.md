---
weight: 250
title: "Node vs Scene"
description: "How to create and call nodes and scenes"
icon: "article"
date: "2025-02-06T16:24:17+02:00"
lastmod: "2025-02-06T16:24:17+02:00"
draft: false
toc: true
---

# Node vs Scene

A node is a built in or custom component with certain functionality. A group of nodes form a tree. When you organize nodes in a tree, it is called a scene, and nodes can communicate between them.

When you organize nodes in a tree, we call this construct a scene. Once saved, scenes work like new node types in the editor, where you can add them as a child of an existing node. In that case, the instance of the scene appears as a single node with its internals hidden.

On top of acting like nodes, scenes have the following characteristics:

1. They always have one root node, like "Player" or "Enemy".
2. You can save them to your local drive and load them later.
3. You can create as many instances of a scene as you'd like. You could have five or ten characters in your game, created from your Character scene.

[Godot documentation](https://docs.godotengine.org/en/stable/getting_started/step_by_step/nodes_and_scenes.html)


# Nodes

## Referencing a node

You can get a reference to a node by calling the `get_node("NodeName")` method, or the short notation `$NodeName`. For this to work, the child node must be present in the scene tree. Getting it in the parent node's _ready() function guarantees that.


[Godot Documentation](https://docs.godotengine.org/en/stable/tutorials/scripting/nodes_and_scene_instances.html)

Example:

```gdscript
@onready var airplane: CharacterBody2D = %Airplane

func some_function():
    airplane.update_destination()
    print(airplane.position)
```

The `@onready` annotation makes the member variable to initalize right before the `_ready()` callback. If you omit it, it will be initialized in the `_ready()` callback.

###  Node Paths

As mentioned earlier, you can access a child node using `$Node` or `get_node("Node")`. Nodes in the scene tree can access other nodes in the scene tree:

| | |
|-|-|
|`$NodeA/NodeB`             | access children|
|`$".."` or `get_parent()`  | access parent|
|`$".."/NodeA`              | access sibling|
|`$"."` or `self`           | access current node|
|`%Node`                    | Unique node, access node everywhere in current scene|

<br>

There is a better way to access a node in the same scene:

```gdscript
@export var my_node: Node
```

The above code works by dragging the node to the property panel in the Inspector. It is like using the unique node `%Node`, but with the added benefit that if we rename the node later, the reference won't be affected and still works.

## Creating a node

To create a node from code, call its `new()` method like for any other class-based datatype. You can store the newly created node's reference in a variable and call `add_child()` to add it as a child of the node to which you attached the script.

```gdscript
func _ready():
	var timer = Timer.new() # Create a new Timer.
	add_child(timer) # Add it as a child of this node.
```

# Scenes

## Instantiating a scene

Scenes are templates from which you can create as many reproductions as you'd like. This operation is called instancing.

```gdscript
const MyScene = preload("myscene.tscn") # if a constant, a scene can only be preloaded, but not loaded
var MyScene = load("myscene.tscn")
var MyScene = preload("myscene.tscn")
@onready var MyScene = preload("myscene.tscn")                  # multiple instances can be created
@onready var MyScene = preload("myscene.tscn").instantiate()    # only one instance can be created
```
At that point, `scene` is a packed scene resource, not a node. To create the actual node, you need to call `PackedScene.instantiate()`. It returns a tree of nodes that you can use as a child of your current node.

Example 1:

{{< alert context="success" text="When `.instantiate()` is in a fuction, you can create as many instances as you want." />}}

```gdscript
const BULLET = preload("res://Bullet.tscn") # multiple instances

some_function():
    var new_bullet = BULLET.instantiate()
    get_parent().add_child(new_bullet)                  # option 1
    # get_tree().current_scene.add_child(new_bullet)    # option 2
    # get_tree().root.add_child(new_bullet)             # option 3
    # add_child(new_bullet)                             # option 4, but not for RigidBody2D instantiating projectiles

```
Example 2:

{{< alert context="danger" text="When `.instantiate()` is on `preload`, you can create only one instance. Do not use to instantiate scenes that need more than one instance, like projectiles. If you do, Godot will crash when instantiating a second time." />}}

```gdscript
var final_boss = preload("res://FinalBoss.tscn").instantiate() # only one instance, constants not allowed.

some_function():
    get_parent().add_child(final_boss)
```


## .new() vs .instantiate()

When instantiating **Objects**, use `new()`. When instantiating **Scenes**, use `instantiate()`.

## .new()

**.new()** is used to instantiate **Objects**, that is, custom classes (Player, Enemy) and built-in nodes (Timer, Sprite2D, RayCast2D).

Example 1:

```gdscript
var node = Node2D.new()
node.position = 200, 300
node.rotation = 1.5
add_child(node)
var a = node.get("rotation")    # a is 1.5
var b = node.rotation           # b is 1.5
```

Example 2:

```gdscript
var city = City.new()
city.name = "Tarraco"
city.population = 3000
add_child(city)
var a = city.get("population")  # a is 3000
var b = city.population         # b is 3000
```

Note in the code above that you can optionally set up inital values for the instance before calling `add_child()`.

`add_child` will add the instance node to the scene tree. If you don't call `add_child`, Godot will generate a stray node (orphan node). You can check for orphan nodes by using `print_orphan_nodes()` and the **Project->Tools->Orphan Resource Explorer...**

### constructor

If you, on the other hand, have defined a class constructor in your `City.gd` class, you can instantiate the class using a constructor like this:

- `City.gd`

```gdscript
class_name City
extend Node

var name: String = ""
var population: int = 0

# Constructor
func _init(_name: String, _population: int) -> void:
	name = _name
	population = _population
```

- `main.gd`

```gdscript

func _ready() -> void:
	# Instantiaton
	var city: City = City.new("Tarraco", 3000)
```


More info in [Godot Documentation](https://docs.godotengine.org/en/stable/tutorials/scripting/gdscript/gdscript_basics.html#classes)

<br>

## .instantiate()

**.instantiate()** is used to instantiate **Scenes**. It is convenient when we want to instantiate things that are recurrent in the game, like enemies, items, coins, etc. Also, **instantiate()** is a must for projectile type of objects, like bullets, arrows, etc.

However, with `.instantiate()` is not possible to add paramenters via constructor while instantiating a scene, as opposed to `.new()`. There is a solution, though. We can use a static method to work around the lack of a constructor. Keep reading:

### static methods (like constructors)

The example below implements a turret that can select three different types of bullets to shoot. Each type of bullet produces a particular damage value. It's not the job of the turret to inflict the damage, that's the job of the bullet. Likewise, it is not the job of the bullet to select the type of munition the turret can shoot. The solution is for the tower to select the type of bullet to shoot and pass that information to the bullet class constructor. The bullet class deals with the damage calculations:

- `Bullet.gd`

```gdscript
class_name Bullet
extends Area2D

const BULLET: PackedScene = preload("res://Projectile/Bullet/bullet.tscn")

enum munition_type { LOW_DAMAGE = 1, MEDIUM_DAMAGE, HIGH_DAMAGE }
var munition_index: int = 0

static func create_bullet(_munition_index: int) -> Bullet:
	var new_bullet: Bullet = BULLET.instantiate()
	new_bullet.munition_index = _munition_index
	return new_bullet

func _ready() -> void:
	damage = munition_type.values()[munition_index]
```

- `turret.gd`

```gdscript
extends StaticBody2D

enum munition { LOW_DAMAGE, MEDIUM_DAMAGE, HIGH_DAMAGE }
@export var munition_type: munition = munition.LOW_DAMAGE

func _shoot():
	var new_bullet: Bullet = Bullet.create_bullet(munition_type)
	get_parent().add_child(new_bullet)
	new_bullet.global_position = muzzle.global_position
```


{{< alert context="success" text="The great advantage of using a **static method** is that the own original class loads its own **PackedScene**. This means that if five different scenes instantiate the original scene, they won't need to load the original **PackedScene**. This means that any changes in the scene path has to be updated only in the own original class." />}}

<br>

#### Instantiate a scene with parameters using custom inititalization method

The following code will instantiate 1 Gem in the player position after the players drop the gem:

- `item.gd`

```gdscript
export (String) var item_name
export (int) var item_quantity = 1

func initialize(name: String, quantity: int):
	item_name = name
	item_quantity = quantity
```

- `player.gd`

```gdscript
const ITEM = preload("res://scenes/item_object.tscn")

var inventory: Array = [ "Gem", "Coin", "Scroll"]

func drop(name: String):
	var quantity = 1
	var item = ITEM.instantiate()
	item.initialize(name, quantity)
	add_child(item)
	item.position = position
	inventory.erase(name)

func ready():
	drop(inventory[0])
```

Note that you cannot instantiate an object from its own script (You cannot instantiate **item** from **item.gd**)

<br>

#### Instantiate a scene with parameters using variables directly

We can also set up the instance properties before adding the instance to the scene via `add_child`:

```gdscript
const ITEM = preload("res://scenes/item_object.tscn")

var inventory: Array = [ "Gem", "Coin", "Scroll"]

func drop():
	var item = ITEM.instantiate()
	item.name = inventory[0]
	item.quantity = 1
	add_child(item)
	item.position = position
	inventory.erase(name)

func ready():
	drop()
```

# load() vs preload()
When importing a resource, you can use either load or preload.

- **load()** is run at runtime
- **preload()** is run at compile time

```gdscript
@onready var Data = load("res://Scripts/data.gd").new()
@onready var Data = preload("res://Scripts/data.gd").new()
```

If you prefer to use a constant:

```gdscript
@onready const Data = preload("res://Scripts/data.gd")
var data = Data.new()
```

# free() vs queue_free() vs remove_child()
When importing a resource, you can use either load or preload.

- **[free()](https://docs.godotengine.org/en/stable/classes/class_object.html#class-object-method-free)** deletes an object from memory immediately. Be cautious.
- **[queue_free()](https://docs.godotengine.org/en/stable/classes/class_node.html#class-node-method-queue-free)** deletes a node from memory when it's safe to do so.
- **[remove_child()](https://docs.godotengine.org/en/latest/classes/class_node.html?#class-node-method-remove-child)** removes a node from the scene tree but it does not delete the node.

You can choose exactly when to free the queue by adding the following code in any method that you are gonna call:
```gdscript

    if is_queued_for_deletion():
        return
```

[WeakRef](https://docs.godotengine.org/en/stable/classes/class_weakref.html)

Holds an Object, but does not contribute to the reference count if the object is a reference.



# Components - A Design Pattern

A good game arquitecture is to structure the game in small components

#### 1. What are components

- Small blocks of useful functionality
- Work independently of another component
- Can be re-configured for easy prototyping
- Ideally know as little as possible outside of the component

#### 2. Accessing components

- $SomeNode or get_node("SomeNode")
- %SomeNode
- @export var my_node: Node
- creating a class
- using the physics engine (Areas, etc.)
- groups
- autoloads
- @export NodePath

#### 3. Component communication

Can be done with minimal coupling using:

- signals
- contracts
- signal relays
- propagate_call



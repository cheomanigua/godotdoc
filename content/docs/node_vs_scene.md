---
weight: 200
title: "Node vs Scene"
description: "How to add nodes and instantiate scenes"
icon: "article"
date: "2025-02-06T16:24:17+02:00"
lastmod: "2025-02-06T16:24:17+02:00"
draft: false
toc: true
---

# Node vs Scene

- [Nodes and scenes - Godot documentation](https://docs.godotengine.org/en/stable/getting_started/step_by_step/nodes_and_scenes.html)
- [Nodes and scenes instances - Godot documentation](https://docs.godotengine.org/en/stable/tutorials/scripting/nodes_and_scene_instances.html)

#### Node

A node is a built in component or custom component (saved scene) with certain properties and functionality. Nodes are added to a tree containing other nodes.

#### Scene

A scene is a collection of nodes organized in a tree. Nodes within the scene can comunicate between them.

Once saved, scenes work like new node types in the editor, where you can instantiate them as a child of an existing node. In that case, the instance of the scene appears as a single node with its internals hidden.

On top of acting like nodes, scenes have the following characteristics:

1. They always have one root node, like "Player" or "Enemy".
2. You can save them to your local drive and load them later.
3. You can create as many instances of a scene as you'd like. You could have five or ten characters in your game, created from your Character scene.

#### Node and Scene creation


- A scene is created by clicking on **Scene** -> **New Scene** and adding nodes to its tree.
- A custom node is created by saving a scene we created.
- A built in node (Label, Timer, RigidBody2D, etc) is already created.

{{< alert context="info" text="The official documentation may lead to confusion because it uses the word *create* instead of *add* in some parts. The rest of this article explains how to **add nodes** and **instantiate scenes** (implicity counting on the node or scene already being created), instead of how to **create nodes** and **create scenes**." />}}

# Nodes

## Adding a node

There are two ways to add a node to the tree: via code or via editor

### Adding a node via code

- To add a node via code, call its `new()` method like for any other class-based datatype and store the class in a variable. The node will be a child of the node where the script is attached.

    {{< tabs tabTotal="2">}}
    {{% tab tabName="GDScript" %}}

```gdscript
@onready var timer: Timer = Timer.new()

func some_function():
	add_child(timer)        # Add it as a child of the node where the script is attached.
```

{{% /tab %}}
{{% tab tabName="C#" %}}

```csharp
public partial class MyClass : Node
{
	private Timer timer = new Timer();

    private void SomeFunction()
    {
	    AddChild(timer);
    }
}
```

{{% /tab %}}
{{< /tabs >}}

### Adding a node via editor

1. To add a node via editor, click on the **Add Child Node... (Ctrl+A)** icon with a "**+**" symbol. The node will be a child of the highlighted node in the tree when you clicked **Add Child Node...**
2. Then, we can store the newly created node reference in a variable:
    1. Ordinary Variable

        {{< tabs tabTotal="2">}}
        {{% tab tabName="GDScript" %}}

```gdscript
@onready var timer: Timer = $Timer
```

{{% /tab %}}
{{% tab tabName="C#" %}}


```csharp
public partial class MyClass : Node
{
	private void SomeFunction()
    {
		var timer = GetNode<Timer>("Timer");
}
```

{{% /tab %}}
{{< /tabs >}}

    2. Exported variable:

        {{< tabs tabTotal="2">}}
        {{% tab tabName="GDScript" %}}

```gdscript
@export var my_node: Node
```
{{% /tab %}}
{{% tab tabName="C#" %}}

```csharp
[Export] private Node _myNode;
```
{{% /tab %}}
{{< /tabs >}}

        The above exported variable code works by dragging the node to the property panel in the Inspector. It is like using the unique node `%Node`, but with the added benefit that if we rename the node later, the reference won't be affected and still works.



<br>

#####  Node Paths

As seen in the snipped above, you can access a child node reference using `$Node` or `get_node("Node")`. Nodes in the scene tree can access other nodes in the scene tree:

| | |
|-|-|
|`%Node`                    | Unique node, access node everywhere in current scene|
|`$NodeA/NodeB`             | access children|
|`$".."` or `get_parent()`  | access parent|
|`$".."/NodeA`              | access sibling|
|`$"."` or `self`           | access current node|


## Implementation

Once the node has been added, we can work with it. Here, it doesn't matter how it was created, it's the same implementation:

{{< tabs tabTotal="2">}}
{{% tab tabName="GDScript" %}}

```gdscript
    timer.wait_time = 3.0
    timer.start()
    timer.timeout.connect(_on_timer_timeout)
```

{{% /tab %}}
{{% tab tabName="C#" %}}


```csharp
		timer.Start(3f);
		timer.Timeout += OnTimerTimeout;
```

{{% /tab %}}
{{< /tabs >}}

## Full code

{{< tabs tabTotal="4">}}
{{% tab tabName="GDScript via code" %}}

```gdscript
@onready var timer: Timer = Timer.new()

func some_function():
    timer.wait_time = 3.0
    timer.start()
    timer.timeout.connect(_on_timer_timeout)
    add_child(timer)
```

{{% /tab %}}
{{% tab tabName="C# via code" %}}

```csharp

public partial class MyClass : Node
{
	private Timer timer = new Timer();

	private void SomeFunction()
    {
		timer.Start(3f);
		timer.Timeout += OnTimerTimeout;
		AddChild(timer);
    }
}
```
{{% /tab %}}
{{% tab tabName="GDScript via editor" %}}

```gdscript

# Node has been already created in the editor

@onready var timer: Timer = $Timer

func some_function():
    timer.wait_time = 3.0
    timer.start()
    timer.timeout.connect(_on_timer_timeout)
```
{{% /tab %}}
{{% tab tabName="C# via editor" %}}

```csharp

public partial class MyClass : Node
{
	// Node has been already created in the editor

	private void SomeFunction()
    {
		timer = GetNode<Timer>("Timer");
		timer.Start(3f);
		timer.Timeout += OnTimerTimeout;
    }
}
```

{{% /tab %}}
{{< /tabs >}}

## Deleting a node

{{< tabs tabTotal="2">}}
{{% tab tabName="GDScript" %}}

```gdscript
    timer.queue_free()
```

{{% /tab %}}
{{% tab tabName="C#" %}}


```csharp
		timer.QueueFree();
```

{{% /tab %}}
{{< /tabs >}}

# Scenes

## Instantiating a scene

Scenes are templates from which you can create as many reproductions as you'd like. This operation is called instancing.

There are two ways to instantiate a scene: via code or via editor.


### Instantiating a scene via code

The first step is to load the scene from the local drive:

{{< tabs tabTotal="2">}}
{{% tab tabName="GDScript" %}}

```gdscript
const MyScene = preload("myscene.tscn") # if a constant, a scene can only be preloaded, but not loaded
var MyScene = load("myscene.tscn")
var MyScene = preload("myscene.tscn")
@onready var MyScene = preload("myscene.tscn")
```
{{% /tab %}}
{{% tab tabName="C#" %}}


```csharp
public PackedScene MyScene = (PackedScene)ResourceLoader.Load("res://myscene.tscn");    // Option 1, preferred
public PackedScene MyScene = GD.Load<PackedScene>("res://myscene.tscn");                // Option 2
```

{{% /tab %}}
{{< /tabs >}}

At this point, `MyScene` is a packed scene resource, not a node.

The second step is to create an instance, that is, to create the actual node. For that you need to call `PackedScene.instantiate()`. It returns a tree of nodes that you can use as a child of your current node.

Example 1:

{{< tabs tabTotal="2">}}
{{% tab tabName="GDScript" %}}

```gdscript
const BULLET = preload("res://Bullet.tscn")

func some_function():
    var new_bullet = BULLET.instantiate()
    get_parent().add_child(new_bullet)                  # option 1
    # get_tree().current_scene.add_child(new_bullet)    # option 2
    # get_tree().root.add_child(new_bullet)             # option 3
    # add_child(new_bullet)                             # option 4, but not for RigidBody2D instantiating projectiles

```

{{% /tab %}}
{{% tab tabName="C#" %}}

```csharp
using Godot;

public partial class World : Node
{
    public PackedScene Bullet = (PackedScene)ResourceLoader.Load("res://bullet.tscn");

    private void SomeFunction()
    {
        var new_bullet = (Area2D)Bullet.Instantiate();      # Option 1
        var new_bullet = Bullet.Instantiate() as Area2D;    # Option 2
        var new_bullet = Bullet.Instantiate<Area2D>();      # Option 3
        new_bullet.Position = new Vector2(100, 100);
        new_bullet.Rotation = 1.0f;
        AddChild(new_bullet);
    }
}
```
{{% /tab %}}
{{< /tabs >}}

Example 2:

```gdscript

func some_function():
    var new_bullet = preload("res://Bullet.tscn").instantiate()
    get_parent().add_child(new_bullet)
```

### Instantiating a scene via editor

To instantiate a scene via editor, click on the **Instantiate Child Scene... (Ctrl+Shift+A)** with a chain symbol. The scene will become a child node of the highlighted node in the tree when you clicked **Instantiate Child Scene…**

And that's pretty much it. Congratulations.

## Deleting a scene

{{< tabs tabTotal="2">}}
{{% tab tabName="GDScript" %}}

```gdscript
    new_bullet.queue_free()
```

{{% /tab %}}
{{% tab tabName="C#" %}}


```csharp
		new_bullet.QueueFree();
```

{{% /tab %}}
{{< /tabs >}}

# .new() vs .instantiate()

When instantiating **Objects**, use `new()`. When instantiating **Scenes**, use `instantiate()`.

## .new()

**.new()** is used to instantiate **Objects**, that is, custom classes (City, Economy) and built-in nodes (Timer, Sprite2D, RayCast2D).

Example 1:

{{< tabs tabTotal="2">}}
{{% tab tabName="GDScript" %}}

```gdscript
var node = Node2D.new()
node.position = 200, 300
node.rotation = 1.5
add_child(node)
print(node.get("rotation"))    # 1.5
print(node.rotation)           # 1.5
```

{{% /tab %}}
{{% tab tabName="C#" %}}

```csharp
var node = new Node2D();
node.Position = new Vector2(200, 300);
node.Rotation = 1.5f;
AddChild(node);
GD.Print(node.Get("Rotation"));
GD.Print(node.Rotation);
```

{{% /tab %}}
{{< /tabs >}}

Example 2:

{{< tabs tabTotal="2">}}
{{% tab tabName="GDScript" %}}

```gdscript
var city = City.new()
city.name = "Tarraco"
city.population = 3000
add_child(city)
print(city.get("population"))  # 3000
print(city.population)         # 3000
```

{{% /tab %}}
{{% tab tabName="C#" %}}

```csharp
var city = new City();
city.Set("city_name", "Tarraco");
city.Set("population", 3000);
AddChild(city);
GD.Print(city.Get("population"));
GD.Print(city.Population);
```

{{% /tab %}}
{{< /tabs >}}

Note in the code above that you can optionally set up inital values for the instance before calling `add_child()`.

`add_child` will add the instance node to the scene tree. If you don't call `add_child`, Godot will generate a stray node (orphan node). You can check for orphan nodes by using `print_orphan_nodes()` and the **Project->Tools->Orphan Resource Explorer...**

### constructor

If you, on the other hand, have defined a class constructor in your `City.gd` class, you can instantiate the class using a constructor like this:

{{< tabs tabTotal="2">}}
{{% tab tabName="GDScript" %}}


- `City.gd`

```gdscript

class_name City
extend Node

var city_name: String = ""
var population: int = 0

# Constructor
func _init(_name: String, _population: int) -> void:
	cityu_name = _name
	population = _population
```

- `main.gd`

```gdscript

func _ready() -> void:
	# Instantiaton
	var city: City = City.new("Tarraco", 3000)
```
{{% /tab %}}
{{% tab tabName="C#" %}}

- `City.cs`

```csharp
using Godot;
[GlobalClass]

public partial class City : Node
{
    public string CityName { get; set; }
    public int Population { get; set; }

    // Constructor
    public City(string name, int population)
    {
        CityName = name;
        Population = population;
    }
}
```
- `Main.cs`

```csharp
    public override void _Ready()
    {
        var city = new City("Tarraco", 3000);
    }
```

{{% /tab %}}
{{< /tabs >}}

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
var damage: int = 1

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

#### Instantiate a scene with parameters using a custom inititalization method

The following code will instantiate 1 Gem in the player position after the players drop the gem:

- `item.gd`

```gdscript
export (String) var item_name
export (int) var item_quantity = 1

func my_custom_init(name: String, quantity: int):
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
	item.my_custom_init(name, quantity)
	add_child(item)
	item.position = position
	inventory.erase(name)

func ready():
	drop(inventory[0])
```

{{< alert context="warning" text="Note that you cannot instantiate an object from its own script (You cannot instantiate **item** from **item.gd**)." />}}

<br>

#### Instantiate a scene using properties directly

We can also set up the instance properties before adding the instance to the scene via `add_child`:

```gdscript
const ITEM = preload("res://scenes/item_object.tscn")

var inventory: Array = [ "Gem", "Coin", "Scroll"]

func drop():
	var item = ITEM.instantiate()
	item.name = inventory[0]
	item.quantity = 1
	item.position = position
	add_child(item)
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

Can be done with noticeable coupling using:

- $SomeNode or get_node("SomeNode")
- %SomeNode
- @export var my_node: Node
- creating a class
- @export NodePath

#### 3. Component communication

Can be done with minimal coupling using:

- signals
- groups
- autoloads
- using the physics engine (colliders)
- propagate_call

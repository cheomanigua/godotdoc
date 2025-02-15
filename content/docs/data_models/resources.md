---
weight: 3500
title: "Resources"
description: "Custom resources for data containers"
icon: "article"
date: "2025-02-12T16:56:26+02:00"
lastmod: "2025-02-12T16:56:26+02:00"
draft: false
toc: true
---

Base class for serializable objects.

Resource is the base class for all Godot-specific resource types, serving primarily as data containers. Since they inherit from [RefCounted](https://docs.godotengine.org/en/stable/classes/class_refcounted.html), resources are reference-counted and freed when no longer in use. They can also be nested within other resources, and saved on disk. [PackedScene](https://docs.godotengine.org/en/stable/classes/class_packedscene.html), one of the most common [Objects](https://docs.godotengine.org/en/stable/classes/class_object.html) in a Godot project, is also a resource, uniquely capable of storing and instantiating the [Nodes](https://docs.godotengine.org/en/stable/classes/class_node.html) it contains as many times as desired.

[Godot Documentation](https://docs.godotengine.org/en/stable/classes/class_resource.html)


## How to create and use resources

1. Create a new class via gdscript and extends from **Resource**.
2. Create a new **Resource** from the new class in the inspector.
3. Edit the properties of the resource and save it.
4. Repeat step 3 as needed.
5. Create a new scene
6. Attach a new script to the new scene where you export the resource in the script
7. Drag and drop one of the `.tres`files created in step 3 into the resource slot in the scene inspector


### Step 1. Create new class

`npc_attributes.gd`

```gdscript
class_name NPC
extends Resource

@export var race_name: String
@export var health: int
@export var strength: int
@export var intelligence: int
@export var dexterity: int
```

### Step 2. Create new resource

1. In the inspector, click on *Create a new resource* icon.
2. In the new dialog that opens, search for `npc`. You'll get *NPC(npc_attributes.gd)*
3. Click on **Create** button.

### Step 3. Edit the resource properties

1. In the inspector you'll see all the the properties we created in the class ready to be filled. Edit the properties as needed.
2. Click on the **Save resource** icon and choose **Save As..**
3. In the new dialog that opens, type in the name you want to give to the new resource.
4. Click on **Save** button. The new resource is created as a `.tres` file.

### Step 4. Repeat step 3 as needed

- Repeat Step 3 as many times as different resources you want to create.

### Step 5. Create a new scene

1. Create a `CharacterBody2D` scene and call it **NPC**.

### Step 6. Create and attach a new script to the scene

1. Attach a new script to the newly created scene where it exports the resource:

`npc.gd`

```gdscript
extends CharacterBody2D

@export var npc: NPC

func _ready() -> void:
	print(npc.race_name)
```

### Step 7. Test

1. Instantiate the **NPC** scene from the main node.
2. Select the instantiated **NPC** node from the scene tree.
3. Drag one of the `.tres` resources created and drop it in the **npc** slot in the inspector.
4. If you run the game, it will print the name of the **race** of the particular `.tres` resource file you dragged.


## Create resources in batch from JSON file

You can generate several to dozens or hundreds of `.tres` files from a single JSON file. In the next two examples, we are generating five different `.tres` files from this [JSON file](https://drive.google.com/file/d/1pqJw1z3rW2_9pZzKRPQUmhrX_wpwNScq/view?usp=drive_link). The order of the keys and values does not matter.

The first example uses a resource file with variables for the `Races` class properties. The second example uses a dictionary for the `Races` class properties. The difference lies in convenience. If you use dictionaries, you can easily iterate through all the properties. Otherwise, you have to access each property individually. It's up to you and the design of your game.

{{< alert context="info" text="You can create a lot of different `.tres` files in seconds from a JSON file. If you want to speed up your workflow even further, you can create all the data very fast in a spreadsheet. Then you can export it to a CSV file and convert it to JSON. You can check [this article](dictionaries/#csv-to-json)." />}}


### Variables

Follow these steps:

- Create the `Races` class in a new standalone script:

```gdscript
class_name Races
extends Resource

@export var race_name: String
@export var strength: float
@export var intelligence: float
@export var dexterity: float
@export var endurance: float
@export var health: float:
	set(value):
		health = endurance + strength
```
<br>

- Create a node (Main, World, ResourceCreator, etc) and attach this script:

```gdscript
extends Node

func get_creatures_data() -> Dictionary:
	var file = FileAccess.open("res://Data/creatures.json", FileAccess.READ)
	var json = JSON.parse_string(file.get_as_text())
	file.close()
	return json


func _ready():
	var creatures: Dictionary = get_creatures_data()
	var resource: Resource = Races.new()
	for race in creatures:
		for attribute in creatures[race]:
			resource.set(attribute, creatures[race][attribute])
		var resource_path = "res://resources/" + race +".tres" # Choose your path.
		ResourceSaver.save(resource, resource_path)
```

<br>

Run the scene once and then stop it. All the five `.tres` files will be generated in the folder `res://resources/`



### Dictionaries

Follow these steps:

- Create the `Races` class in a new standalone script:

```gdscript
class_name Races
extends Resource

@export var attributes: Dictionary = {}
```
<br>


- Create a node (Main, World, ResourceCreator, etc) and attach this script:

```gdscript
extends Node

func get_creatures_data() -> Dictionary:
	var file = FileAccess.open("res://Data/creatures.json", FileAccess.READ)
	var json = JSON.parse_string(file.get_as_text())
	file.close()
	return json


func _ready():
	var creatures: Dictionary = get_creatures_data()
	var resource: Resource = Races.new()
	for race in creatures:
		resource.attributes = creatures[race]
		var resource_path = "res://resources/" + race +".tres" # Choose your path.
```
<br>

Run the scene once and then stop it. All the five `.tres` files will be generated in the folder `res://resources/`

## Instantiate a NPC at runtime

{{< alert context="warning" text="If you are creating the `.tres` files in batch as explained above, and the `.tres` files have not been created yet, use `load` instead of `preload` to load the resources. Otherwise, you will get an error when launching the scene. If the resources have been created, you can use either `load` or `preload`." />}}


### Specified

You can create instances dynamically at runtime and assign a specific `.tres` file to the instance:

```gdscript
extends Node

const NPC = preload("res://npc.tscn")

func _ready() -> void:
	var npc = NPC.instantiate()
	npc.race = load("res://resources/goblin.tres")
	npc.transform = Transform2D(0, Vector2(100, 100 ))
	add_child(npc)
```

<br>

### Random

You can create instances dynamically at runtime and assign a random `.tres` file to the instance by using a [JSON file](https://drive.google.com/file/d/1pqJw1z3rW2_9pZzKRPQUmhrX_wpwNScq/view) as source containing all the data for every NPC type:

```gdscript
extends Node

const NPC = preload("res://npc.tscn")

func get_creatures_data() -> Dictionary:
	var file = FileAccess.open("res://Data/creatures.json", FileAccess.READ)
	var json = JSON.parse_string(file.get_as_text())
	file.close()
	return json


func _ready() -> void:
	
	var creatures: Dictionary = get_creatures_data()
	var npc = NPC.instantiate()
	
	randomize()
	var a = randi() % creatures.size()
	var filename = creatures.keys()[a]
	
	npc.race = load("res://resources/" + filename + ".tres")
	npc.transform = Transform2D(0, Vector2(100, 100))
	add_child(npc)
```

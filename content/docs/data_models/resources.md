---
weight: 3500
title: "Resources"
description: "Custom resources for data storage"
icon: "article"
date: "2025-02-12T16:56:26+02:00"
lastmod: "2025-02-12T16:56:26+02:00"
draft: false
toc: true
---

[Godot Documentation](https://docs.godotengine.org/en/stable/classes/class_resource.html)


## How to create and use resources

1. Create a new class with via gdscript and extends from **Resource**.
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

@export var race: String
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

1. Attach a new script to the newly created scene and export the resource:

`npc.gd`

```gdscript
extends CharacterBody2D

@export var npc: NPC

func _ready() -> void:
	print(npc.race)
```

### Step 7. Test

1. Instantiate the **NPC** scene from the main node.
2. Select the instantiated **NPC** node from the scene tree.
3. Drag one of the `.tres` resources created and drop it in the **npc** slot in the inspector.
4. If you run the game, it will print the name of the **race** of the particular `.tres` resource file you dragged.


## Create resources in batch from JSON file

You can generate several to dozens or hundreds of `.tres` files from a single JSON file. In this example, we are generating five different `.tres` files from this [JSON file](https://drive.google.com/file/d/1lkMs1Yh7TzhiIBZON0oo9a3gSrr7PFbf/view?usp=drive_link). The order of the keys and values does not matter.

Follow these steps:

- Create the Races class in a new standalone script:

```gdscript
class_name Races
extends Resource

@export var race: String
@export var strength: float
@export var intelligence: float
@export var dexterity: float
@export var endurance: float
@export var health: float = strength + endurance
```

- Create a node and attach this script:

```gdscript
extends Node

func get_creatures_data() -> Dictionary:
	var file = FileAccess.open("res://Data/test.json", FileAccess.READ)
	var json = JSON.parse_string(file.get_as_text())
	file.close()
	return json


func _ready():
	var creatures: Dictionary = get_creatures_data()
	var resource: Resource = Races.new()
	creatures = get_creatures_data()
	for race in creatures:
		resource.set("race", race)
		for attribute in creatures[race]:
			resource.set(attribute, creatures[race][attribute])
		var resource_path = "res://resources/" + race +".tres" # Choose your path.
		ResourceSaver.save(resource, resource_path)
```

Run the scene once and then stop it. All the five `.tres` files will be generated in the folder `res://resources/`


{{< alert context="info" text="You can create a lot of different `.tres` files in seconds from a JSON file. If you want to speed up your workflow even further, you can create all the data very fast in a spreadsheet. Then you can export it to a CSV file and convert it to JSON. You can check [this article](dictionaries/#csv-to-json)." />}}


### Dynamically instantiating

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
	var fname = creatures.keys()[a]
	
	npc.race = Races.new()
	npc.race = load("res://resources/" + fname + ".tres")
	npc.transform = Transform2D(0, Vector2(100 * a, 100 * a))
	add_child(npc)
```

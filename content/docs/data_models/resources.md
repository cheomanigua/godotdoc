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

- [Godot Documentation - Resource Class](https://docs.godotengine.org/en/stable/classes/class_resource.html)
- [Godot Documentation - Custom Resources](https://docs.godotengine.org/en/stable/tutorials/scripting/resources.html#creating-your-own-resources)


## How to create and use resources

1. Create a new class via gdscript and extends from **Resource**.
2. Create a new **Resource** from the new class in the inspector.
3. Edit the properties of the resource and save it.
4. Repeat step 3 as needed.
5. Create a new scene
6. Attach a new script to the new scene where you export the resource in the script
7. Drag and drop one of the `.tres`files created in step 3 into the resource slot in the scene inspector


### Step 1. Create new class

{{< tabs tabTotal="2">}}
{{% tab tabName="GDScript" %}}

`race.gd`

```gdscript
class_name Race
extends Resource

@export var race_name: String
@export var strength: int
@export var intelligence: int
@export var dexterity: int
@export var health: int
```
{{% /tab %}}
{{% tab tabName="C#" %}}

`Race.cs`

```csharp
using Godot;
[GlobalClass]

public partial class Race : Resource
{
    [Export] public string RaceName {get; set;}
    [Export] public int Strength {get; set;}
    [Export] public int Intelligence {get; set;}
    [Export] public int Dexterity {get; set;}
    [Export] public int Health {get; set;}
}
```
{{% /tab %}}
{{< /tabs >}}


### Step 2. Create new resource

1. In the inspector, click on *Create a new resource* icon.
2. In the new dialog that opens, search for `race`. You'll get *Race(race.gd)*
3. Click on **Create** button.

### Step 3. Edit the resource properties

1. In the inspector you'll see all the the properties we created in the class ready to be filled. Edit the properties as needed.
2. Click on the **Save resource** icon and choose **Save As..**
3. In the new dialog that opens, type in the name you want to give to the new resource.
4. Click on **Save** button. The new resource is created as a `.tres` file.

### Step 4. Repeat step 3 as needed

- Repeat Step 3 as many times as different resources you want to create.

### Step 5. Create a new scene

1. Create a `CharacterBody2D` scene and call it **NPC**. It will create the file `npc.tscn`.

{{< alert context="info" text="Throughout this article the NPC scene we created here in **Step 5** will be used, referenced or mentioned. Go back up here if in doubt." />}}

### Step 6. Create and attach a new script to the scene

1. Attach a new script to the newly created scene where it exports the resource:

{{< tabs tabTotal="2">}}
{{% tab tabName="GDScript" %}}
`npc.gd`

```gdscript
extends CharacterBody2D

@export var race: Race

func print_name() -> void:
	print(race.race_name)
```
{{% /tab %}}
{{% tab tabName="C#" %}}

`NPC.cs`

```csharp
using Godot;

public partial class NPC : CharacterBody2D
{
    [Export] public Race Race_ {get; set;}

    public void PrintName()
    {
        GD.Print(race.RaceName);
    }
}
```
{{% /tab %}}
{{< /tabs >}}

{{< alert context="info" text="Throughout this article the NPC script we created here in **Step 6** will be used, referenced or mentioned. Go back up here if in doubt." />}}

### Step 7. Test

1. Instantiate the **NPC** scene from the main node.
2. Select the instantiated **NPC** node from the scene tree.
3. Drag one of the `.tres` resources created and drop it in the **race** slot in the inspector.
4. If you run the game, it will print the name of the race of the particular `.tres` resource file you dragged.


## Create resources in batch from JSON file

You can generate several to dozens or hundreds of `.tres` files from a single JSON file. In the next two examples, we are generating five different `.tres` files from this [JSON file](https://drive.google.com/file/d/1pqJw1z3rW2_9pZzKRPQUmhrX_wpwNScq/view?usp=drive_link). The order of the keys and values does not matter.

The first example uses a resource file with variables for the `Race` class properties. The second example uses a dictionary for the `Race` class properties. The difference lies in convenience. If you use dictionaries, you can easily iterate through all the properties. Otherwise, you have to access each property individually. It's up to you and the design of your game.

{{< alert context="info" text="You can create a lot of different `.tres` files in seconds from a JSON file. If you want to speed up your workflow even further, you can create all the data very fast in a spreadsheet. Then you can export it to a CSV file and convert it to JSON. You can check [this article](dictionaries/#csv-to-json)." />}}


### Variables

{{< alert context="info" text="Throughout this article the `race.gd` and `Race.cs` scripts created below will be referenced or mentioned. Go back up here if in doubt." />}}

Follow these steps:


{{< tabs tabTotal="3">}}
{{% tab tabName="GDScript" %}}

1. Create the `race.gd` class in a new standalone script:

    ```gdscript
    class_name Race
    extends Resource

    var is_health_initated: bool = false
    @export var race_name: String
    @export var strength: int
    @export var intelligence: int
    @export var dexterity: int
    @export var endurance: int
    @export var health: int:
        set(value):
            if value == 0 && !is_health_initated:
                value = strength + endurance
                is_health_initated = true
            health = clampi(value, 0, strength + endurance)
    ```
    <br>

2. Create the node **ResourceCreator** attach this **GDScript** `resource_creator.gd` script:

    ```gdscript
    extends Node

    func _ready():
        var resource: Resource = Race.new()     # GDSCRIPT RESOURCE FILE
        var creatures: Dictionary = get_creatures_data()
        for race in creatures:
            for attribute in creatures[race]:
                resource.set(attribute, creatures[race][attribute])
            var resource_path = "res://resources/gdscript/" + race + ".tres"
            ResourceSaver.save(resource, resource_path)


    func get_creatures_data() -> Dictionary:
        var file = FileAccess.open("res://Data/creatures.json", FileAccess.READ)
        var json = JSON.parse_string(file.get_as_text())
        file.close()
        return json
    ```

    <br>

3. Run the scene once and then stop it. All the five `.tres` files will be generated in the folder `res://resources/gdscript/`

{{< alert context="warning" text="Be sure the formatting (snake case, Pascal case, etc) of the dictionary keys and the resource class properties matches when assigning the values to the resource. If they don't match, the resource will be generated with zero values. You can use helper methods like `to_camel_case()`, `to_snake_case()`, `to_pascal_case()` and `capitalize()` to convert between different formattings if necessary." />}}

{{% /tab %}}
{{% tab tabName="C#" %}}

1. Create the `Race.cs` class in a new standalone script:

    ```csharp
    using Godot;
    [GlobalClass]

    public partial class Race : Resource
    {
        bool is_health_initiated = false;
        private int health;

        [Export] public string RaceName {get; set;}
        [Export] public int Strength {get; set;}
        [Export] public int Intelligence {get; set;}
        [Export] public int Dexterity {get; set;}
        [Export] public int Endurance {get; set;}
        [Export] public int Health 
        {
            get => health;
            set
            {
                if (value == 0 && !is_health_initiated)
                {
                    value = Strength + Endurance;
                    is_health_initiated = true;
                }
                health = Mathf.Clamp(value, 0, Strength + Endurance);
            }
        }

    }
    ```
    <br>

2. Create the node **ResourceCreator** attach this **C#** `ResourceCreator.cs` script:

    ```csharp
    using Godot;
    using Godot.Collections;

    public partial class ResourceCreator : Node
    {
        public override void _Ready()
        {
            Resource resource = new Race();     // CSHARP RESOURCE FILE

            Dictionary creatures = GetCreaturesData();
            
            foreach (var race in creatures)
            {
                Dictionary<string, Variant> raceDict = (Dictionary<string, Variant>)race.Value;
                foreach (var attribute in raceDict)
                {
                    resource.Set(attribute.Key.ToPascalCase(), attribute.Value);
                var resource_path = "res://resources/csharp/" + race.Key + ".tres";
                ResourceSaver.Save(resource, resource_path);
                }
            }
        }

        public static Dictionary GetCreaturesData()
        {
            var file = FileAccess.Open("res://Data/creatures.json", FileAccess.ModeFlags.Read);
            var json = (Dictionary)Json.ParseString(file.GetAsText());
            file.Close();
            return json;
        }
    }
    ```
    <br>

3. Run the scene once and then stop it. All the five `.tres` files will be generated in the folder `res://resources/csharp/`

{{< alert context="warning" text="Be sure the formatting (snake case, Pascal case, etc) of the dictionary keys and the resource class properties matches when assigning the values to the resource. If they don't match, the resource will be generated with zero values. You can use helper methods like `ToCamelCase()`, `ToSnakeCase()`, `ToPascalCase()` and `Capitalize()` to convert between different formattings if necessary." />}}

{{% /tab %}}
{{% tab tabName="GDScript from C#" %}}

1. Create the `race.gd` class in a new standalone script:

    ```gdscript
    class_name Race
    extends Resource

    var is_health_initated: bool = false
    @export var race_name: String
    @export var strength: int
    @export var intelligence: int
    @export var dexterity: int
    @export var endurance: int
    @export var health: int:
        set(value):
            if value == 0 && !is_health_initated:
                value = strength + endurance
                is_health_initated = true
            health = clampi(value, 0, strength + endurance)
    ```
    <br>

2. Create the node **ResourceCreator** attach this **C#** `ResourceCreator.cs` script:

    ```csharp
    using Godot;
    using Godot.Collections;

    public partial class ResourceCreator : Node
    {
        public override void _Ready()
        {
            var myGDScript = GD.Load<GDScript>("res://race.gd");    // GDSCRIPT RESOURCE FILE
            var resource = (Resource)myGDScript.New();

            Dictionary creatures = GetCreaturesData();

            foreach (var race in creatures)
            {
                Dictionary<string, Variant> raceDict = (Dictionary<string, Variant>)race.Value;
                foreach (var attribute in raceDict)
                {
                    resource.Set(attribute.Key.ToSnakeCase(), attribute.Value);
                var resource_path = "res://resources/gdscript/" + race.Key + ".tres";
                ResourceSaver.Save(resource, resource_path);
                }
            }
        }

        public static Dictionary GetCreaturesData()
        {
            var file = FileAccess.Open("res://Data/creatures.json", FileAccess.ModeFlags.Read);
            var json = (Dictionary)Json.ParseString(file.GetAsText());
            file.Close();
            return json;
        }
    }
    ```
    <br>

3. Run the scene once and then stop it. All the five `.tres` files will be generated in the folder `res://resources/gdscript/`

{{< alert context="warning" text="Be sure the formatting (snake case, Pascal case, etc) of the dictionary keys and the resource class properties matches when assigning the values to the resource. If they don't match, the resource will be generated with zero values. You can use helper methods like `ToCamelCase()`, `ToSnakeCase()`, `ToPascalCase()` and `Capitalize()` to convert between different formattings if necessary." />}}

{{% /tab %}}
{{< /tabs >}}



### Dictionaries

Follow these steps:

{{< tabs tabTotal="2">}}
{{% tab tabName="GDScript" %}}

1. Create the `race.gd` class in a new standalone script:

    ```gdscript
    class_name Race
    extends Resource

    @export var attributes: Dictionary = {
        "race_name": "",
        "strength": 0,
        "intelligence": 0,
        "dexterity": 0,
        "endurance": 0,
        "health": 0
    }
    ```
    <br>


2. Create the node **ResourceCreator** attach this **GDScript** `resource_creator.gd` script:

    ```gdscript
    extends Node

    func _ready():
        var creatures: Dictionary = get_creatures_data()
        var resource: Resource = Race.new()
        for race in creatures:
            resource.attributes = creatures[race]
            var resource_path = "res://resources/gdscript/" + race + ".tres" # Choose your path.

    func get_creatures_data() -> Dictionary:
        var file = FileAccess.open("res://Data/creatures.json", FileAccess.READ)
        var json = JSON.parse_string(file.get_as_text())
        file.close()
        return json
    ```
    <br>

3. Run the scene once and then stop it. All the five `.tres` files will be generated in the folder `res://resources/gdscript/`

{{% /tab %}}
{{% tab tabName="C#" %}}

1. Create the `Race.cs` class in a new standalone script:

    ```csharp
    using Godot;
    using Godot.Collections;
    [GlobalClass]

    public partial class Race : Resource
    {
        [Export] public Dictionary<string, Variant> Attributes = new Dictionary<string, Variant>();

        public Race()
        {
            Attributes["RaceName"] = "";
            Attributes["Strength"] = 0;
            Attributes["Intelligence"] = 0;
            Attributes["Dexterity"] = 0;
            Attributes["Endurance"] = 0;
            Attributes["Health"] = 0;
        }
    }
    ```
    <br>

2. Create the node **ResourceCreator** attach this **C#** `ResourceCreator.cs` script:

    ```csharp
    using Godot;
    using Godot.Collections;
    using System.Collections.Generic;

    public partial class Spawner : Node
    {
        public override void _Ready()
        {
            Resource resource = new Race();
            Dictionary creatures = GetCreaturesData();

            foreach (var race in creatures)
            {
                resource.Set("Attributes", race.Value);
                string resource_path = "res://resources/csharp/" + race.Key + ".tres";
                ResourceSaver.Save(resource, resource_path);
            }
        }

        public static Dictionary GetCreaturesData()
        {
            var file = FileAccess.Open("res://Data/creatures.json", FileAccess.ModeFlags.Read);
            var json = (Dictionary)Json.ParseString(file.GetAsText());
            file.Close();
            return json;
        }
    }
    ```
    <br>

3. Run the scene once and then stop it. All the five `.tres` files will be generated in the folder `res://resources/csharp/`

{{% /tab %}}
{{< /tabs >}}

## Instantiate a NPC at runtime

{{< alert context="warning" text="If you are creating the `.tres` files in batch as explained above, and the `.tres` files have not been created yet, use `load` instead of `preload` to load the resources. Otherwise, you will get an error when launching the scene. If the resources have been created, you can use either `load` or `preload`." />}}


### Specified

You can create instances dynamically at runtime and assign a specific `.tres` file to the instance. For these examples below, we create a Node called `Spawner` and create the correspoding script. In the scene tree, don't forget to attach the `npc.gd` or `NPC.cs` to the `npc.tscn` scene.

{{< tabs tabTotal="3">}}
{{% tab tabName="GDScript" %}}

```gdscript
extends Node

const NPC = preload("res://npc.tscn")       # Remember to attach npc.gd to npc.tscn

func _ready() -> void:
	var npc = NPC.instantiate()
	npc.race = load("res://resources/gdscript/goblin.tres")
	npc.transform = Transform2D(0, Vector2(100, 100 ))
	print(npc.race.race_name)   # Not so good option
	npc.print_name()            # Better option
	add_child(npc)
```
{{% /tab %}}
{{% tab tabName="C#" %}}

```csharp
public partial class Spawner : Node
{
    public PackedScene NPCScene = (PackedScene)ResourceLoader.Load("res://npc.tscn");   // Remember to attach NPC.cs to npc.tscn
    public override void _Ready()
    {
        CharacterBody2D npcInstance = (CharacterBody2D)NPCScene.Instantiate();
        NPC npc = npcInstance as NPC; // NPC.cs class
        npc.Race_ = GD.Load<Race>("res://resources/csharp/goblin.tres");
        npc.Transform = new Transform2D(0f, new Vector2(100, 100));
        GD.Print(npc.race.RaceName)     // Not so good option
        npc.PrintName()                 // Better option
        AddChild(npcInstance);
    }
}
```

{{% /tab %}}
{{% tab tabName="GDSCript from C#" %}}

```csharp
public partial class Spawner : Node
{
    public PackedScene NPCScene = (PackedScene)ResourceLoader.Load("res://npc.tscn");   // Remember to attach npc.gd to npc.tscn
    public override void _Ready()
    {
        CharacterBody2D npcInstance = (CharacterBody2D)NPCScene.Instantiate();
        Resource Race = GD.Load<Resource>("res://resources/gdscript/goblin.tres"));
        npcInstance.Set("race", Race);
        npc.Transform = new Transform2D(0f, new Vector2(100, 100));
        GD.Print(Race.Get("race_name"));    // Not so good option
        npcInstance.Call("print_name");     // Better option
        AddChild(npcInstance);
    }
}
```
{{< alert context="success" text="Why would you instantiate a `gdscript` scene in a `csharp` script class? Well, If you are going to spawn hundreds of NPCs, using `csharp` to spawn those NPCs is faster than using `gdscript`. So a good rule of thumb is to create your classes and resources in `gdscript` if you prefer this language, and use `csharp` to handle those classes only when performance is critical." />}}


{{% /tab %}}
{{< /tabs >}}

<br>

### Random

You can create instances dynamically at runtime and assign a random `.tres` file to the instance by using a [JSON file](https://drive.google.com/file/d/1pqJw1z3rW2_9pZzKRPQUmhrX_wpwNScq/view) as source containing all the data for every NPC type:

{{< tabs tabTotal="2">}}
{{% tab tabName="GDScript" %}}

```gdscript
extends Node

const NPC = preload("res://npc.tscn")

func _ready() -> void:
	
	var creatures: Dictionary = get_creatures_data()

	# Generate 50 random NPCs
	for i in 50:
		randomize()
		var a = randi() % creatures.size()
		var filename = creatures.keys()[a]
		var xaxis = randi_range(100, 1100)  # random x axis
		var yaxis = randi_range(100, 600)   # random y axis
		
		var npc = NPC.instantiate()
		npc.race = load("res://resources/gdscript/" + filename + ".tres")
		npc.transform = Transform2D(0, Vector2(xaxis, yaxis))
		add_child(npc)

func get_creatures_data() -> Dictionary:
	var file = FileAccess.open("res://Data/creatures.json", FileAccess.READ)
	var json = JSON.parse_string(file.get_as_text())
	file.close()
	return json
```
{{% /tab %}}
{{% tab tabName="C#" %}}

```csharp
using Godot;
using Godot.Collections;
using System.Collections.Generic;

public partial class Spawner : Node
{
	public PackedScene npcScene = (PackedScene)ResourceLoader.Load("res://npc.tscn");

	public override void _Ready()
	{
		Dictionary creatures = GetCreaturesData();

		List<string> races = new List<string> {};
		foreach (var key in creatures.Keys)
		{
			races.Add((string)key);
		}
		
		// Generate 50 random NPCs
		for (int i = 0; i < 50; i++)
		{
			string filename = GetRandomElement(races);
			var xaxis = GD.RandRange(100, 1100);
			var yaxis = GD.RandRange(100, 600);

			CharacterBody2D npcInstance = (CharacterBody2D)npcScene.Instantiate();
			NPC npc = npcInstance as NPC;
			npc.Race_ = GD.Load<Race>("res://resources/csharp/" + filename + ".tres");
			npc.Transform = new Transform2D(0.0f, new Vector2(xaxis, yaxis));
			AddChild(npcInstance);
		}
	}

	public static Dictionary GetCreaturesData()
	{
		var file = FileAccess.Open("res://Data/creatures.json", FileAccess.ModeFlags.Read);
		var json = (Dictionary)Json.ParseString(file.GetAsText());
		file.Close();
		return json;
	}

	private T GetRandomElement<T>(List<T> list)
    {
        System.Random random = new System.Random();
        int randomIndex = random.Next(list.Count); // Get a random index from 0 to list.Count - 1
        return list[randomIndex]; // Return the element at the random index
    }
}
```
{{% /tab %}}
{{< /tabs >}}


## NPC class usage examples

As we saw previously, we created resources using a [variable data model](resources/#variables) and a [dictionary data model](resources/#dictionaries). Depending on the data model selected to create the resources (`.tres` files), the NPC class interface implementation may differ. Below there are several examples.

### Variable created resources

{{< tabs tabTotal="2">}}
{{% tab tabName="GDScript" %}}

```gdscript
class_name Npc
extends CharacterBody2D

const NPC: PackedScene = preload("res://npc.tscn")
@export var race: Race

func _unhandled_input(_event: InputEvent) -> void:
	if Input.is_mouse_button_pressed(MOUSE_BUTTON_WHEEL_UP):
		increase_health(1)
	if Input.is_mouse_button_pressed(MOUSE_BUTTON_WHEEL_DOWN):
		decrease_health(1)

func _ready() -> void:
	set_pickable(true)
	mouse_entered.connect(_on_mouse_entered)

func _on_mouse_entered():
	print_stats()

######## BLOCK SPECIFIC FOR VARIABLE CREATED RESOURCES ########

func print_stats():
	print("Hello from C#. I'm a ", race.race_name,
	", my Health is ", race.health,
	", my Max Health is ", race.strength + race.endurance)

func increase_health(health: int):
	race.health += health
	print("+1 Health is now: ", race.health);

func decrease_health(health: int):
	race.health -= health
	print("-1 Health is now: ", race.health);
```

{{% /tab %}}
{{% tab tabName="C#" %}}

```csharp
using Godot;
public partial class NPC : CharacterBody2D
{
    [Export] public Race Race_ {get; set;}

    public override void _Ready()
    {
        SetPickable(true);
        MouseEntered += OnMouseEntered;
    }

    public override void _UnhandledKeyInput(InputEvent @event)
    {
        base._UnhandledKeyInput(@event);
        if (@event.IsActionPressed("ui_select"))
            PrintStats()
    }

    public override void _UnhandledInput(InputEvent @event)
    {
        base._UnhandledInput(@event);
        if (Input.IsMouseButtonPressed(MouseButton.WheelUp))
		{
			IncreaseHealth(1);

		}
        if (Input.IsMouseButtonPressed(MouseButton.WheelDown))
		{
			DecreaseHealth(1);
		}
    }

    private void OnMouseEntered()
    {
        PrintStats()
    }


    /////////// BLOCK SPECIFIC FOR VARIABLE CREATED RESOURCES //////////

    public void PrintStats()
    {
        GD.Print("Hello from C#. I'm a ", Race_.RaceName,
        ", my Health is ", Race_.Health,
        ", my Max Health is ", Race_.Strength + Race_.Endurance);
    }

    public void IncreaseHealth(int health)
    {
        Race_.Health += health;
        GD.Print("+1 Health is now: ", Race_.Health);
    }

    public void DecreaseHealth(int health)
    {
        Race_.Health -= health;
        GD.Print("-1 Health is now: ", Race_.Health);
    }
}
```

{{% /tab %}}
{{< /tabs >}}


### Dictionary created resources

{{< tabs tabTotal="2">}}
{{% tab tabName="GDScript" %}}

```gdscript
class_name Npc
extends CharacterBody2D

const NPC: PackedScene = preload("res://npc.tscn")
@export var race: Race

func _unhandled_input(_event: InputEvent) -> void:
	if Input.is_mouse_button_pressed(MOUSE_BUTTON_WHEEL_UP):
		increase_health(1)
	if Input.is_mouse_button_pressed(MOUSE_BUTTON_WHEEL_DOWN):
		decrease_health(1)

func _ready() -> void:
	set_pickable(true)
	mouse_entered.connect(_on_mouse_entered)

func _on_mouse_entered():
	print_stats()

######## BLOCK SPECIFIC FOR DICTIONARY CREATED RESOURCES ########

func print_stats():
	print("Hello from C#. I'm a ", race.attributes.race_name,
	", my Health is ", race.attributes.health,
	", my Max Health is ", race.attributes.strength + race.attributes.endurance)

func increase_health(health: int):
	race.attributes.health += health
	print("+1 Health is now: ", race.attributes.health)

func decrease_health(health: int):
	race.attributes.health -= health
	print("-1 Health is now: ", race.attributes.health)
```

{{% /tab %}}
{{% tab tabName="C#" %}}

```csharp
using Godot;
public partial class NPC : CharacterBody2D
{
    [Export] public Race Race_ {get; set;}

    public override void _Ready()
    {
        SetPickable(true);
        MouseEntered += OnMouseEntered;
    }

    public override void _UnhandledKeyInput(InputEvent @event)
    {
        base._UnhandledKeyInput(@event);
        if (@event.IsActionPressed("ui_select"))
            PrintStats()
    }

    public override void _UnhandledInput(InputEvent @event)
    {
        base._UnhandledInput(@event);
        if (Input.IsMouseButtonPressed(MouseButton.WheelUp))
		{
			IncreaseHealth(1);

		}
        if (Input.IsMouseButtonPressed(MouseButton.WheelDown))
		{
			DecreaseHealth(1);
		}
    }

    private void OnMouseEntered()
    {
        PrintStats()
    }


    /////////// BLOCK SPECIFIC FOR DICTIONARY CREATED RESOURCES //////////

    public void PrintStats()
    {
        GD.Print("Hello from C#. I'm a ", Race_.Attributes["RaceName"],
        ", my Health is ", Race_.Attributes["Health"],
        ", my Max Health is ", (int)Race_.Attributes["Strength"] + (int)Race_.Attributes["Endurance"]);
    }

    public void IncreaseHealth(int health)
    {
        Race_.Attributes["Health"] = (int)Race_.Attributes["Health"] + health;
        GD.Print("+1 Health is now: ", Race_.Attributes["Health"]);
    }

    public void DecreaseHealth(int health)
    {
        Race_.Attributes["Health"] = (int)Race_.Attributes["Health"] - health;
        GD.Print("-1 Health is now: ", Race_.Attributes["Health"]);
    }
}
```
{{% /tab %}}
{{< /tabs >}}

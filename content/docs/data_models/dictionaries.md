---
weight: 3300
title: "Dictionaries"
description: ""
icon: "article"
date: "2024-08-24T14:59:45+02:00"
lastmod: "22024-08-24T14:59:45+02:00"
draft: false
toc: true
---

[Godot Documentation](https://docs.godotengine.org/en/stable/classes/class_dictionary.html)


Dictionaries contain a list of key-value pairs {"key" : "value"}. The key-value pairs can be of different types:

```gdscript
var inventory = {
    "coin" : 5, 
    "gem" : 2,
    "color" : "green"
}
```
Access:

- `inventory` will return **{coin: 5, gem: 2, color: green}**
- `inventory["coin"]` will return **5**
- `inventory.coin` will return **5**
- `inventory.keys()` will return **[coin, gem, color]**
- `inventory.keys()[0]` will return **coin**
- `inventory.values()` will return **[5, 2, green]**
- `inventory.values()[0]` will return **5**
- `inventory.get("coin")` will return **5**

Assignments:
- `inventory["coin"] = 2` will change the value of **coin** from **5** to **2**
- `inventory.coin = 2` is the same as above

Math:
- `inventory["coin"] += 2` will increment the value of **coin** by **2**
- `inventory["coin"] -= 2` will decrease the value of **coin** by **2**

Creating an entry:
- `inventory["potion"] = 3` will create the key **potion** with the value **3**
- `inventory.potion = 3` is the same as above

Deleting an entry:
- `inventory.erase("coin")`

Clearing the whole dictionary:
- `inventory.clear()`


### Printing dictionaries

- `print(inventory)`
- `print(JSON.print(inventory, "\t"))`
- Loops:

```gdscript

for key in inventory:
	print(key + " : " + str(inventory[key]))
```
or

```gdscript

for key in inventory:
	print("%s : %s" % [key, inventory[key]])
```
or

```gdscript

for i in inventory.size():
	print (inventory.keys()[i] + " : " + str(inventory.values()[i]))
	i+=1
```
or

```gdscript

for i in inventory.size():
	print ("%s : %s" % [inventory.keys()[i], inventory.values()[i]])
	i+=1
```

will print:

```
coin : 5
gem : 2
color : green
```

### Get random key on Dictionary

```gdscript
randomize()
var a = randi() % inventory.size()
print(inventory.keys()[a])
```

### Deep Copy

```gdscript
var copied_dictionary = str2var(var2str(original_dictionary))
```


### Dictionary Management

You can manage a dynamic dictionary by automatically allocating a new key or updating the value of a key:

#### Player.gd

```gdscript
var inventory = {}

func add_item(item):
	if inventory.has(item):
		inventory[item] += 1
		print("You have %d %s" % [inventory[item], item])
	else:
		inventory[item] = 1
		print("You have %d %s" % [inventory[item], item])
```

With the above setup, you can create a **item management** system. The example below shows how to decouple the responsibilities when the player picks up a coin and add it to the Player's inventory. The player enters an item's Area2D collision shape, whose `body_entered` signal is listened from **Item.gd**. Player scene is **Autoloaded** (global singleton), so the Player's `add_item()` function above can be accessed globally from any script:

#### Item.gd

```gdscript
extends Area2D

func _ready():
	pickup(get_parent().get_name())

func pickup(item):
	body_entered.connect(_on_body_entered,[item])

func _on_body_entered(body,item):
	if body.name == "Player":
		Player.add_item(item)
		queue_free()
```

## More complex dictionaries

Dictionaries can be more complex that the previous examples.

### 1. Array within dictionary

{{< tabs tabTotal="2">}}
{{% tab tabName="GDScript" %}}

```gdscript
extends Node

var inventory = {
	0: ["Silver", 23],
	1: ["Gold", 56],
	2: ["Ruby", 8]
}

func _ready():
	## loop through the dictionary keys to match the value to be "Gold" via the first element of the array
	for key in inventory:
		if inventory[key][0] == "Gold":
			print("There is Gold!!!!")

	## other interesting stuff
	var i = 1
	print(inventory.keys()[i]) ## will print 1
	print(inventory[0][0]) ## will print "Silver"
	print(inventory[0][1]) ## will print 23
	print(inventory[2][0]) ## will print "Ruby"
	print(inventory[2][1]) ## will print 8

	## add a new entry to the dictionary
	inventory[3] = ["Emerald", 3]

	## print a random array element of the dictionary
	randomize()
	var rand = randi() % inventory.size()
	print("%s : %s" % [inventory[rand][0], inventory[rand][1]])

	## print the full inventory
	for key in inventory:
		print("%s: %s" % [inventory[key][0], inventory[key][1]])
```
{{% /tab %}}
{{% tab tabName="C#" %}}

```csharp

using System;
using System.Collections.Generic;

class Inventory
{
    public override void _Ready()
    {
        // Initialize the dictionary
        Dictionary<int, (string item, int quantity)> inventory = new Dictionary<int, (string, int)>
        {
            { 0, ("Silver", 23) },
            { 1, ("Gold", 56) },
            { 2, ("Ruby", 8) }
        };

        // Loop through dictionary keys to find "Gold"
        foreach (var key in inventory.Keys)
        {
            if (inventory[key].item == "Gold")
            {
                Console.WriteLine("There is Gold!!!!");
            }
        }

        // Other interesting stuff
        int i = 1;
        Console.WriteLine(i); // Prints key at index i (1)
        Console.WriteLine(inventory[0].item); // Prints "Silver"
        Console.WriteLine(inventory[0].quantity); // Prints 23
        Console.WriteLine(inventory[2].item); // Prints "Ruby"
        Console.WriteLine(inventory[2].quantity); // Prints 8

        // Add a new entry to the dictionary
        inventory[3] = ("Emerald", 3);

        // Print a random array element of the dictionary
        Random random = new Random();
        int rand = random.Next(0, inventory.Count);
        Console.WriteLine($"{inventory[rand].item} : {inventory[rand].quantity}");

        // Print the full inventory
        foreach (var key in inventory)
        {
            Console.WriteLine($"{key.Value.item}: {key.Value.quantity}");
        }
    }
}
```

{{% /tab %}}
{{< /tabs >}}


### 2. Dictionary within dictionary

Multi-dimentional dictionaries a.k.a. nested dictionaries

{{< tabs tabTotal="2">}}
{{% tab tabName="GDScript" %}}

```gdscript
var cities: Array = ["Tarraco", "Ampuria"]
var products: Array = ["Wheat", "Olives"]
var urbes: Dictionary = {}

func _ready() -> void:
	# Create multiple cities at once
	for city in cities:
		urbes[city] = {}			# creates 1st level keys with "cities" for dictionary "urbes"
		urbes[city]["stock"] = {}	# creates 2nd level dictionary with key "stock"
		urbes[city]["price"] = {}	# creates 2nd level dictionary with key "price"

	# Generate each product stocks and prices for each city in dictionary "urbes"
	for city in urbes:
		# creates 3rd level dictionary with keys "Wheat" and "Olives"
		for product in products:
			urbes[city]["stock"][product] = randi_range(0, 1000)
			urbes[city]["price"][product] = randf_range(0.0, 2.0)

	# Printing the whole dictionary
	print(JSON.stringify(urbes, "\t"))
```
{{% /tab %}}
{{% tab tabName="C#" %}}

```csharp

using System;
using System.Collections.Generic;
using System.Text.Json;

publid class Economy
{
    public override void _Ready()
    {
        // Initialize lists
        List<string> cities = new List<string> { "Tarraco", "Ampuria" };
        List<string> products = new List<string> { "Wheat", "Olives" };

        // Initialize the nested dictionary
        Dictionary<string, Dictionary<string, Dictionary<string, double>>> urbes = new Dictionary<string, Dictionary<string, Dictionary<string, double>>>();

        // Random number generator
        Random random = new Random();

        // Create dictionary structure for each city
        foreach (string city in cities)
        {
            urbes[city] = new Dictionary<string, Dictionary<string, double>>
            {
                { "stock", new Dictionary<string, double>() },
                { "price", new Dictionary<string, double>() }
            };

            // Populate stock and price for each product in the city
            foreach (string product in products)
            {
                urbes[city]["stock"][product] = random.Next(0, 1001); // randi_range(0, 1000) equivalent
                urbes[city]["price"][product] = random.NextDouble() * 2.0; // randf_range(0.0, 2.0) equivalent
            }
        }

        // Print the dictionary as JSON
        string jsonOutput = JsonSerializer.Serialize(urbes, new JsonSerializerOptions { WriteIndented = true });
        Console.WriteLine(jsonOutput);
    }
}

```

{{% /tab %}}
{{< /tabs >}}

It will print:

```
{
	"Tarraco": {
		"stock": {
			"Wheat": 660,
			"Olives": 925
		},
		"price": {
			"Wheat": 0.92997416180484,
			"Olives": 0.12390274271286
		}
	},
	"Ampuria": {
		"stock": {
			"Wheat": 625,
			"Olives": 888
		},
		"price": {
			"Wheat": 0.22696960263633,
			"Olives": 0.89867763460801
		}
	}
}
```

## Load JSON files as dictionaries

Example file: [creatures.json](https://drive.google.com/file/d/16irrPAzEku4uLfroE1ri3X_i5ChUJEcE/view?usp=drive_link)

1. JSON file is downloaded to Godot project at location res://Data/creatures.json
2. A Global singleton script in created:

Given the [creatures.json](https://drive.google.com/file/d/16irrPAzEku4uLfroE1ri3X_i5ChUJEcE/view?usp=drive_link) file, we write the following code:

{{< tabs tabTotal="2">}}
{{% tab tabName="GDScript" %}}

```gdscript
extends Node

func get_creatures_data() -> Dictionary:
	var file = FileAccess.open("res://Data/creatures.json", FileAccess.READ)
	var json = JSON.parse_string(file.get_as_text())
	file.close()
	return json

func _ready():
	var creatures: Dictionary = get_creatures_data()
	# Testing
	print (creatures.keys())
	var ckey: String = "goblin"
	var cvalue: String = "strength"
	print ("%s stats are:" % [ckey.capitalize()])
	for key in creatures[ckey]:
		print ("%s : %s" % [key, creatures[ckey][key]])
	print("")
	print(ckey.capitalize())
	print("Strength: %d" % [creatures[ckey]["strength"]])
	print("Intelligence: %d" % [creatures[ckey]["intelligence"]])
	print("Dexterity: %d" % [creatures[ckey]["dexterity"]])
	print("Endurance: %d" % [creatures[ckey]["endurance"]])
	print("Health: %d" % [creatures[ckey]["health"]])
	print("")
	print("a) %s strength is %d" % [ckey.capitalize(), creatures[ckey]["strength"]])
	print("b) %s" % [creatures.keys()[0]])
	print("c) %s" % [creatures[ckey].values()[0]])
	print("d) %s" % [creatures[ckey].keys()])
	print("e) %s" % [creatures[ckey].values()])
	print("f) %s" % [creatures[ckey].keys()[0]])
	print("g) %s" % [creatures.values()[0]])
	print("h) goblin strength is %d" % [creatures[ckey]["strength"]])
	print("i) %s strength is %d" % [ckey, creatures[ckey]["strength"]])
	print("j) %s %s is %d" % [ckey, cvalue, creatures[ckey][cvalue]])
	print("k) %s %s is %d" % [creatures.keys()[0], creatures[ckey].keys()[0], creatures[ckey].values()[0]])
```

The above code will print:


```
["human", "orc", "goblin", "adivia", "agoiru"]
Goblin stats are:
strength : 5.0
intelligence : 5.0
dexterity : 7.0
endurance : 7.0
health : 10.0
sprite_sheet : demon1.png
vframes : 9.0
hframes : 8.0
frame : 1.0

Goblin
Strength: 5
Intelligence: 5
Dexterity: 7
Endurance: 7
Health: 10

a) Goblin strength is 5
b) human
c) 5.0
d) ["strength", "intelligence", "dexterity", "endurance", "health", "sprite_sheet", "vframes", "hframes", "frame"]
e) [5.0, 5.0, 7.0, 7.0, 10.0, "demon1.png", 9.0, 8.0, 1.0]
f) strength
g) { "strength": 5.0, "intelligence": 5.0, "dexterity": 5.0, "endurance": 5.0, "health": 10.0, "sprite_sheet": <null>, "vframes": <null>, "hframes": <null>, "frame": <null> }
h) goblin strength is 5
i) goblin strength is 5
j) goblin strength is 5
k) human strength is 5

```

{{% /tab %}}
{{% tab tabName="C#" %}}

```csharp
using Godot;
using System;
using System.Collections.Generic;
using System.IO;
using System.Text.Json;
using System.Linq;

public partial class Test : Node
{
    public override void _Ready()
    {
        var ckey = "goblin";
        var cvalue = "strength";

        // Read JSON file
        string jsonString = File.ReadAllText("creatures.json");

        // Parse JSON into a dictionary
        var creatures = JsonSerializer.Deserialize<Dictionary<string, Dictionary<string, object>>>(jsonString);
        
        // Print results
        GD.Print(string.Join(", ", creatures.Keys));
        foreach (var race in creatures)
        {
            GD.Print(race.Key);
        }

        GD.Print("");
        GD.Print($"{ckey.Capitalize()} stats are:\n");

        // Print attributes of Goblin
        GD.Print("VERSION 1");
        foreach (var attribute in creatures[ckey])
        {
            GD.Print($"{attribute.Key}: {attribute.Value}");        // same result as VERSION 2
        }

        // Print attributes of Goblin
        GD.Print("\nVERSION 2");
        foreach (var attribute in creatures[ckey].Keys)
        {
            GD.Print($"{attribute}: {creatures[ckey][attribute]}"); // same result as VERSION 1
        }

        // // Print a list of all creatures and their attributes
        // GD.Print("\nVERSION 3");
        // foreach (var creature in creatures)
        // {
        //     GD.Print($"Creature: {creature.Key}");
        //     foreach (var attribute in creature.Value)
        //     {
        //         GD.Print($"{attribute.Key}: {attribute.Value}");
        //     }
        //     GD.Print();
        // }

        GD.Print("");
        GD.Print(ckey.Capitalize());
        GD.Print($"Strength: {creatures[ckey][cvalue]}");
        GD.Print($"Intelligence: {creatures[ckey]["intelligence"]}");
        GD.Print($"Dexterity: {creatures[ckey]["dexterity"]}");
        GD.Print($"Endurance: {creatures[ckey]["endurance"]}");
        GD.Print($"Health: {creatures[ckey]["health"]}");
        GD.Print("");
        GD.Print($"a) {ckey.Capitalize()} {cvalue} is {creatures[ckey][cvalue]}");
        GD.Print($"b1) {creatures.Keys.ElementAt(0)}");
        GD.Print($"b2) {creatures.Keys.First()}");
        GD.Print($"c) {creatures[ckey].Values.ElementAt(0)}");
        GD.Print($"d) {string.Join(", ", creatures[ckey].Keys)}");
        GD.Print($"e) {string.Join(", ", creatures[ckey].Values)}");
        GD.Print($"f) {creatures[ckey].Keys.ElementAt(0)}");
        GD.Print($"g1) {string.Join(", ", creatures.Values.ElementAt(0).Select(kvp => $"{kvp.Key}: {kvp.Value}"))}");
        GD.Print($"g2) {JsonSerializer.Serialize(creatures.Values.First())}");
        GD.Print($"h) goblin strength is {creatures[ckey]["strength"]}");
        GD.Print($"i) {ckey} strength is {creatures[ckey]["strength"]}");
        GD.Print($"j) {ckey} {cvalue} is {creatures[ckey][cvalue]}");
        GD.Print($"k) {creatures.Keys.ElementAt(0)} {creatures[ckey].Keys.ElementAt(0)} is {creatures[ckey].Values.ElementAt(0)}");
    }
}
```

The above code will print:

```
human, orc, goblin, adivia, agoiru
human
orc
goblin
adivia
agoiru

Goblin stats are:

VERSION 1
strength: 5
intelligence: 5
dexterity: 7
endurance: 7
health: 10
sprite_sheet: demon1.png
vframes: 9
hframes: 8
frame: 1

VERSION 2
strength: 5
intelligence: 5
dexterity: 7
endurance: 7
health: 10
sprite_sheet: demon1.png
vframes: 9
hframes: 8
frame: 1

Goblin
Strength: 5
Intelligence: 5
Dexterity: 7
Endurance: 7
Health: 10

a) Goblin strength is 5
b1) human
b2) human
c) 5
d) strength, intelligence, dexterity, endurance, health, sprite_sheet, vframes, hframes, frame
e) 5, 5, 7, 7, 10, demon1.png, 9, 8, 1
f) strength
g1) strength: 5, intelligence: 5, dexterity: 5, endurance: 5, health: 10, sprite_sheet: , vframes: , hframes: , frame: 
g2) {"strength":5,"intelligence":5,"dexterity":5,"endurance":5,"health":10,"sprite_sheet":null,"vframes":null,"hframes":null,"frame":null}
h) goblin strength is 5
i) goblin strength is 5
j) goblin strength is 5
k) human strength is 5
```


{{% /tab %}}
{{< /tabs >}}


As you can see from the results, using numbers as indexes is not a good idea. It may work if the json file is always rendered in the same order both for keys and values. But this is not always the case. In the example above we are trying to get *Goblin* related data. However in lines **b)**, **g)** and **k)** *Human* related data is fetched.

Assigning the name `goblin` to the variable `ckey` is the safest way to proceed with keys. Again, it will not guarantee the correct data if the values are indexed by numbers. In the example above the numbered index used is `[0]`. If you are using C#, the index is `ElementsAt(0)` or `First()`.

However, if we assign the name `strength` to the variable `cvalue`, we can safely index the value from the json file, regardless if it changes the key/value orders when rendering the file. You can see a fine example comparing lines **h)**, **i)** and **j**. They yield the same result, but the line **j)** in the code is cleaner and it's safe.


### Instantiating a NPC at runtime via JSON

[creatures.json](https://drive.google.com/file/d/1pqJw1z3rW2_9pZzKRPQUmhrX_wpwNScq/view?usp=drive_link)

You can create instances dynamically at runtime using a JSON file as data source.

{{< tabs tabTotal="2">}}
{{% tab tabName="GDScript" %}}

##### `npc.gd`

```gdscript
extends CharacterBody2D

var attributes: Dictionary = { 
	"strength" : 0, 
	"intelligence" : 0, 
	"dexterity" : 0, 
	"endurance" : 0, 
	"health" : 0, 
	"race_name" : ""
	}
```
<br>

##### `somenode.gd`

```gdscript

extends Node

func get_creatures_data() -> Dictionary:
	var file = FileAccess.open("res://Data/creatures.json", FileAccess.READ)
	var json = JSON.parse_string(file.get_as_text())
	file.close()
	return json


func _ready() -> void:
	var creatures: Dictionary = get_creatures_data()
	var npc = preload("res://npc.tscn").instantiate()

	for attribute in creatures["goblin"]:
		npc.attributes[attribute] = creatures["goblin"][attribute]

	for key in npc.attributes:
		print(key, ": ", npc.attributes[key])

	add_child(npc)
	npc.transform = Transform2D(0, Vector2(600, 300))
```

{{% /tab %}}
{{% tab tabName="C#" %}}

##### `NPC.cs`
```csharp
using Godot;
using System.Collections.Generic;

public partial class NPC : CharacterBody2D
{
    Dictionary<string, object> _attributes = new Dictionary<string, object>();

    public Dictionary<string, object> Attributes
    {
        get => _attributes;
        set => _attributes = value ?? new Dictionary<string, object>();
    }
}
```

##### `Someone.cs`
```csharp
using Godot;
using System.Collections.Generic;
using System.IO;
using System.Text.Json;

public partial class SomeOne : Node
{
    public PackedScene NPCScene = (PackedScene)ResourceLoader.Load("res://npc.tscn");

    public override void _Ready()
    {
        var ckey = "goblin";
		var npc = (CharacterBody2D)NPCScene.Instantiate() as NPC;

        // Read JSON file
        string jsonString = File.ReadAllText("creatures.json");

        // Parse JSON into a dictionary
        var creatures = JsonSerializer.Deserialize<Dictionary<string, Dictionary<string, object>>>(jsonString);
        
        // Generate npc dictionary with attributes and values from json file
        foreach (var attribute in creatures[ckey])
        {
            npc.Attributes[attribute.Key] = attribute.Value;
        }
        
        // Print npc instance dictionary
        foreach (var attribute in npc.Attributes)
        {
            GD.Print($"{attribute.Key}: {npc.Attributes[attribute.Key]}");
        }

        AddChild(npc);
        npc.Position = new Vector2(100, 100);
    }
}
```



{{% /tab %}}
{{< /tabs >}}


If you want to instantiate random NPCs:

{{< tabs tabTotal="2">}}
{{% tab tabName="GDScript" %}}

```gdscript

func get_creatures_data() -> Dictionary:
	var file = FileAccess.open("res://Data/creatures.json", FileAccess.READ)
	var json = JSON.parse_string(file.get_as_text())
	file.close()
	return json


func _ready() -> void:
	var creatures: Dictionary = get_creatures_data()
	var npc = preload("res://npc.tscn").instantiate()
	
	randomize()
	var a = randi() % creatures.size()

	for attribute in creatures[creatures.keys()[a]]:
		npc.attributes[attribute] = creatures[creatures.keys()[a]][attribute]

	for key in npc.attributes:
		print(key, ": ", npc.attributes[key])

	add_child(npc)
	npc.transform = Transform2D(0, Vector2(600, 300))
```
{{% /tab %}}
{{% tab tabName="C#" %}}

```csharp
using Godot;
using System.Collections.Generic;
using System.IO;
using System.Text.Json;

public partial class SomeOne : Node
{
    public PackedScene NPCScene = (PackedScene)ResourceLoader.Load("res://npc.tscn");

    public override void _Ready()
    {
		var npc = (CharacterBody2D)NPCScene.Instantiate() as NPC;

        // Read JSON file
        string jsonString = File.ReadAllText("creatures.json");

        // Parse JSON into a dictionary
        var creatures = JsonSerializer.Deserialize<Dictionary<string, Dictionary<string, object>>>(jsonString);
        
        // Get a random key from the creatures dictionary
        var keys = new List<string>(creatures.Keys);
        var random = new RandomNumberGenerator();
        random.Randomize(); // Ensure randomization is seeded properly
        var ckey = keys[random.RandiRange(0, keys.Count - 1)];

        // Generate npc dictionary with attributes and values from json file
        foreach (var attribute in creatures[ckey])
        {
            npc.Attributes[attribute.Key] = attribute.Value;
        }
        
        // Print npc instance dictionary
        foreach (var attribute in npc.Attributes)
        {
            GD.Print($"{attribute.Key}: {npc.Attributes[attribute.Key]}");
        }

        AddChild(npc);
        npc.Position = new Vector2(100, 100);
    }
}
```


{{% /tab %}}
{{< /tabs >}}

### CSV to JSON

As you can see, it is possible to use a JSON file to load game data. However, creating game data directly in a JSON file is cumbersome and time consuming.

It is much better to create the game data in a spreadsheet, export it as .csv file and convert it to .json.

In order to convert a .csv file to .json file, you can download these Python and Perl scripts I made from my Google Drive:

- [csv2json.py](https://drive.google.com/file/d/1r3dX10uMR1ZL-4USXzSz7h0Pr0-xi7Uz/view)
- [csv2json.pl](https://drive.google.com/file/d/1tDPkofgMqbHjJzLIjUwQtkvq39rQdLtj/view)

Also, you can download a .csv file as an example: [godot.csv](https://drive.google.com/file/d/1zb45BjCNyuNjUCUiBO62_f3GcG-qcu3U/view?usp=drive_link)

In order to convert `godot.csv` to a .json file, run one of the following commands. Note that you must run the command in the same directory where the scripts and the `godot.csv` are.

```
$ python csv2json.py
$ perl csv2json.pl
```

After running one of the commands, there will be a new JSON file created called `output.json`. You can rename it if you want. It's ready to use in Godot.

Alternatively, you can convert a .csv file to .json file using this web page: [https://csvjson.com/csv2json](https://csvjson.com/csv2json). Be sure to select **Hash** instead of the default **Array** in the **Output** section.

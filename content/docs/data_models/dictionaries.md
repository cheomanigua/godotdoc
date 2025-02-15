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


```gdscript
var inventory {
    0 : ["Silver", 23], 
    1 : ["Gold", 56],
    2 : ["Ruby", 8]
}

func _ready():

    ## loop through the dictionary keys to match the value to be "Gold" via the first element of the array

    for key in inventory:
        if inventory[key][0] == "Gold": 
            print ("There is Gold!!!!")

    ## other interesting stuff
    var i = 1
    print(inventory.keys().[i] ## will print 1
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
        print("%s: %s" % [inventory[key][0],inventory[key][1]])
```

### 2. Dictionary within dictionary

Multi-dimentional dictionaries a.k.a. nested dictionaries

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
	print(urbes)
```

It will print something like this (manually formatted for easy reading):

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

Example file: [test.json](https://drive.google.com/file/d/1lkMs1Yh7TzhiIBZON0oo9a3gSrr7PFbf/view)

1. JSON file is downloaded to Godot project at location res://Data/test.json
2. A Global singleton script in created:

Given the [test.json](https://drive.google.com/file/d/1lkMs1Yh7TzhiIBZON0oo9a3gSrr7PFbf/view) file, we write the following code:

```gdscript
extends Node2D

func get_creatures_data() -> Dictionary:
	var file = FileAccess.open("res://Data/test.json", FileAccess.READ)
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
["human", "adivia", "goblin", "agoiru", "orc"]
Goblin stats are:
dexterity : 7
strength : 5
intelligence : 5
endurance : 7
health : 10

Goblin
Strength: 5
Intelligence: 5
Dexterity: 7
Endurance: 7
Health: 10

a) Goblin strength is 5
b) human
c) 7
d) ["dexterity", "strength", "intelligence", "endurance", "health"]
e) [7, 5, 5, 7, 10]
f) dexterity
g) { "intelligence": 5, "dexterity": 5, "strength": 5, "endurance": 5, "health": 10 }
h) goblin strength is 5
i) goblin strength is 5
j) goblin strength is 5
k) human dexterity is 7
```

As you can see from the results, using numbers as indexes is not a good idea. It may work if the json file is always rendered in the same order both for keys and values. But this is not always the case. In the example above we are trying to get *Goblin* related data. However in lines **b)**, **g)** and **k)** *Human* related data is fetched.

Assigning the name `goblin` to the variable `ckey` is the safest way to proceed with keys. Again, it will not guarantee the correct data if the values are indexed by numbers. In the example above the numbered index used is `[0]`.

However, if we assign the name `strength` to the variable `cvalue`, we can safely index the value from the json file, regardless if it changes the key/value orders when rendering the file. You can see a fine example comparing lines **h)**, **i)** and **j**. They yield the same result, but the line **j)** in the code is cleaner and it's safe.


### Instantiating a NPC at runtime via JSON

[creatures.json](https://drive.google.com/file/d/1pqJw1z3rW2_9pZzKRPQUmhrX_wpwNScq/view?usp=drive_link)

You can create instances dynamically at runtime using a JSON file as data source.

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

If you want to instantiate random NPCs:

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

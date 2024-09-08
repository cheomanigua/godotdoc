---
weight: 500
title: "Dictionaries"
description: ""
icon: "article"
date: "2024-08-24T14:59:45+02:00"
lastmod: "22024-08-24T14:59:45+02:00"
draft: false
toc: true
---
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

Assignments:
- `inventory["coin"] = 2` will change the value of **coin** from **5** to **2**
- `inventory.coin = 2` is the same as above

Math:
- `inventory["coin"] += 2` will increment the value of **coin** by **2**
- `inventory["coin"] -= 2` will decrease the value of **coin** by **2**

Creating:
- `inventory["potion"] = 3` will create the key **potion** with the value **3**
- `inventory.potion = 3` is the same as above

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

### Array within dictionary
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

### Dictionary Management
You can manage a dynamic dictionary by automatically allocating a new key or updating the value of a key:

#### Player.gd

```gdscript
var inventory = {}

func add_item(item):
	if inventory.has(item):
		var temp = inventory[item]
		temp += 1
		inventory[item] = temp
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

### Loading JSON files
Example file: [https://drive.google.com/file/d/1EngStPfZxbTGDxjVKbe7Zql3kBepNaq3/view](https://drive.google.com/file/d/1EngStPfZxbTGDxjVKbe7Zql3kBepNaq3/view)

1. JSON file is downloaded to Godot project at location res://Data/Creatures.json
2. A Global singleton script in created:

```gdscript
extends Node

var creatures:Dictionary = {}

func get_creatures_data() -> Dictionary:
	var file = FileAccess.open("res://Data/creatures.json", FileAccess.READ)
	var json = JSON.parse_string(file.get_as_text())
	file.close()
	return json

func _ready():
	creatures = get_creatures_data()
	# Testing
	print (creatures.keys())
	var type = "Goblin"
	print ("%s stats are:" % [type])
	for key in creatures[type]:
		print ("%s : %s" % [key, creatures[type][key]])
	print("%s strength is %d" % [type, creatures[type]["strength"]])

```
The above code will print:

```
["Player", "Human", "Orc", "Goblin", "Adivía", "Agoiru"]
Goblin stats are:
strength : 5
intelligence : 5
dexterity : 7
endurance : 5
health : 10
sprite_sheet : demon1.png
vframes : 9
hframes : 8
frame : 1
Goblin strength is 5
```

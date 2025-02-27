---
weight: 3400
title: "Classes"
description: "Godot approach to OOP"
icon: "article"
date: "2025-02-03T08:36:26+02:00"
lastmod: "2025-02-03T08:36:26+02:00"
draft: false
toc: true
---

While Godot uses a more modular approach to game design, it can also use Object Oriented Programming approach by the use of Classes.

{{< alert context="info" text="In this article we explain how to use clases in the context of data models. Classes has more features than just being a data model solution." />}}

For reference on this article:

- A variable defined in a class is called property.
- A function defined in a class is called method.


# Declaration/Definition

## Approach

There are are two approaches for defining classes in Godot:

- Inner classes are defined and generally used in the same script.
- Non-inner classes are defined in their own separate script and act as Godot **Objects**, and hence, can be used by other scripts.

### Inner class

`myscript.gd`

```gdscript
extends Node

class City:
	var name: String
	var population: int

func _ready():
	var osgiliath = City.new()
	osgiliath.name = "Osgiliath"
	osgiliath.population = 5000
	print("The city of %s has a population of %d" % [osgiliath.name, osgiliath.population])
````

### Non-inner class

`City.gd`

```gdscript
class_name City
extends Node

var name: String
var population: int
```

`myscript.gd`

```gdscript
extends Node

func _ready():
	var osgiliath: City = City.new()
	osgiliath.name = "Osgiliath"
	osgiliath.population = 5000
	print("The city of %s has a population of %d" % [osgiliath.name, osgiliath.population])
```

As you can see, creating an instance of a class is exactly the same regardless if the class is defined using the inner class or the non-inner class approach.

In this article we'll only show the class definition, it doesn't matter the approach. For real full implementation of classes, you can check the [Economy](/docs/recipes/economy) article.

## Constructor

Class constructors facilitates the creation of instances:

```gdscript
class City:
	var name: String
	var population: int
	func _init(_name: String, _population: int)
		name = _name
		population = _name

func _ready():
	var osgiliath:City = City.new("Osgiliath", 5000)
	print("The city of %s has a population of %d" % [osgiliath.name, osgiliath.population])
```

# Batch instantiation

We could create several instances of a class at once by feeding the class properties with external data and storing the new created instances in an array:

```gdscript
var externaldata: Dictionary = { "Osgiliath": 5000, "Edoras": 4000, "Bree": 2000 }
var cities: Array = []

func _ready():
	for i in externaldata.size():
		var city: City = City.new()
		city.name = externaldata.keys()[i]
		city.population = externaldata.values()[i]
		cities.append(city)

	# Printing the information of all cities stored in the array
	for city in cities:
		print("The city of %s has a population of %d" % [city.name, city.population])

	# Updating and printing the information of a specific city stored in the array
	for city in cities:
		if city.name == "Osgiliath":
			city.population += 1000
			print("The city of %s has a population of %d" % [city.name, city.population])
```

# Classes vs Dictionaries

As you could see at [Batch instantiation](#batch-instantiation), classes and dictionaries can hold the same data. At this point one could use either a Class or a Dictionary for data model.

However, classes can also implement functionality via methods, that is, create methods to operate with the data (properties) of the class. You can keep the data and functionality compacted inside a class:

```gdscript
class City:
	var name: String
	var population: int
	func _init(_name: String, _population: int)
		name = _name
		population = _population
	func increase_population(quantity: int)
		population += quantity

func _ready():
	var osgiliath = City.new("Osgiliath", 5000)
	print("The city of %s has a population of %d" % [osgiliath.name, osgiliath.population]) # 5000
	osgiliath.increase_population(1000)
	print("The city of %s has a population of %d" % [osgiliath.name, osgiliath.population]) # 6000
```

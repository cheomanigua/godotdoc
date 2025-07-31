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

{{< alert context="info" text="In this article we explain how to use clases in the context of data models. However, Classes has more features than just being a data model solution and are better suited for behavior and logic. See below." />}}

{{< alert context="info" text="Use **Classes** for defining behavior and logic, typically tied to nodes or game objects. Use **Resources** for reusable, serializable data that can be shared across multiple objects." />}}

For reference on this article:

- A private variable defined in a class is called property.
- A public variable defined in a class is called field.
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

{{< tabs tabTotal="2">}}
{{% tab tabName="GDScript" %}}

`City.gd`

```gdscript

class_name City

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

{{% /tab %}}
{{% tab tabName="C#" %}}

`City.cs`

```csharp

using Godot;
[GlobalClass]

public partial class City : Node
{
    public string CityName;
    public int Population;
}
```

`MyScript.cs`

```csharp

using Godot;

public partial class MyScript : Node
{
    public override void _Ready()
    {
		var osgiliath = new City();
		osgiliath.CityName = "Osgiliath";
		osgiliath.Population = 500;
		GD.Print($"The city of {osgiliath.CityName} has a population of {osgiliath.Population}");
    }
}
```
{{% /tab %}}
{{< /tabs >}}

As you can see, creating an instance of a class is exactly the same regardless if the class is defined using the inner class or the non-inner class approach.

In this article we'll only show the class definition, it doesn't matter the approach. For real full implementation of classes, you can check the [Economy](/docs/recipes/economy) article.

## Constructor

Class constructors facilitates the creation of instances:

{{< tabs tabTotal="2">}}
{{% tab tabName="GDScript" %}}

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

{{% /tab %}}
{{% tab tabName="C#" %}}

- `City.cs`

```csharp

using Godot;
[GlobalClass]

public partial class City : Node
{
    public string CityName;
    public int Population;

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
        var osgiliath = new City("Osgiliath", 3000);
    }
```

{{% /tab %}}
{{< /tabs >}}

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

# Properties vs Fields

In Godot, when using C# for scripting, you can define classes with or without `get` and `set` properties, and the choice depends on your design needs, encapsulation preferences, and how you intend to interact with the Godot engine. Below, I'll explain the differences between using C# classes with `get`/`set` properties versus classes without them, focusing on their use in Godot.

### Classes with `get`/`set` Properties
In C#, properties with `get` and `set` provide a controlled way to access and modify fields, often used for encapsulation. In Godot, properties are particularly important when you want to expose fields to the Godot editor or other scripts while maintaining control over how they are accessed or modified.

#### Characteristics
1. **Encapsulation**: Properties allow you to control access to a field, adding logic in the `get` or `set` methods (e.g., validation, notifications).
2. **Godot Editor Integration**: To expose a property to the Godot editor (e.g., for tweaking values in the Inspector), you use the `[Export]` attribute with a property. Godot requires properties (not fields) for this.
3. **Change Notifications**: You can use `NotifyPropertyChanged` or Godot's `Notify` methods to signal changes, which is useful for multiplayer or UI updates.
4. **Flexibility**: You can make properties read-only (`get` only) or write-only (`set` only), or add custom logic.

#### Example: Class with `get`/`set`

{{< tabs tabTotal="3">}}
{{% tab tabName="GDScript" %}}

```gdscript

extends Node2D
class_name Player

var _health: int = 100

@export var health: int:
	get:
		return _health
	set(value):
		_health = clampi(value, 0, 100) # Ensure health stays between 0 and 100
		print("Health updated to: ", _health)

func _ready() -> void:
	health = 50 # Uses the setter
	print(health) # Uses the getter
```
{{% /tab %}}
{{% tab tabName="GDScript Signal" %}}

```gdscript

extends Node2D
class_name Player

signal health_changed(new_health)

var _health: int = 100

@export var health: int:
	get:
		return _health
	set(value):
		_health = clampi(value, 0, 100) # Ensure health stays between 0 and 100
		health_changed.emit(_health)
```
{{% /tab %}}
{{% tab tabName="C#" %}}


```csharp
using Godot;

public partial class Player : Node2D
{
    private int _health = 100;

    [Export]
    public int Health
    {
        get => _health;
        set
        {
            _health = Mathf.Clamp(value, 0, 100); // Ensure health stays between 0 and 100
            GD.Print($"Health updated to: {_health}");
        }
    }

    public override void _Ready()
    {
        Health = 50; // Uses the setter
        GD.Print(Health); // Uses the getter
    }
}
```
{{% /tab %}}
{{< /tabs >}}

- **Explanation**:
  - The `Health` property is exposed to the Godot editor via `[Export]`.
  - The setter clamps the value to ensure it stays within a valid range.
  - The getter simply returns the private `_health` field.
  - This approach is ideal for variables you want to tweak in the editor or need logic when setting values.

#### When to Use
- When you need to expose variables to the Godot Inspector.
- When you want to add logic (e.g., validation, events) when getting or setting values.
- When integrating with Godot's systems like signals or multiplayer synchronization.

### Classes without `get`/`set` (Using Public Fields)
You can use public fields instead of properties when you don't need encapsulation or editor exposure. This is simpler but less flexible.

#### Characteristics
1. **Simplicity**: Public fields are straightforward and require less code.
2. **No Editor Exposure**: Fields cannot be marked with `[Export]`, so they won't appear in the Godot Inspector.
3. **No Logic**: Fields don't allow custom logic when accessed or modified.
4. **Direct Access**: Other scripts can read/write the field directly, which may lead to unintended modifications.

#### Example: Class without `get`/`set`

{{< tabs tabTotal="2">}}
{{% tab tabName="GDScript" %}}

```gdscript

extends Node2D
class_name Player

@export var health: int = 100

func _ready() -> void:
	health = 50 # Direct access
	print(health)
```
{{% /tab %}}
{{% tab tabName="C#" %}}

```csharp
using Godot;

public partial class Player : Node2D
{
    public int Health = 100;

    public override void _Ready()
    {
        Health = 50; // Direct access
        GD.Print(Health);
    }
}
```
{{% /tab %}}
{{< /tabs >}}

- **Explanation**:
  - `Health` is a public field, directly accessible by other scripts or code.
  - It cannot be exposed to the Godot editor or have validation logic.
  - This is simpler but lacks control over how `Health` is modified.

#### When to Use
- For internal data that doesn't need editor exposure or custom logic.
- When simplicity is preferred, and you don't mind direct access to fields.
- For quick prototyping where encapsulation isn't a priority.

### Key Differences
| Feature                     | With `get`/`set` (Properties)                     | Without `get`/`set` (Fields)               |
|-----------------------------|--------------------------------------------------|--------------------------------------------|
| **Encapsulation**           | Controlled access with logic in getters/setters   | Direct access, no control                  |
| **Godot Editor Exposure**   | Can use `[Export]` to show in Inspector           | Cannot be exported                        |
| **Custom Logic**            | Can add validation, events, or notifications      | No logic possible                          |
| **Code Complexity**         | More verbose (requires property definition)       | Simpler (just a field)                    |
| **Flexibility**             | Can be read-only, write-only, or computed         | Always read-write (unless `readonly`)      |
| **Use in Godot Systems**    | Works with signals, multiplayer, serialization    | Limited integration with Godot features    |

### Best Practices in Godot
1. **Use Properties (`get`/`set`) When**:
   - You need to expose variables to the Godot editor.
   - You want to add logic (e.g., clamping, logging, or triggering signals) when values change.
   - You're working with Godot's multiplayer or serialization systems, which often expect properties.
   - Example: Player stats (health, speed) that need editor tweaking or validation.

2. **Use Fields When**:
   - The data is internal to the class and doesn't need editor exposure.
   - You want minimal code for quick prototyping.
   - Example: Temporary variables or counters used only within the class.

3. **Hybrid Approach**:
   - You can mix properties and fields in the same class. Use properties for editor-exposed or controlled data and fields for simple, internal data.

{{< tabs tabTotal="2">}}
{{% tab tabName="GDScript" %}}

   ```gdscript

    extends Node2D
    class_name Player

    var temporary_counter: int = 0 # Internal field

    var _score: int = 0
    @export var score: int:
        get:
 	        return _score
        set(value):
 	         _score = max(0, value) # Ensure score isn't negative
   ```
{{% /tab %}}
{{% tab tabName="C#" %}}

   ```csharp
   using Godot;

   public partial class Player : Node2D
   {
       public int TemporaryCounter = 0; // Internal field

       private int _score;
       [Export]
       public int Score
       {
           get => _score;
           set => _score = Mathf.Max(0, value); // Ensure score isn't negative
       }
   }
   ```
{{% /tab %}}
{{< /tabs >}}

4. **Godot-Specific Considerations**:
   - If you use `[Export]`, you must use properties, as fields are not supported.
   - For multiplayer, properties with `NotifyPropertyChanged` or Godot's `Rpc` methods are preferred for synchronization.
   - Avoid overusing public fields to prevent unintended modifications from other scripts.

### Conclusion
- **Properties with `get`/`set`** are the preferred approach in Godot for most cases, especially when you need editor integration, encapsulation, or custom logic. They align well with Godot's workflows and provide flexibility.
- **Fields without `get`/`set`** are suitable for simple, internal data or rapid prototyping but lack the control and integration features of properties.

If you have a specific use case or need an example tailored to a particular Godot feature (e.g., signals, multiplayer), let me know!

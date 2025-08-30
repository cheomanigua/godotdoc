---
weight: 2100
title: "Basics"
description: ""
icon: "article"
date: "2025-03-08T11:03:49+01:00"
lastmod: "2025-03-08T11:03:49+01:00"
draft: false
toc: true
---


### _init()


`_init()` is the GDScript class constructor. The equivalent in **C#** is a **C#** class constructor:

{{< tabs tabTotal="2">}}
{{% tab tabName="GDScript" %}}

```gdscript

class_name Character

var race_name: String
var strength: int

# Constructor
func _init(race_name: String, strength: int):
    self.race_name = race_name
    self.strength = strength
```
{{% /tab %}}
{{% tab tabName="C#" %}}

```csharp
public class Character
{
    private string raceName;
    private int strength;

    // Constructor
    public Character(string raceName, int strength)
    {
        this.raceName = raceName;
        this.strength = strength;
    }
}
```
{{% /tab %}}
{{< /tabs >}}


### @onready

There is no `@onready` annotation in **C#**. The equivalent in **C#** is to declare the variable as class member and define it in the `_Ready()` function:

```csharp
public partial class Player : RigidBody2D
{
    Label Stats;

    public override void _Ready()
    {
        Stats = GetNode<Label>("Stats");
    }
}
```


### Printing

```csharp
GD.Print("The " + Race.Name + " has a health of " + Race.Health);
GD.Print("The {0} has a health of {1}", Race.Name, Race.Health);
GD.Print($"The {Race.Name} has a health of {Race.Health}");
```

### Loading Scenes and Resources

```csharp
    // Load scene NPC
    public PackedScene NPCScene = (PackedScene)ResourceLoader.Load("res://npc.tscn");   // Remember to attach NPC.cs to npc.tscn

    public override void _Ready()
    {
        // Instantiate scene NPC
        var npc = NPCScene.Instantiate() as NPC;

        // Load goblin.tres resource into NPC's NpcRace property via Race's Resource class 
        npc.NpcRace = ResourceLoader.Load("res://resources/csharp/goblin.tres") as Race;

        npc.Transform = new Transform2D(0f, new Vector2(100, 100));
        GD.Print(npc.race.RaceName)     // Not so good option
        npc.PrintName()                 // Better option
        AddChild(npcInstance);
    }
```

### JSON Serialization

```csharp
// Read JSON file
string jsonString = File.ReadAllText("creatures.json");

// Parse JSON into a dictionary
var creatures = JsonSerializer.Deserialize<Dictionary<string, Dictionary<string, object>>>(jsonString);
```

### Classes vs Resources

- **Classes**: Use for **behavior-driven** entities or systems (e.g., players, enemies, quest managers) that require logic, processing, or scene tree integration.
- **Resources**: Use for **data-driven** elements (e.g., items, abilities, quests) that need to be stored, shared, or edited in the Godot editor.
- **Combine Them**: Most RPGs use classes to process resources (e.g., a `Player` class uses `Item` resources for inventory).

By leveraging both, you can create a flexible, data-driven RPG architecture that’s easy to extend and maintain. For example, define item data as resources for easy tweaking, and use classes to implement gameplay logic like combat or quest progression.


#### Practical Example in an RPG
Let’s say you’re building an RPG with players, enemies, items, and abilities:

- **Player and Enemy (Classes)**:
  - Create a `Player` class (extending `CharacterBody2D`) for movement, input, and combat logic.
  - Create an `Enemy` class for AI and behavior.
  - Both might inherit from a `Character` base class for shared health and movement logic.

</br>

- **Items and Abilities (Resources)**:
  - Create an `Item` resource for weapons, potions, etc., with properties like `name`, `value`, and `icon`.
  - Create an `Ability` resource for skills like “Fireball” or “Heal,” with properties like `damage` and `cooldown`.
  - Store these in an `Inventory` (class) or `AbilitySystem` (class) that processes the data.

</br>

- **Quest System**:
  - Use a `Quest` resource to store quest data (e.g., title, objectives, rewards).
  - Use a `QuestManager` class to track progress, update quests, and trigger events.

</br>

- **Saving/Loading**:
  - Resources are ideal for saving game state (e.g., player inventory as an array of `Item` resources) because they can be serialized easily.
  - Classes handle the logic of saving/loading (e.g., a `SaveManager` class that writes resources to disk).

---
weight: 2300
title: "Dictionaries"
description: "How to create and use native C# dictionaries"
icon: "article"
date: "2025-03-07T23:35:38+01:00"
lastmod: "2025-03-07T23:35:38+01:00"
draft: false
toc: true
---

## Dictionary creation

There are to ways to create a dictionary:

```csharp
Dictionary<string, int> attributes = new Dictionary<string, int>
{
	{"strength", 7},
	{"intelligence", 8},
	{"dexterity", 5}
};
```
or

```csharp
Dictionary<string, int> attributes = new Dictionary<string, int>()
attributes.Add("strength", 7);
attributes.Add("intelligence", 8);
attributes.Add("dexterity", 5);
```

## New Entry

```csharp
attributes.Add("wisdom", 10);
```

## Entry Update

```csharp
// Direct update
attributes["wisdom"] = 8;                   // wisdom == 8

// Update via variable
int wisdom = (int)attributes["wisdom"];
wisdom += 4;
attributes["wisdom"] = wisdom;              // wisdom == 12
```

## Example

```csharp
using System.Collections.Generic;

public partial class Player : RigidBody2D
{
    // Declare a new dictionary
    Dictionary<string, int> attributes;

    public override void _Ready()
    {
        // Define a declared dictionary
        attributes = new Dictionary<string, int>
        {
            {"strength", 7},
            {"intelligence", 8},
            {"dexterity", 5}
        };

        // Add new entry
        attributes.Add("endurance", 6);

        // Update an entry
        attributes["strength"] = 8;

        // Update an entry
        int strength = (int)attributes["strength"];
        strength += 2;
        attributes["strength"] = strength;



        // Dictionary iteration

        foreach (var attrib in attributes)
        {
            Godot.GD.Print(attrib.Key, ": ", attrib.Value);
        }

        foreach (var attrib in attributes)
        {
            Godot.GD.Print($"{attrib.Key}: {attrib.Value}");
        }

        foreach (KeyValuePair<string, int> attrib in attributes)
        {
            Godot.GD.Print(attrib.Key, ": ", attrib.Value);
        }

        foreach (var key in attributes.Keys)
        {
            Godot.GD.Print(key);
        }

        foreach (var val in attributes.Values)
        {
            Godot.GD.Print(val);
        }
    }
}
```

## Object vs Variant

</br>

##### Object (`Dictionary<string, object>`)

```csharp
var city = new Dictionary<string, object>
{
    {"name", "Uruk"},
    {"population", 3000},
    {"growth_rate", 2.5f}
};
```

It is better for most scenarios because of performance, simplicity and flexibility.

- **Performance**: Faster for dictionary creation (0.001–0.002 ms vs. 0.002–0.005 ms) and JSON deserialization (0.005–0.01 ms vs. 0.01–0.02 ms).
- **Simplicity**: No custom `JsonConverter` needed for `System.Text.Json`.
- **Flexibility**: Works seamlessly in C# and can be converted to `Variant` for Godot API calls using `ConvertToVariant`.

</br>
</br>

##### Variant (`Dictionary<string, Variant>`)

```csharp
var city = new Dictionary<string, Variant>
{
    {"name", "Uruk"},
    {"population", 3000},
    {"growth_rate", 2.5f}
};
```

It is better only if:

- You frequently pass the dictionary to Godot’s API (e.g., `Node.Set`, `Node.Call`) and want to avoid conversion overhead.
- You’re comfortable with a custom `JsonConverter` for JSON deserialization or using Godot’s `Json` class (slower but simpler).
- The implicit conversion of .NET types to `Variant` is reliable in your environment.


## JSON Serialization

JSON file: [creatures.json](https://drive.google.com/file/d/1pqJw1z3rW2_9pZzKRPQUmhrX_wpwNScq/view)

```csharp
// Read JSON file
string jsonString = File.ReadAllText("creatures.json");

// Parse JSON into a dictionary
var creatures = JsonSerializer.Deserialize<Dictionary<string, Dictionary<string, object>>>(jsonString);
```

## Iteration and access

Given the above JSON serialization, we can iterate and access particular keys and values:

```csharp

/////  1. ITERATION  /////

// Print primary keys
foreach (var race in creatures)
{
    GD.Print(race.Key);
}

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

// Print a list of all creatures and their attributes
GD.Print("\nVERSION 3");
foreach (var creature in creatures)
{
    GD.Print($"Creature: {creature.Key}");
    foreach (var attribute in creature.Value)
    {
        GD.Print($"{attribute.Key}: {attribute.Value}");
    }
    GD.Print();
}



/////  2. ACCESS PARTICULAR KEYS AND VALUES  /////

var ckey = "goblin"
var cvalue = "strength"

// Accessing list of primary keys
GD.Print(string.Join(", ", creatures.Keys));                // agoiru, orc, adivia, human, goblin

// Accessing list of primary values
GD.Print(string.Join(", ", creatures[ckey]));               // [race_name, goblin], [strength, 5], [dexterity, 7], etc

// Accessing list of secondary keys
GD.Print($"{string.Join(", ", creatures[ckey].Keys)}");     // race_name, strength, dexterity, etc

// Accessing list of secondary values
GD.Print($"{string.Join(", ", creatures[ckey].Values)}");   // goblin, 5, 7, etc

// Accessing strength
GD.Print($"{ckey} {cvalue} is {creatures[ckey][cvalue]}");          // 5
GD.Print($"{ckey} strength is {creatures[ckey]["strength"]}");      // 5
GD.Print($"goblin strength is {creatures[ckey]["strength"]}");      // 5



```

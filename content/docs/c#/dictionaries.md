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

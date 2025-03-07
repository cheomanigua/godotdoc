---
weight: 2100
title: "GDScript from C#"
description: "How to use C# in Godot"
icon: "article"
date: "2025-03-06T11:24:17+02:00"
lastmod: "2025-03-06T11:24:17+02:00"
draft: false
toc: true
---

## C#

### 1. _init()

There is no `_init()` function in **C#**. The equivalent in **C#** is the class constructor:

{{< tabs tabTotal="2">}}
{{% tab tabName="GDScript" %}}

```gdscript

func _init() -> void:
	set_pickable(true)
```
{{% /tab %}}
{{% tab tabName="C#" %}}

```csharp
    public MyClass()
    {
        SetPickable(true);
    }
```
{{% /tab %}}
{{< /tabs >}}

### 2. GDSCript from C#

#### 2.1. Instantiating GDScript classes from C# [](https://docs.godotengine.org/en/stable/tutorials/scripting/cross_language_scripting.html#instantiating-gdscript-nodes-from-c)

```csharp
var myGDScript = GD.Load<GDScript>("res://path/to/my_gd_script.gd");
var myGDScriptNode = (GodotObject)myGDScript.New(); // This is a GodotObject, but could be a Resource, or whatever
```
#### 2.2. Accessing GDScript fields from C# [](https://docs.godotengine.org/en/stable/tutorials/scripting/cross_language_scripting.html#accessing-gdscript-fields-from-c)

```csharp
// Set
myGDScriptNode.Set("my_property", "MY GDSCRIPT VALUE");
// Get
GD.Print(myGDScriptNode.Get("my_property"));
```

#### 2.3. Calling GDScript methods from C# [](https://docs.godotengine.org/en/stable/tutorials/scripting/cross_language_scripting.html#calling-gdscript-methods-from-c)

```csharp
myGDScriptNode.Call("print_node_name", this);
```

#### 2.4. Connecting to GDScript signal from C# [](https://docs.godotengine.org/en/stable/tutorials/scripting/cross_language_scripting.html#connecting-to-gdscript-signals-from-c)

No arguments

```csharp
myGDScriptNode.Connect("my_signal", Callable.From(OnMySignal));
```
With argument (Node2D):

```csharp
public override void _Ready()
{
    Area2D radar = GetNode<Area2D>("Radar");
    radar.Connect("player_detected", Callable.From<Node2D>(OnPlayerDetected));
}

private void OnPlayerDetected(Node2D body)
{
    Node2D player = body;
}
```

### 3. Printing

```csharp
GD.Print($"Health: {Race.Health}");    // Print Health
GD.Print("Health: ", Race.Health);     // Print Health
```


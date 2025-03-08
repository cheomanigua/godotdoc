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


### @onready

There is no `@onready` annotation in **C#**. The equivalent in **C#** is to declare the variable as class member and define it in the `onready()` function:

```csharp
public partial class Player : RigidBody2D
{
    Label Stats;

    public override void _Ready()
    {
        Stats = GetNode<Label>("Stats")
    }
}
```


### Printing

```csharp
GD.Print($"Health: {Race.Health}");    // Print Health
GD.Print("Health: ", Race.Health);     // Print Health
```

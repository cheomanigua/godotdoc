---
weight: 2400
title: "Events"
description: "How to use events and delegates"
icon: "article"
date: "2025-03-07T23:00:46+01:00"
lastmod: "2025-03-07T23:00:46+01:00"
draft: false
toc: true
---


## Declaration

There are two types of events: events that return a value and events that don't return a value. They are declared slightly differently:

### Action

Action events don't return a value.

```csharp
public Action<string> MyEvent;
```
is the same as

```csharp
    public delegate void MyEventHandler(string);
    public event MyEventHandler OnMyEvent;
```

### Func

Func events return a value.

```csharp
public Func<int, bool> MyEvent;
```
is the same as

```csharp
    public delegate int MyEventHandler(bool toggle);
    public event MyEventHandler OnMyEvent;
```


## Event manager

We can create an event manager to handle a variety of events. In the example below, we create an event manager to handle attributes changes and event messages.

The attribute to handle is ammunition. When we shoot, we substract one to the total ammo and update a label that shows the ammo available. When we reach 0 ammo, we update another label with the message: "Ammo depleted!".

`EventManager.cs`

```csharp
using System;
using Godot;

public static class EventManager
{
    public static Action<Variant> AttributeChangeEvent;
    public static Action<string> MessageEvent;
    
    public static void BroadcastAttributeChange(Variant attribute)
    {
        AttributeChangeEvent?.Invoke(attribute);
    }

    public static void BroadcastMessage(string message)
    {
        MessageEvent?.Invoke(message);
    }
}
```
<br>

`Stats.cs`

```csharp
using Godot;

public partial class Stats : Label
{
    public override void _Ready()
    {
        EventManager.AttributeChangeEvent += OnAttributeChange;
    }

    private void OnAttributeChange(Variant attribute)
    {
        Text = "";
        foreach (var kvp in Player.attributes)
        {
            Text += $"{kvp.Key}: {kvp.Value:F2}\n";
        }
    }
}
```

<br>

`EventsLabel.cs`

```csharp
using Godot;

public partial class EventsLabel : Label
{
    public override void _Ready()
    {
        EventManager.MessageEvent += OnMessageEVent;
    }

    private void OnMessageEVent(string message)
    {
        Text = message;
    }
    
}
```

<br>

`Player.cs`

```csharp
   void Shoot()
    {
        if (Ammo > 0)
        {
            var new_bullet = Bullet.Instantiate();
            GetParent().AddChild(new_bullet);
            Ammo -= 1;
            EventManager.BroadcastAttributeChange(Ammo);
        }
        else
        {
            EventManager.BroadcastMessage("Ammo depleted!");
        }
    }
```

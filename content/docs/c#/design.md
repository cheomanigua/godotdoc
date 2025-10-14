---
weight: 2500
title: "Design Patterns"
description: "Using GoF's Design Pattern for game development"
icon: "article"
date: "2025-10-13T11:26:17+01:00"
lastmod: "2025-10-13T11:26:17+01:00"
draft: false
toc: true
---

Written programs need to be flexible, easily maintainable, and reusable. A good program has been designed using GoF's design patterns. Design patterns apply for all OOP languages and all platforms.

In the case of indie game development, there are certain patterns that are used very commonly: Singleton, Composite, Factory, Strategy, State, Observer, Object Pool.  

#### Use case

- Object **Ship** can have dynamically added/removed components like: **Radar**, **Shield**, **Weapon**.
- There are different types of **Weapon**
- Each **Weapon** may have different types of **Ammo**
- **Ship** may be dynamically instantiated many times.

Which design patterns to use in such cases?

This is a classic use case for **composition over inheritance**, where entities like your `Ship` dynamically acquire behaviors via components. In **Godot 4 with C#**, you can apply **design patterns** to make this scalable, flexible, and easy to manage. Here’s a breakdown of relevant patterns and concepts, and how they apply:

---

### 1. Component Pattern (Composition over Inheritance)

**Use for:** Dynamically adding/removing behaviors like `Radar`, `Shield`, `Weapon`.

**How it works in Godot:**

* Each "component" (e.g., `Radar`, `Shield`, `Weapon`) is a `Node` or `Node2D` (depending on 2D/3D).
* Attach them as children to the `Ship` at runtime.
* Components should implement a common interface or base class like `IShipComponent`.

**C# Interface Example:**

```csharp
public interface IShipComponent
{
    void Initialize(Ship ship);
    void UpdateComponent(float delta);
}
```

Attach like this:

```csharp
public class Ship : Node2D
{
    private List<IShipComponent> _components = new();

    public void AddComponent(Node component)
    {
        AddChild(component);
        if (component is IShipComponent shipComponent)
        {
            shipComponent.Initialize(this);
            _components.Add(shipComponent);
        }
    }

    public override void _Process(double delta)
    {
        foreach (var component in _components)
        {
            component.UpdateComponent((float)delta);
        }
    }
}
```

---

### 2. Factory Pattern

**Use for:** Creating Weapons, WeaponTypes, AmmoTypes dynamically.

Instead of instantiating directly with `new`, use a factory that abstracts the creation logic based on parameters.

**Example:**

```csharp
public static class WeaponFactory
{
    public static Weapon CreateWeapon(string weaponType)
    {
        switch (weaponType)
        {
            case "Laser":
                return new LaserWeapon();
            case "Missile":
                return new MissileLauncher();
            default:
                throw new ArgumentException("Unknown weapon type");
        }
    }
}
```

You can also extend this by using **Resource-based** configurations in Godot (e.g., load `.tres` files per weapon).

---

### 3. Strategy Pattern

**Use for:** Weapon behavior (e.g., fire logic) or different ammo logic.

This separates the behavior of a weapon from its data.

```csharp
public interface IFireStrategy
{
    void Fire(Ship ship);
}

public class LaserFireStrategy : IFireStrategy
{
    public void Fire(Ship ship)
    {
        // laser firing logic
    }
}

public class Weapon : Node
{
    public IFireStrategy FireStrategy { get; set; }

    public void Fire()
    {
        FireStrategy?.Fire(this.GetParent<Ship>());
    }
}
```

This lets you plug-and-play firing logic per weapon or ammo type.

---

### 4. Prototype Pattern

**Use for:** Cloning ship configurations, or ammo/weapon setups.

If you want to duplicate a setup (e.g., same ship with Radar + Weapon A + Shield B), save that as a "prototype".

In Godot, this can be a `.tscn` scene you instantiate, or a C# object you clone.

---

### 5. Observer/Event Pattern

**Use for:** Communication between components (e.g., Radar detects enemy → Weapon fires).

Avoid tight coupling between components. Use events or signals.

```csharp
public class Radar : Node, IShipComponent
{
    public event Action<Vector3> OnTargetDetected;

    public void Scan()
    {
        // If target found:
        OnTargetDetected?.Invoke(targetPosition);
    }
}
```

Then `Weapon` subscribes to the radar's `OnTargetDetected`.

---

### 6. Service Locator / Dependency Injection (Optional)

Useful if you have shared services like logging, configuration, or object pooling.

---

### Bonus: ECS Alternative (Entity-Component-System)

If you want *more advanced* dynamic behavior, you could adopt an **ECS-like** approach (either manually or using 3rd-party Godot ECS libraries like **Godex**), but it’s often overkill for many games unless you're building something massive or simulation-heavy.

---

### Summary of Patterns to Use

| Pattern            | Use For                                        |
| ------------------ | ---------------------------------------------- |
| **Component**      | Add/remove dynamic behavior like Radar, Shield |
| **Factory**        | Create weapons/ammo types dynamically          |
| **Strategy**       | Vary behavior (like different fire modes)      |
| **Prototype**      | Clone ship configurations                  |
| **Observer/Event** | Decoupled communication between components     |
| (Optional) ECS     | Large-scale, performance-heavy systems         |

---

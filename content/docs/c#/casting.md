---
weight: 2150
title: "Casting"
description: "Different methods to cast in C#"
icon: "article"
date: "2025-03-08T15:24:45+01:00"
lastmod: "2025-03-08T15:24:45+01:00"
draft: false
toc: true
---

```csharp
npc.NpcRace = ResourceLoader.Load("res://resources/csharp/human.tres") as Race;
npc.NpcRace = (Race)ResourceLoader.Load("res://resources/csharp/human.tres");
```

Both code snippets use Godot’s `ResourceLoader.Load` to load a `.tres` file as a `Race` resource, but they differ in how they handle type casting. The choice between one or the other depends on how you want to handle potential casting errors and your coding style preferences. Let’s compare the two approaches to determine which is better in your context.

### Option 1: Using `as` Cast
```csharp
npc.NpcRace = ResourceLoader.Load("res://resources/csharp/human.tres") as Race;
```

#### How It Works
- The `as` operator attempts to cast the result of `ResourceLoader.Load` (which returns a `Resource`) to the `Race` type.
- If the resource cannot be cast to `Race` (e.g., if `human.tres` is not a `Race` resource), the result is `null`.
- This is a *safe cast* because it doesn’t throw an exception on failure.

#### Pros
- **Null Safety**: If the cast fails, `npc.NpcRace` is set to `null`, avoiding a runtime exception.
- **Simpler Error Handling**: You can check for `null` to handle errors gracefully:

  ```csharp
  npc.NpcRace = ResourceLoader.Load("res://resources/csharp/human.tres") as Race;
  if (npc.NpcRace == null)
  {
      GD.PrintErr("Failed to load or cast resource: res://resources/csharp/human.tres");
  }
  ```

- **Readable**: The `as` operator is concise and commonly used in C# for safe casting.

#### Cons
- **Requires Null Check**: If you don’t check for `null`, accessing `npc.NpcRace` later could cause a `NullReferenceException`.
- **Less Explicit**: It might be less clear to other developers that a failed cast is expected or handled.

### Option 2: Using Explicit Cast
```csharp
npc.NpcRace = (Race)ResourceLoader.Load("res://resources/csharp/human.tres");
```

#### How It Works
- The `(Race)` operator performs an *explicit cast* of the `Resource` returned by `ResourceLoader.Load` to the `Race` type.
- If the resource cannot be cast to `Race`, it throws an `InvalidCastException` at runtime.
- This is an *unsafe cast* because it assumes the resource is of the correct type.

#### Pros
- **Explicit Intent**: The explicit cast makes it clear that you expect the resource to be a `Race`, which can improve code readability.
- **No Null Check Needed**: If the cast succeeds, you can use `npc.NpcRace` immediately without checking for `null`.

#### Cons
- **Throws Exception on Failure**: If `human.tres` is not a `Race` resource, an `InvalidCastException` is thrown, which you must catch to avoid crashing:

  ```csharp
  try
  {
      npc.NpcRace = (Race)ResourceLoader.Load("res://resources/csharp/human.tres");
  }
  catch (InvalidCastException ex)
  {
      GD.PrintErr($"Failed to cast resource: {ex.Message}");
  }
  ```

- **More Error-Prone**: Without a `try-catch`, a failed cast will crash your game, which is risky in production.

### Which Is Better?
The choice depends on your priorities for safety, readability, and error handling:

#### Use `as` Cast (Option 1) If:
- You prioritize **safety** and want to avoid exceptions.
- You’re okay with adding a `null` check to handle cases where the resource isn’t a `Race` or fails to load.
- You’re working in a context where the resource type might be uncertain (e.g., during development or if `human.tres` might change).
- **Example Use Case**: Early in development or when loading resources dynamically, where the file might not exist or might not be the expected type.

**Recommended Code**:
```csharp
npc.NpcRace = ResourceLoader.Load("res://resources/csharp/human.tres") as Race;
if (npc.NpcRace == null)
{
    GD.PrintErr("Failed to load or cast resource: res://resources/csharp/human.tres");
}
```

#### Use Explicit Cast (Option 2) If:
- You’re **certain** that `human.tres` is a valid `Race` resource (e.g., it’s been verified in your project setup).
- You prefer to handle errors via exceptions and want to fail fast during development or testing.
- You don’t want to add `null` checks and prefer a more concise assignment.
- **Example Use Case**: In a production environment where resources are guaranteed to be correct, and you want to catch errors via exceptions for debugging.

**Recommended Code**:
```csharp
try
{
    npc.NpcRace = (Race)ResourceLoader.Load("res://resources/csharp/human.tres");
}
catch (InvalidCastException ex)
{
    GD.PrintErr($"Failed to cast resource: {ex.Message}");
}
catch (Exception ex)
{
    GD.PrintErr($"Failed to load resource: {ex.Message}");
}
```

### Additional Considerations
- **Godot Resource Type**: Ensure `human.tres` is defined as a `Race` resource in Godot. For example:

  ```tres
  [gd_resource type="Race" load_steps=2 format=3]
  [ext_resource type="Script" path="res://resources/csharp/Race.cs" id="1"]

  [resource]
  script = ExtResource("1")
  name = "Human"
  strength = 10
  agility = 8
  ```

  And your `Race` class should inherit from `Resource`:

  ```csharp
  using Godot;

  public partial class Race : Resource
  {
      [Export]
      public string Name { get; set; }

      [Export]
      public int Strength { get; set; }

      [Export]
      public int Agility { get; set; }
  }
  ```

- **Resource Loading Failure**: Both approaches return `null` or throw an exception if `res://resources/csharp/human.tres` doesn’t exist. Always verify the file exists in your project.
- **Performance**: The performance difference between `as` and explicit casting is negligible, so choose based on error handling and readability.
- **Godot Version**: The code assumes Godot 4.x. In Godot 3.x, `ResourceLoader.Load` works similarly, but confirm your version if you encounter issues.
- **Context from Previous Errors**: Your previous issues with `System.Text.Json` and `FileAccess` suggest `human.tres` might not be JSON. Using `ResourceLoader` is the best approach for `.tres` files, making either casting method appropriate, but `as` is safer.

### Recommendation
**Use the `as` cast (Option 1)** unless you’re absolutely certain `human.tres` is a `Race` resource and want to fail fast with an exception. The `as` cast is safer, more flexible, and aligns with common C# practices for handling potentially null results. It’s especially useful in Godot, where resource loading might fail due to file issues or misconfigured types.

**Final Code**:
```csharp
npc.NpcRace = ResourceLoader.Load("res://resources/csharp/human.tres") as Race;
if (npc.NpcRace == null)
{
    GD.PrintErr("Failed to load or cast resource: res://resources/csharp/human.tres");
}
```

### Next Steps
- Verify that `human.tres` is a valid Godot resource with `type="Race"` or a compatible type.
- If you prefer the explicit cast and want to use it, add a `try-catch` block to handle potential exceptions.
- If you encounter runtime issues (e.g., `null` or exceptions), share the content of `human.tres` or the `Race` class definition for further debugging.
- Confirm your Godot version (3.x or 4.x) if you need specific API adjustments.

Let me know if you need help verifying the resource or handling specific edge cases!

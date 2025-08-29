---
weight: 3600
title: "Classes vs Resources"
description: "When to choose a class or a resource"
icon: "article"
date: "2025-02-03T021:25:18+02:00"
lastmod: "2025-02-03T021:25:18+02:00"
draft: false
toc: true
---

In Godot 4, when developing an RPG game, both **classes** and **resources** have distinct use cases, and choosing between them depends on the specific needs of your game's architecture, data management, and extensibility. Below, I’ll explain when it’s better to use **classes** (such as GDScript classes or nodes) versus **resources** in the context of an RPG, along with their strengths and practical applications.

### Practical Example in an RPG
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

---

### Summary
- **Classes**: Use for **behavior-driven** entities or systems (e.g., players, enemies, quest managers) that require logic, processing, or scene tree integration.
- **Resources**: Use for **data-driven** elements (e.g., items, abilities, quests) that need to be stored, shared, or edited in the Godot editor.
- **Combine Them**: Most RPGs use classes to process resources (e.g., a `Player` class uses `Item` resources for inventory).

By leveraging both, you can create a flexible, data-driven RPG architecture that’s easy to extend and maintain. For example, define item data as resources for easy tweaking, and use classes to implement gameplay logic like combat or quest progression.

If you have a specific RPG system or feature in mind (e.g., inventory, combat, or quests), let me know, and I can provide a more tailored example or code!

### Classes in Godot 4 for RPGs
Classes in Godot typically refer to **GDScript classes** (using the `class_name` keyword or extending nodes like `Node`, `CharacterBody2D`, etc.) or **custom node types**. They are best suited for defining **behavior**, **logic**, or **entities** that have active functionality in your game.

#### When to Use Classes
1. **Defining Game Entities with Behavior**:
   - Classes are ideal for objects that require logic, such as characters, enemies, or interactive objects. For example, a `Player` class (extending `CharacterBody2D`) might handle movement, combat, and inventory management.
   - Example: A `Enemy` class could encapsulate AI behavior, health, and attack patterns.

   ```gdscript
   class_name Enemy extends CharacterBody2D

   var health: int = 100
   var speed: float = 200.0

   func _physics_process(delta: float) -> void:
       # Move towards player
       move_and_slide()
   ```

2. **Managing Complex Systems**:
   - Use classes for systems like a `QuestManager`, `InventorySystem`, or `DialogueSystem` that centralize logic and coordinate multiple components.
   - Example: A `QuestManager` class could track quest progress, trigger events, and update objectives.

3. **Inheritance and Reusability**:
   - Classes are great when you need inheritance to share functionality. For instance, you might have a base `Character` class with shared movement and health logic, extended by `Player` and `Enemy` classes.
   - Example:
     ```gdscript
     class_name Character extends CharacterBody2D
     var health: int = 100
     func take_damage(amount: int) -> void:
         health -= amount
     ```

4. **Scene-Specific Logic**:
   - If a game object is tightly coupled to a scene (e.g., a specific NPC with unique behavior), a class attached to a node in the scene tree is often more intuitive.

#### Advantages of Classes
- Encapsulate behavior and state together.
- Integrate seamlessly with Godot’s node system for scene management.
- Support inheritance for shared logic.
- Ideal for objects that need to process in real-time (e.g., `_process`, `_physics_process`).

#### Limitations
- Classes tied to nodes can become tightly coupled to the scene tree, making them less reusable outside their context.
- Not ideal for purely data-driven systems, as they are more focused on behavior than data storage.

---

### Resources in Godot 4 for RPGs
Resources are lightweight, serializable data containers (extending `Resource`) that are designed to store and share data. They are perfect for **data-driven design**, which is common in RPGs for items, abilities, or configurations.

#### When to Use Resources
1. **Storing Static or Configurable Data**:
   - Resources are ideal for defining data that doesn’t require active behavior, such as items, abilities, or character stats.
   - Example: An `Item` resource could store properties like name, description, and value.
     ```gdscript
     class_name Item extends Resource
     @export var name: String
     @export var description: String
     @export var value: int
     @export var icon: Texture2D
     ```

2. **Inventory and Equipment Systems**:
   - RPGs often have inventories with many items. Resources are perfect for representing items, as they can be instantiated, saved, and loaded easily.
   - Example: An `Inventory` node might store an array of `Item` resources.

3. **Abilities and Skills**:
   - Use resources for ability data, such as damage, cooldown, or effects. A `Player` or `Enemy` class can then reference these resources to execute behavior.
   - Example:
     ```gdscript
     class_name Ability extends Resource
     @export var name: String
     @export var damage: int
     @export var cooldown: float
     ```

4. **Data Sharing Across Scenes**:
   - Resources can be shared between nodes or scenes without duplicating data. For example, a `Weapon` resource can be referenced by multiple characters.
   - Resources are easily saved to disk (e.g., `.tres` or `.res` files) and loaded, making them great for persistent data like player progress or game settings.

5. **Data-Driven Design**:
   - RPGs often rely on data-driven systems (e.g., item databases, skill trees). Resources allow you to define this data in the editor or via scripts, making it easy to tweak without changing code.

#### Advantages of Resources
- Lightweight and focused on data storage.
- Easily serialized and saved/loaded, ideal for game saves or editor workflows.
- Reusable across multiple nodes or scenes without duplication.
- Editable in Godot’s inspector, making them designer-friendly for tweaking values.

#### Limitations
- Resources don’t contain behavior, so you need a separate class or node to process them (e.g., an `Item` resource needs an `Inventory` class to manage it).
- Less suited for complex logic or real-time processing.

---

### Key Considerations for RPGs
RPGs often require a mix of **behavior** (handled by classes) and **data** (handled by resources). Here’s how to decide:

1. **Use Classes When**:
   - You need to define **active entities** (e.g., players, enemies, NPCs) with logic like movement, combat, or AI.
   - You need **system-level logic** (e.g., quest systems, dialogue managers).
   - You want to leverage **inheritance** for shared behavior (e.g., a base `Character` class).
   - Example: A `Player` class that handles input, movement, and combat logic.

2. **Use Resources When**:
   - You need to store **static or configurable data** (e.g., items, abilities, stats).
   - You want **data-driven design** for easy tweaking in the editor (e.g., balancing item stats).
   - You need to **share data** across multiple objects or scenes (e.g., a single `Sword` resource used by multiple characters).
   - You need **save/load functionality** for game data (e.g., inventory, player progress).

3. **Combining Classes and Resources**:
   - In practice, RPGs often combine both. For example:
     - A `Player` class (node-based) might reference an array of `Item` resources for its inventory.
     - An `AbilitySystem` class might process `Ability` resources to execute attacks or spells.
     - Example:
       ```gdscript
       class_name Player extends CharacterBody2D
       @export var inventory: Array[Item]
       @export var abilities: Array[Ability]

       func use_ability(ability: Ability) -> void:
           # Execute ability logic
           print("Using ", ability.name, " dealing ", ability.damage, " damage")
       ```

4. **Performance and Workflow**:
   - **Resources** are more memory-efficient for storing large datasets (e.g., hundreds of items) because they are lightweight and don’t require node overhead.
   - **Classes** (especially node-based) are better for objects that need to interact with the scene tree or process in real-time.

5. **Editor Integration**:
   - Resources are particularly useful in Godot’s editor, as you can create `.tres` files for items, abilities, or quests and edit them visually. This is great for designers or non-programmers.
   - Classes are better for scripting complex logic that doesn’t need editor exposure.

---


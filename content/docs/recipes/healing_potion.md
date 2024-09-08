---
weight: 5100
title: "Healing Potion"
description: "Player touching healing potion and getting healed"
icon: "article"
date: "2024-09-02T18:23:39+02:00"
lastmod: "2024-09-02T18:23:39+02:00"
draft: false
toc: true
---

- The example shows how to trigger a healing potion when player overrun the potion.
- There are two versions:
	1. The healing process occurs in a function within **player.gd**
	2. The healing process occurs within **potion.gd**

### Version 1 

#### player.md

```gdscript
...

@export var health: float = 150:
	set(value):
		health = clamp(value, 0, max_health)
		health_changed.emit(health)

@export var max_health: float = 200
...

func add_health(healing_points: float):
	health += healing_points
	health_bar.value = health

...
```

#### potion.gd

```gdscript
extends Area2D
var healing_points: float = 100

func _ready() -> void:
	body_entered.connect(_on_body_entered)	
	
func _on_body_entered(body):
	if body.has_method("add_health"):
		body.add_health(healing_points)
		queue_free()
```

### Version 2

#### potion.gd

```gdscript
extends Area2D
var healing_points: float = 100

func _ready() -> void:
	body_entered.connect(_on_body_entered)	
	
func _on_body_entered(body):
	if body.name == "Player":
		body.health += healing_points
		body.health_bar.value = body.health
		queue_free()
```


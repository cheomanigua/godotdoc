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

func add_health(heal: float):
	health += heal
	if health > max_health:
		health = max_health
	health_bar.value = health

...
```

#### potion.gd

```gdscript
extends Area2D
var heal_points: float = 100

func _ready() -> void:
	body_entered.connect(_on_body_entered)	
	
func _on_body_entered(body):
	if body.has_method("add_health"):
		body.add_health(heal_points)
		queue_free()
```

### Version 2

#### potion.gd

```gdscript
extends Area2D
var heal_points: float = 100

func _ready() -> void:
	body_entered.connect(_on_body_entered)	
	
func _on_body_entered(body):
	if body.name == "Player":
		body.health += heal_points
		if body.health > body.max_health:
			body.health = body.max_health
		body.health_bar.value = body.health
		queue_free()
```


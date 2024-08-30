---
weight: 600
title: "Collisions"
description: ""
icon: "article"
date: "2024-08-28T16:35:15+02:00"
lastmod: "2024-08-28T16:35:15+02:00"
draft: false
toc: true
---

There are two ways to detect a collision, depending of the type of node you are using:

### 1. PhysicsBody2D
```gdscript
func _physics_process(delta):
	move_and_slide()

	var collision_count = get_slide_collision_count()

	for i in collision_count:
		var collision_info = get_slide_collision(i)
		var collider = collision_info.get_collider()

		if collider.has_method("take_damage"):
			collider.take_damage(impact_damage)
			died.emit()
			queue_free()
```

### 2. Area2D
```gdscript
func _physics_process(delta):
	global_position += Vector2(speed, 0) * delta

func _on_area_2d_body_entered(body):
	if body.is_in_group("enemy"):
		if body.has_method("take_damage"):
			body.take_damage(damage)
			queue_free()
```

### Layers and Masks

- **Collision Layer**: This describes the layers that the object appears **in**.
- **Collision Mask**: This describes what layers the body will **scan** for collisions. If an object isn't in one of the mask layers, the body will ignore it.

[Ref](https://docs.godotengine.org/en/stable/tutorials/physics/physics_introduction.html#collision-layers-and-masks)

**Object A** will collide to **Object B** if **Object A**'s Mask value matches **Object B**'s Layer value.

Example:

| | |
|:-:|:-:|
| **Object A** | **Object B** |
| ![alt](/images/objectA.jpg) | ![alt](/images/objectB.jpg) |
| - Object A belongs to layer 2, and will collide with any object belonging to layers 1, 2 or 3 | - Object B belongs to layer 1, and will collide with any object belonging to layer 1 |
| - If another object has set one of its masks to 2, it will collide with Object A | - If another object has set one of its masks to 1, it will collide with Object B.


### Filtering

```gdscript
func _on_body_entered():
    if body.name == "Player":
    if body.is_in_group("enemy"):
    if body.has_method("player_spotted"):
    if body.variable == "holy"
```

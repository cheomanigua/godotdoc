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

There are three ways to detect a collision, depending of the type of node you are using:

1. **PhysicsBody2D** uses the built in collision feature.
2. **Area2D** uses the built in `body_entered` or `area_entered` signals.
3. **RigidBody2D** uses the built in `body_entered` or `area_entered` signals.

### 1. PhysicsBody2D

Comparison between **move_and_collide** and **move_and_slide** for a bullet object.

- **move_and_collide** is better used for projectiles kind of objects because we asure that the projectile is going to hit only once at the target, causing the expected amount of damage.
- **move_and_slide** is not good for projectiles, because a single projectile can impact several times at the target, causing more damage than expected.

```gdscript
func _physics_process(delta):

	# Using move_and_collide.
	var collision = move_and_collide(velocity * delta)
	if collision:
		queue_free()
		if collision.get_collider().has_method("take_damage"):
			collision.get_collider().take_damage(damage)
			print("Bullet hit target")

	# Using move_and_slide.
	move_and_slide()
	for i in get_slide_collision_count():
		queue_free()
		var collision = get_slide_collision(i)
		if collision.get_collider().has_method("take_damage"):
			collision.get_collider().take_damage(damage)
			print("Bullet hit target")
```

### 2. Area2D
```gdscript
func _ready():
	body_entered().connect(_on_body_entered)

func _physics_process(delta):
	global_position += Vector2(speed, 0) * delta

func _on_body_entered(body):
	if body.is_in_group("enemy"):
		if body.has_method("take_damage"):
			body.take_damage(damage)
			queue_free()
```

### 3. RigidBody2D

In order for **RigidBody2D** to detect a collision, **contact_monitor** and **max_contact_reported** have to be set to an integer bigger than 0.

```gdscript
func _ready():
	contact_monitor = 1
	max_contact_reported = 1
	body_entered().connect(_on_body_entered)

func _on_body_entered(body):
	if body.name == "HeliPad":
		if linear_velocity.y > 0.0002:
			print("Hard landing")
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

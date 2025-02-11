---
weight: 400
title: "Collisions"
description: ""
icon: "article"
date: "2024-08-28T16:35:15+02:00"
lastmod: "2024-08-28T16:35:15+02:00"
draft: false
toc: true
---

## Collision objects

Godot offers four kinds of collision objects which all extend [CollisionObject2D](https://docs.godotengine.org/en/stable/classes/class_collisionobject2d.html#class-collisionobject2d). The last three listed below are physics bodies and additionally extend [PhysicsBody2D](https://docs.godotengine.org/en/stable/classes/class_physicsbody2d.html#class-physicsbody2d).

1. **Area2D** uses the built in `body_entered` or `area_entered` signals.
2. **CharacterBody2D** uses the built in collision feature.
3. **RigidBody2D** uses the built in `body_entered` or `area_entered` signals.
4. **StaticBody2D**

{{< alert context="warning" text="**Area2D** do **NOT** detect moving **StaticBody2D**." />}}

[Godot Documentation](https://docs.godotengine.org/en/stable/tutorials/physics/physics_introduction.html)

### 1. Area2D [](https://docs.godotengine.org/en/stable/classes/class_area2d.html)
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


### 2. CharacterBody2D [](https://docs.godotengine.org/en/stable/classes/class_characterbody2d.html#class-characterbody2d)

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


### 3. RigidBody2D [](https://docs.godotengine.org/en/stable/classes/class_rigidbody2d.html)

In order for **RigidBody2D** to detect a collision, **contact_monitor** must to be set to `true` and **max_contact_reported** have to be set to an integer bigger than 0. If you don't set those parameters, the signal fuctions like `body_entered` won't report a thing.

`max_contact_reported` is the maximum number of different contacts that will be reported.

**Note**: The number of contacts is different from the number of collisions. Collisions between parallel edges will result in two contacts (one at each end), and collisions between parallel faces will result in four contacts (one at each corner).

```gdscript
func _ready():
	contact_monitor = true
	max_contact_reported = 1
	body_entered().connect(_on_body_entered)

func _on_body_entered(body):
	if body.name == "HeliPad":
		if linear_velocity.y > 0.0002:
			print("Hard landing")
```

### 4. StaticBody2D [](https://docs.godotengine.org/en/stable/classes/class_staticbody2d.html)

A 2D physics body that can't be moved by external forces. When moved manually, it doesn't affect other bodies in its path.

A static 2D physics body. It can't be moved by external forces or contacts, but can be moved manually by other means such as code, AnimationMixers, etc.

When StaticBody2D is moved, it is teleported to its new position without affecting other physics bodies in its path. If this is not desired, use [AnimatableBody2D](https://docs.godotengine.org/en/stable/classes/class_animatablebody2d.html) instead.

## Layers and Masks

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


## Accessing data or logic from an object

You can use either of these (Note that these methods are **much** slower than direct references):

```gdscript

func _on_body_entered(body):
	if body.name == "Player":
	if body.is_in_group("enemy"):
	if body.has_method("pickup"):
	if body.has_signal("body_entered"):
	if body.has_user_signal("my_custom_signal"):
	if body.variable == "holy"
	if body is ClassName
```

You can also check if a given property, method, or signal name exists in an object with the `in` operator:


```gdscript

func _on_body_entered(body):
	if "inventory" in body:
	if "pickup" in body:
	if "player_spotted" in body:
```

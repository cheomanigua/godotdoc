---
weight: 800
title: "RayCast2D"
description: "How to create and use RayCast2D"
icon: "article"
date: "2025-02-08T19:35:43+02:00"
lastmod: "2025-02-08T19:35:43+02:00"
draft: false
toc: true
---

- [RayCast2D - Godot Documentation](https://docs.godotengine.org/en/stable/classes/class_raycast2d.html)
- [ShapeCast2D - Godot Documentation](https://docs.godotengine.org/en/stable/classes/class_shapecast2d.html#class-shapecast2d)
- [Ray-casting - Godot Documentation](https://docs.godotengine.org/en/stable/tutorials/physics/ray-casting.html)


### Description

A ray in 2D space, used to find the first CollisionObject2D it intersects. A raycast represents a ray from its origin to its target_position that finds the closest CollisionObject2D along its path, if it intersects any. The origin is the parent node.

RayCast2D can ignore some objects by adding them to an exception list, by making its detection reporting ignore Area2Ds (collide_with_areas) or PhysicsBody2Ds (collide_with_bodies), or by configuring physics layers.

RayCast2D calculates intersection every physics frame, and it holds the result until the next physics frame. For an immediate raycast, or if you want to configure a RayCast2D multiple times within the same physics frame, use force_raycast_update.

To sweep over a region of 2D space, you can approximate the region with multiple RayCast2Ds or use [ShapeCast2D](https://docs.godotengine.org/en/stable/classes/class_shapecast2d.html#class-shapecast2d)

### Properties

- `raycast.enabled = true` Enable raycast. Collisions will be reported.
- `raycast.set_enabled(true)` Enable raycast. Collisions will be reported.
- `raycast.enabled = false` Disable raycast. Collisions won't be reported.
- `raycast.set_enabled(false)` Disable raycast. Collisions won't be reported.

### Methods

- `raycast.force_raycast_update()` One time shot raycast. **enabled** doesn't need to be `true`.
- `raycast.get_collider()` Returns the first object that the ray intersects, or `null` if no object is intersecting the ray.
- `raycast.is_colliding()` Returns whether any object is intersecting with the ray's vector (considering the vector length).


### Execution

```gdscript
var raycast: RayCast2D = RayCast2D.new()

func _ready():
	add_child(raycast)
	raycast.enabled = true
	raycast.target_position = Vector2(350, 0)
```

The above code creates a new raycast extending 350 pixeles in front of the node. The raycast origin is relative to the node position, so there is no need to specify the **from** position.


### Make the raycast visible

You can see the raycast in the screen for development by activating the **Debug** -> **Visible Collision Shapes**.

If you want to make the raycast visible during game, you can draw lines, circles, etc relative to the raycast:

```gdscript
func _physics_process(delta: float) -> void:
	queue_redraw()

func _draw() -> void:
	draw_line(raycast.position, raycast.target_position, Color.GREEN, 1.0)
	draw_circle(Vector2(raycast.target_position), 8.0, Color.SKY_BLUE, false, -1.0, false)
```

<br>

### Example

The example below features a node that will shoot the player if the player stays within a 180º arc in front of the node. If the player exists the arc or the detection area, the node will forget about it. If the player moves behind obstacles, the node will stop shooting. In order to detect obstacles, the node uses a `RayCast2D`.

We could had stopped and started the timer in the `_on_player_detected()` and `_on_player_lost()` methods, but decided to do it inside `_physics_process()` for more fine tunning.



```gdscript
extends Area2D

@export var reload_time: float = 1.0

const Bullet = preload("res://Projectile/Bullet/bullet.tscn")
var raycast: RayCast2D = RayCast2D.new()
var detected: bool = false
var can_shoot: bool = true
var elapsed: float = 10.0
var player: RigidBody2D

@onready var timer: Timer = Timer.new()
@onready var radar: Area2D = %Radar


func _ready():
	add_child(raycast)
	raycast.target_position = Vector2(350, 0)
	add_child(timer)
	timer.timeout.connect(_on_timer_timeout)
	timer.wait_time = reload_time
	radar.body_entered.connect(_on_player_detected)
	radar.body_exited.connect(_on_player_lost)


func _physics_process(delta: float) -> void:
	queue_redraw()
	if detected:
		var target: Vector2 = position.direction_to(player.position)
		var facing = Vector2(cos(rotation), sin(rotation))
		var fov = target.dot(facing) # field of view
		if fov > 0:
			rotation = lerp_angle(rotation, target.angle(), elapsed * delta)
			if can_shoot:
				if raycast.is_colliding():
					var collider = raycast.get_collider()
					if collider != player:
						timer.stop()
						can_shoot = true
					else:
						if can_shoot:
							_shoot()
							can_shoot = false
							timer.start()
		else:
			timer.stop()
			can_shoot = true
	else:
		timer.stop()
		can_shoot = true


func _draw() -> void:
	draw_line(raycast.position, raycast.target_position, Color.GREEN, 1.0)
	draw_circle(Vector2(raycast.target_position), 8.0, Color.SKY_BLUE, false, -1.0, false)


func _on_player_detected(body):
	if body is Player:
		player = body
		detected = !detected
		raycast.enabled = true
		#timer.start()


func _on_player_lost(body):
	if body is Player:
		detected = !detected
		raycast.enabled = false
		#timer.stop()


func _on_timer_timeout() -> void:
	can_shoot = true


func _shoot():
	var new_bullet: Bullet = Bullet.instantiate()
	new_bullet.transform = Transform2D(rotation, position)
	#new_bullet.rotation = rotation
	#new_bullet.position = position
	get_parent().add_child(new_bullet)
```

### Hints

- Always create the raycast in the node that directly cast it. For instance, if you have tank composed of a turret node and a vehicle node, and you need a raycast to detect enemies, create a script in the turret node and create a new `RayCast2D`. This way, you can move forward with the vehicle while scanning for enemies and locking onto targets with the rotating turret.

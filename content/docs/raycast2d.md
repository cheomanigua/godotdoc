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

[Godot Documentation](https://docs.godotengine.org/en/stable/classes/class_raycast2d.html)





The example below features a node that will shoot the player if the player stays within a 180º arc in front of the node. If the player exists the arc or the detection area, the node will forget about it. If the player moves behind obstacles, the node will stop shooting. In order to detect obstacles, the node uses a `RayCast2D`.

We could had stopped and started the timer in the `body_entered` and `body_exited` methods, but decided to do it inside `_physics_process()` for more fine tunning.

You can see the raycast in action by activating the **Debug** -> **Visible Collision Shapes**. On my case, I wanted to draw a line to make it visible in the game.


```gdscript
extends Area2D

@export var reload_time: float = 1.0

var raycast: RayCast2D = RayCast2D.new()
var ray_length: float = 350
var detected: bool = false
var can_shoot: bool = true
var elapse: float = 5.0
var player: RigidBody2D

@onready var timer: Timer = Timer.new()
@onready var radar: Area2D = $Radar


func _ready():
	add_child(raycast)
	add_child(timer)
	timer.timeout.connect(_on_timer_timeout)
	timer.wait_time = reload_time
	rotation = deg_to_rad(rotation)
	radar.player_detected.connect(_on_player_detected)
	radar.player_lost.connect(_on_player_lost)


func _physics_process(delta: float) -> void:
	if detected:
		var target: Vector2 = position.direction_to(player.position)
		var facing = Vector2(cos(rotation), sin(rotation))
		var fov = target.dot(facing) # field of view
		raycast.target_position = Vector2(ray_length, 0)
		queue_redraw()
		if fov > 0:
			rotation = lerp_angle(rotation, target.angle(), elapse * delta)
			await get_tree().create_timer(0.5).timeout
			look_at(player.position)

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
	draw_circle(Vector2(ray_length, 0), 8.0, Color.SKY_BLUE, false, -1.0, false)
	

func _on_player_detected(body):
	player = body
	detected = !detected
	raycast.enabled = true
	#timer.start()


func _on_player_lost():
	detected = !detected
	raycast.enabled = false
	#timer.stop()


func _on_timer_timeout() -> void:
	can_shoot = true


func _shoot():
	var new_bullet: Bullet = Bullet.instantiate()
	new_bullet.rotation = rotation
	new_bullet.position = position
	get_parent().add_child(new_bullet)
```

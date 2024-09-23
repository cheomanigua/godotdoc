---
weight: 10500
title: "Rotating Turret"
description: "How to make a turret with a cannon rotate towards the player's direction"
icon: "article"
date: "2024-09-23T09:27:29+02:00"
lastmod: "2024-09-23T09:27:29+02:00"
draft: false
toc: true
---

In this example we show how a **turret** will rotate towards the **player** when the player enters the **radar** area. The **turret** will start to shoot every second towards the **player** while the player stays in the radar area 180º in front of the cannon.


### Node Structure

```

[Area2D] "turret"						S
	|-[CollisionBody2D] "Radar"
	|-[CollisionBody2D] "Base"			%
	|	|-[CollisionBody2D] "Cannon"
	|		|-[Marker2D] "Muzzle"		%
	|		|-[Marker2D] "Shoot-At"		%
	|-[Timer]							%
```


### Node layout

- The base of the **turret** is a circle with a **cannon** sticking out of it.
- There are two markers along the longitudinal axis of the **cannon** at the end point of the **cannon**: the **muzzle** and shoot-at.
- The **muzzle** marker is the spawing point of the bullets.
- The **shoot-at** marker is the direction where the bullets move towards from the **muzzle**. 
- There is a **radar** area around the **turret** scanning for the **player**.
- There is a **timer** in charge of shooting the **cannon** every second when **player** is detectedand within 180º cannon front area.


### Scripting

#### turret.gd
```gdscript

extends Area2D

const BULLET = preload("res://Projectile/Bullet/bullet.tscn")
var detected: bool = false
var locked: bool = false
var elapse: float = 5.0
var original_angle: float
@onready var base: CollisionShape2D = %Base
@onready var muzzle: Marker2D = %Muzzle
@onready var marker_2d: Marker2D = %Marker2D
@onready var timer: Timer = %Timer
@onready var player: Player = %Player


func _ready():
	original_angle = base.global_rotation
	body_entered.connect(_on_body_entered)
	body_exited.connect(_on_body_exited)


func _physics_process(delta: float) -> void:
	if detected:
		var angle: float = (player.global_position - base.global_position).angle()
		if (angle_difference(original_angle, angle) < PI/2 && angle_difference(original_angle, angle) > -PI/2):
			locked = true
			base.global_rotation= lerp_angle(base.global_rotation, angle, elapse * delta)
		else:
			locked = false


func _on_body_entered(body):
	if body is Player:
		detected = !detected
		timer.timeout.connect(_shoot)
		timer.start()
		

func _on_body_exited(body):
	if body is Player:
		detected = !detected
		timer.timeout.disconnect(_shoot)


func _shoot():
	if detected and locked:
		var new_bullet = BULLET.instantiate()
		get_tree().root.call_deferred("add_child", new_bullet)
		new_bullet.global_position = muzzle.global_position
		new_bullet.look_at(marker_2d.global_position)
```

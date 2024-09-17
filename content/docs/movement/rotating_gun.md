---
weight: 10400
title: "Rotating Gun"
description: "How to make a gun rotate around the player"
icon: "article"
date: "2024-09-09T11:36:13+02:00"
lastmod: "2024-09-09T11:36:13+02:00"
draft: false
toc: true
---

- In this recipe will implement of a gun rotating around the player, or to be more precise, a gun rotating around a pivot.
- The gun points where the mouse cursor is.
- The player moves independently from the gun rotation.

### Node structure

#### Main
```
[Node] "Main"									S
		|-[Player] "CharacterBody2D"			%
```

#### Player
```
[CharacterBody2D] "Player"						S
		|-[CollisionPolygon2D]
		|-[Marker2D] "Pivot"					%
				|-[Sprite2D] "Gun"
						|-[Marker2D] "Muzzle"	%
```
- **Player** and **Pivot** center points are located in the same place.
- **Gun** center point is offset from **Pivot** center point.
- **Muzzel** center point is offseet from **Gun** center point.


#### Bullet
```
[Area2d] "Bullet"								S
		|-[CollisionShape2D]
		|-[VisibleOnScreenNotifier2D]			%
```


### Scripts

#### player.gd

```gdscript
extends CharacterBody2D

signal shoot(bullet, direction, location)

@export var speed = 200

const BULLET = preload("res://Scenes/Projectiles/bullet.tscn")

@onready var pivot: Marker2D = %Pivot
@onready var muzzle: Marker2D = %Muzzle

func _unhandled_input(event: InputEvent) -> void:
	if event.is_action_pressed("shoot"):
		shoot.emit(BULLET, pivot.rotation, muzzle.global_position)

func _process(_delta):
	pivot.look_at(get_global_mouse_position())

func _physics_process(_delta: float) -> void:
	var input_direction = Input.get_vector("left", "right", "up", "down")
	velocity = input_direction * speed
	move_and_slide()
```

#### bullet.gd

```gdscript
extends Area2D

var speed:float = 2000
var damage:float = 1

@onready var visible_on_screen_notifier_2d: VisibleOnScreenNotifier2D = %VisibleOnScreenNotifier2D


func _ready() -> void:
	body_entered.connect(_on_body_entered)
	visible_on_screen_notifier_2d.screen_exited.connect(_on_screen_exited)


func _physics_process(delta):
	global_position += transform.x * speed * delta


func _on_body_entered(body):
	queue_free()
	if body.has_method("take_damage"):
		body.take_damage(damage)


func _on_screen_exited() -> void:
	queue_free()
```

#### main.gd

```gdscript
extends Node

@onready var player: CharacterBody2D = %Player

func _ready() -> void:
	player.shoot.connect(_on_player_shoot)

func _on_player_shoot(Bullet, direction, location):
	var spawned_bullet = Bullet.instantiate()
	add_child(spawned_bullet)
	spawned_bullet.rotation = direction
	spawned_bullet.position = location
```

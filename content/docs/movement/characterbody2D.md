---
weight: 10100
title: "CharacterBody2D - Mouse shoot"
description: "In this article I explain how to move with the keyboard and point and shoot with the mouse"
icon: "article"
date: "2024-08-28T19:24:58+02:00"
lastmod: "2024-08-28T19:24:58+02:00"
draft: false
toc: true
---

### Setup
- **W**, **A**, **S** and **D** keys has been mapped to up, left, down, right.
- **Left mouse button** has been mapped to shoot.
- `Debug` -> `Visible Collision Shapes` has been enabled.

### File structure
```
res://
    |-scenes
    |	    |-bullet.tscn
    |	    |-enemy.tscn
    |	    |-main.tscn
    |	    |-player.tscn
    |
    |-scripts
	    |-bullet.gd
	    |-enemy.gd
	    |-player.gd
```

### Node structure
```
[Node2D] "Main"
		|-Player
		|-Enemy
```
Bullets are instantiated at runtime, so they do not appear in the initial node structure.


### Player

#### Node Hierarcy
```
[CharacterBody2D] "Player"
		|-[CollisionShape2D]
```
#### player.gd
```gdscript
extends CharacterBody2D

@export var speed = 400
const BULLET = preload("res://scenes/bullet.tscn")

func _unhandled_input(event: InputEvent) -> void:
	if event.is_action_pressed("shoot"):
		shoot()

func get_input():
	var input_direction = Input.get_vector("left", "right", "up", "down")
	velocity = input_direction * speed
		
func _physics_process(delta):
	get_input()
	move_and_slide()

func shoot():
	var new_bullet = BULLET.instantiate()
	owner.add_child(new_bullet)
	new_bullet.global_position = global_position
	new_bullet.look_at(get_global_mouse_position())
```


### Enemy

#### Node hierarchy

```
[StaticBody2D] "Enemy"
		|-[CollisionShape2D]
		|-[ProgressBar] "HealthBar"
```

#### enemy.gd
```gdscript
extends StaticBody2D

@onready var health_bar = %HealthBar
@export var health:float = 10

signal died

func _ready():
	health_bar.max_value = health
	health_bar.value = health

func take_damage(damage:float):
	health -= damage
	health_bar.value = health
	
	if health <= 0:
		died.emit()
		queue_free()
```


### Bullet

#### Node hierarchy
```
[Area2D] "Bullet"
		|-[CollisionShape2D]
```

#### bullet.gd
```gdscript
extends Area2D

var speed:float = 400
var damage:float = 1

func _ready():
	#set_as_top_level(true)
	body_entered.connect(_on_area_2d_body_entered)
	
func _physics_process(delta):
	global_position += transform.x * speed * delta

func _on_area_2d_body_entered(body):
	queue_free()
	#if body.is_in_group("enemy"):
	#	print("Enemy hit")
	if body.has_method("take_damage"):
		body.take_damage(damage)
	else:
		print("Nothing happened")
```



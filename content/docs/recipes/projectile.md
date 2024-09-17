---
weight: 5250
title: "Projectile"
description: "Creating and destroying a projectile object"
icon: "article"
date: "2024-09-17T09:39:26+02:00"
lastmod: "2024-09-17T09:39:26+02:00"
draft: false
toc: true
---

### Steps

1. Instantiate a projectile
2. Detect collision
3. Inflict damage
4. Destroy projectile when hitting object or leaving the screen
	- `VisibleOnScreenNotifier2D` node will detect when the parent node has left the screen


### Node structure

```
[Area2d] "Bullet"								S
		|-[CollisionShape2D]
		|-[VisibleOnScreenNotifier2D]			%
```

### Scripts


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


#### player.gd

```gdscript

...

signal shoot(bullet, direction, location)

...

const BULLET = preload("res://Scenes/Projectiles/bullet.tscn")

...

func _unhandled_input(event: InputEvent) -> void:
	if event.is_action_pressed("shoot"):
		shoot.emit(BULLET, pivot.rotation, muzzle.global_position)

func _process(_delta):
	pivot.look_at(get_global_mouse_position())
...
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

### Full implementation

{{< alert text="You can see a full implementation of a projectile at [Rotating Gun](../../movement/rotating_gun)" />}}

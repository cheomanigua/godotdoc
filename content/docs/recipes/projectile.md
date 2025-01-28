---
weight: 5250
title: "Projectiles"
description: "Projectile behaviour. Born and death of a bullet"
icon: "article"
date: "2024-09-17T09:39:26+02:00"
lastmod: "2024-09-17T09:39:26+02:00"
draft: false
toc: true
---

There are several ways to implement a projectile behaviour. It depends on the type of node the projectile is, and the type of node the shooter is. For this tutorial, we'll call the projectile a bullet.

A bullet can be an Aread2D node or a CharacterBody2D node. The shooter can be a CharacterBody2D or a RigidBody2D. Combining the type of bullet and the type of shooter requires slighty different code.

## 1. CharacterBody2D shooter

### 1.1 shooter.gd

```gdscript
extends CharacterBody2D

var bullet = preload("res://bullet.tscn")

func shoot():
	# "Muzzle" is a Marker2D placed at the barrel of the gun.
	var new_bullet = bullet.instantiate()
	new_bullet.start($Muzzle.global_position, rotation)
	get_parent().add_child(new_bullet)
```

### 1.2 bullet.gd

```gdscript
extends CharacterBody2D

var speed = 750

func start(_position, _direction):
	rotation = _direction
	position = _position
	velocity = Vector2(speed, 0).rotated(rotation)

func _physics_process(delta):
	var collision = move_and_collide(velocity * delta)
	if collision:
		velocity = velocity.bounce(collision.get_normal())
		if collision.get_collider().has_method("hit"):
			collision.get_collider().hit()

func _on_VisibilityNotifier2D_screen_exited():
	# Deletes the bullet when it exits the screen.
	queue_free()
```

## 2. RigidBody2D shooter

### 2.1 shooter.gd

For a RigidBody2D shooter, you need two Marker2D for the bullet to shoot straight. The **Muzzle** marker represents the position of the instantiated bullet, and the **ShootAt** marker represents the direction of the instantiated bullet. Align both markers to the gun barrel and the bullet will shoot straight.

```gdscript
extends RigidBody2D

const BULLET = preload("res://Projectile/Bullet/bullet.tscn")

func _shoot():
	# "%Muzzle" and "%ShootAt" are two lined up Marker2Ds placed at the barrel of the gun.
    var bullet = BULLET.instantiate()
    get_parent().add_child(bullet)
    bullet.global_position = %Muzzle.global_position
    bullet.look_at(%ShootAt.global_position)
```

### 2.2 bullet.gd

```gdscript
extends Area2D

var speed:float = 500
@export var damage: float = 1
@onready var vosn2d: VisibleOnScreenNotifier2D = %VisibleOnScreenNotifier2D

func _ready() -> void:
	body_entered.connect(_on_body_entered)
	vosn2d.screen_exited.connect(_on_screen_exited)

func _physics_process(delta: float) -> void:
	global_position += transform.x * speed * delta

func _on_screen_exited() -> void:
	queue_free()

func _on_body_entered(body):
	queue_free()
	if body.has_method("take_damage"):
		body.take_damage(damage)
```


## Instantiating a bullet with signals

In the previous examples, if we try to test our "Player" scene independently, it will crash on shooting, because there is no parent node to access. The solution to this is to use a signal to "emit" the bullets from the player. The player then has no need to "know" what happens to the bullets after that.

[Godot Documentation](https://docs.godotengine.org/en/stable/tutorials/scripting/instancing_with_signals.html)

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
	var new_bullet = Bullet.instantiate()
    add_child(new_bullet)
	new_bullet.rotation = direction
	new_bullet.position = location
```

### Full implementation

{{< alert text="You can see a full implementation of a projectile at [Rotating Gun](../../movement/rotating_gun)" />}}

---
weight: 10500
title: "Rotating Turret"
description: "How to make a turret with a cannon to rotate towards the player's direction"
icon: "article"
date: "2024-09-23T09:27:29+02:00"
lastmod: "2024-09-23T09:27:29+02:00"
draft: false
toc: true
---

In this example we show how a **turret** will rotate towards the **player** when the player enters a 180º arc in front of the **turret**. The **turret** will start to shoot every second towards the **player** for as long as the player stays in the 180º arc in front of the cannon and there is no obstacle blocking its sight.


## Node Layout

```

[StaticBody2D] "Turret"					  % S
	|-[CollisionShape2D]
	|-[Area2D] "Radar"                    % S
	|	|-[CollisionBody2D]
	|-[Marker2D] "Giro"		              % S
	|		|-[Sprite2D] "Cannon"		  %
	|		|-[Sprite2D] "Giro"
```

Symbols: `%` Unique node - `S` Script

## Node functions

- The **Turret** node is never rotating. It's the place holder for the **Label** text, so the text will always appear in a fixed position regardless of the rotation of the cannon.
- The **Radar** node scans for the player. when the player is detected, **Turret** enables the **raycast** and setups the **Giro**.
- The **Giro** node with rotate towards the player if the players enters the 180º arc in from of the **Giro**. When this occurs, **Giro** will lock on to the player and start shooting every second. If there is an obstacle between the **Giro** and the player, **Giro** will stop shooting. If there is no obstacle, **Giro** will resume shooting.

## External classes

There is a **Bullet** class with a static method constructor that facilitates the instantiation when instantiated. The **Bullet** features three types of munition, which deal three types of damage amount. The player can choose the type of munition for every **Turret** added to the scene.


## Scripting

### `turret.gd`

```gdscript
extends StaticBody2D

@export var health: int = 5
enum munition { LOW_DAMAGE, MEDIUM_DAMAGE, HIGH_DAMAGE }
@export var munition_type: munition = munition.LOW_DAMAGE
@export var reload_time: float = 1.0
@export var cannon_rotation: float = 0

var detected: bool = false
var can_shoot: bool = true
var elapsed: float = 10.0
var player: RigidBody2D

@onready var timer: Timer = Timer.new()
@onready var radar: Area2D = %Radar
@onready var cannon: Sprite2D = %Cannon
@onready var giro: Marker2D = %Giro


func _ready():
	add_child(timer)
	giro.rotation = deg_to_rad(cannon_rotation)
	timer.timeout.connect(_on_timer_timeout)
	timer.wait_time = reload_time
	radar.player_detected.connect(_on_player_detected)
	radar.player_lost.connect(_on_player_lost)


func _physics_process(delta: float) -> void:
	if detected:
		var target: Vector2 = position.direction_to(player.position)
		var facing = giro.transform.x
		var fov = target.dot(facing) # field of view
		if fov > 0:
			giro.rotation = lerp_angle(giro.rotation, target.angle(), elapsed * delta)
			if can_shoot:
				if giro.raycast.is_colliding():
					var collider = giro.raycast.get_collider()
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


func _on_player_detected(body):
	player = body
	detected = !detected
	giro.raycast.enabled = true
	#timer.start()


func _on_player_lost():
	detected = !detected
	giro.raycast.enabled = false
	#timer.stop()


func _on_timer_timeout() -> void:
	can_shoot = true


func _shoot():
	var tween = create_tween()
	tween.tween_property(cannon, "position", Vector2(-10, 0), 0.2 * reload_time).as_relative().set_trans(Tween.TRANS_SINE)
	tween.tween_property(cannon, "position", Vector2(10, 0), 0.2 * reload_time).as_relative().set_trans(Tween.TRANS_SINE)
	var new_bullet: Bullet = Bullet.create_bullet(munition_type)
	get_parent().add_child(new_bullet)
	new_bullet.transform = Transform2D(giro.rotation, position + Vector2(32, 0).rotated(giro.rotation))
	#new_bullet.position = position + Vector2(32, 0).rotated(giro.rotation)
	#new_bullet.rotation = giro.rotation


func take_damage(damage):
	var label: Label = Label.new()
	add_child(label)
	label.position = Vector2(0, -50) + Vector2(randf_range(-20, 20), 0)
	label.position = Vector2(-10, -30) + Vector2(randf_range(-20, 20), 0)
	label.text = "-%d" % [damage]
	
	var tween: Tween = create_tween()
	tween.tween_property(label, "position", Vector2(0, -30), 2.0).as_relative().set_ease(Tween.EASE_IN_OUT)
	tween.set_parallel()
	tween.tween_property(label, "modulate:a", 0, 2.0)
	#tween.tween_property(label, "scale", Vector2.ZERO, 2.0)
	tween.connect("finished", Callable(label, "queue_free"))
	
	health -= damage
	if health <= 0:
		queue_free()
```

### `radar.gd`

```gdscript
extends Area2D

signal player_detected
signal player_lost


func _ready():
	body_entered.connect(_on_body_entered)
	body_exited.connect(_on_body_exited)


func _on_body_entered(body):
	if body is Player:
		player_detected.emit(body)
		

func _on_body_exited(body):
	if body is Player:
		player_lost.emit()
```


### `giro.gd`

```gdscript
extends Marker2D

var raycast: RayCast2D = RayCast2D.new()

func _ready() -> void:
	add_child(raycast)
	raycast.target_position = Vector2(350, 0)

func _physics_process(_delta: float) -> void:
	queue_redraw()

func _draw() -> void:
	draw_line(raycast.position, raycast.target_position, Color.GREEN, 1.0)
```


### `bullet.gd`

```gdscript
class_name Bullet
extends Area2D
const BULLET: PackedScene = preload("res://Projectile/Bullet/bullet.tscn")
enum munition_type { LOW_DAMAGE = 1, MEDIUM_DAMAGE, HIGH_DAMAGE }
var munition_index: int = 0
var speed:float = 500
var damage: int = 1

@onready var vosn2d: VisibleOnScreenNotifier2D = %VisibleOnScreenNotifier2D

# static method created to instantiate bullets with parameters in other scenes
static func create_bullet(_munition_index: int) -> Bullet:
	var new_bullet: Bullet = BULLET.instantiate()
	new_bullet.munition_index = _munition_index
	return new_bullet


func _ready() -> void:
	damage = munition_type.values()[munition_index]

	body_entered.connect(_on_body_entered)
	vosn2d.screen_exited.connect(_on_screen_exited)


func _physics_process(delta: float) -> void:
	global_position += transform.x * speed * delta


func _on_screen_exited() -> void:
	await get_tree().create_timer(3.0).timeout
	queue_free()


func _on_body_entered(body):
	queue_free()
	if body.has_method("take_damage"):
		body.take_damage(damage)
```

---
weight: 10200
title: "RigidBody2D - Gravity"
description: "Asteroid like game with gravity and impulse force damage"
icon: "article"
date: "2024-09-02T09:41:07+02:00"
lastmod: "2024-09-02T09:41:07+02:00"
draft: false
toc: true
---

- The example shows how to move a RigidBody2D using thrust for impulse, and torque for turning.
- Damage is inflicted when the RigidBody2D collides with other object. Damage is directly proportional to the impulse force.

### Setup
- **Arrow left** and **Arrow right**: Torque
- **Arrow up**: Thrust
- `Debug` -> `Visible collision shapes` has been enabled.

### Node structure

```
[RigidBody2D] -> "Ship"
		|-[CollisionPolygon2D]
		|-[ProgressBar] -> "HealhBar"
```

### Code

{{< alert text="In order for **RigidBody2D** to detect collisions, `contact_monitor` has to be set to `true` and `max_contacts_reported` has to be bigger than `0`." />}}


```gdscript
extends RigidBody2D

var thrust = Vector2(0, -500)
var torque = 10000
var collision_force : Vector2 = Vector2.ZERO
var previous_linear_velocity : Vector2 = Vector2.ZERO
var previous_velocity : Vector2 = Vector2.ZERO
var health: float = 500

@onready var health_bar: ProgressBar = %HealthBar

func _ready() -> void:
	health_bar.max_value = health
	health_bar.value = health
	contact_monitor = true
	max_contacts_reported = 1
	body_entered.connect(_on_body_entered)

func _physics_process(_delta: float) -> void:
	previous_velocity = linear_velocity
	
func _integrate_forces(state):
	for i in range(state.get_contact_count()):
		collision_force += state.get_contact_impulse(i) * state.get_contact_local_normal(i)

	if Input.is_action_pressed("ui_up"):
		state.apply_force(thrust.rotated(rotation))
	else:
		state.apply_force(Vector2())
	var rotation_direction = 0
	if Input.is_action_pressed("ui_right"):
		rotation_direction += 1
	if Input.is_action_pressed("ui_left"):
		rotation_direction -= 1
	state.apply_torque(rotation_direction * torque)
	
func _on_body_entered(body):
	var damage:float = 0
	var impulse = mass * (linear_velocity - previous_velocity)
	if impulse.length() > 100:
		damage = impulse.length() - 100
		take_damage(damage)
		
func take_damage(damage: float):
	health -= damage
	health_bar.value = health

	if health <= 0:
		print("You died!!!")
		
```

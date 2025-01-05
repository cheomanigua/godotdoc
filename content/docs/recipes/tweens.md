---
weight: 5275
title: "Tweens"
description: "How to create quick and easy animations"
icon: "How to create fast and simple animations"
date: "2024-10-19T11:05:31+02:00"
lastmod: "2024-10-19T11:05:31+02:00"
draft: false
toc: true
---

### Show pop up damage

```gdscript

func take_damage(damage):
	var label: Label = Label.new()
	add_child(label)
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


### Cannon firing animation

```gdscript

func _shoot():
	if detected and locked:
		var tween = create_tween()
		tween.tween_property(cannon, "position", Vector2(-10, 0), 0.2).as_relative().set_trans(Tween.TRANS_SINE)
		tween.tween_property(cannon, "position", Vector2(10, 0), 0.2).as_relative().set_trans(Tween.TRANS_SINE)
		var new_bullet = BULLET.instantiate()
		get_tree().root.call_deferred("add_child", new_bullet)
		new_bullet.global_position = muzzle.global_position
		new_bullet.look_at(shoot_at.global_position)
```

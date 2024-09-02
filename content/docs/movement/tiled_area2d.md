---
weight: 10300
title: "Tiled Area2D"
description: "Characters move from center of tile to center of tile"
icon: "article"
date: "2024-08-30T13:52:42+02:00"
lastmod: "2024-08-30T13:52:42+02:00"
draft: false
toc: true
---

### parent node

```gdscript
var tile_size = 32
var can_move = true
var facing = 'ui_right'

var moves = {'ui_right': Vector2.RIGHT,
    'ui_left': Vector2.LEFT,
   'ui_up': Vector2.UP,
   'ui_down': Vector2.DOWN}

var raycasts = {'ui_right': 'RayCastRight',
   'ui_left': 'RayCastLeft',
   'ui_up': 'RayCastUp',
   'ui_down': 'RayCastDown'}

func move(dir):
	if get_node(raycasts[facing]).is_colliding():
	return

 facing = dir
 can_move = false

 $AnimationPlayer.play(facing)
 $MoveTween.interpolate_property(self, "position", position, position + moves[facing] * tile_size, 0.8, Tween.TRANS_LINEAR, Tween.EASE_IN_OUT)
 $MoveTween.start()
 return true


func _on_MoveTween_tween_completed( object, key ):
	can_move = true
```

### child Player node

```gdscript
func _process(delta):
	if can_move:
		for dir in moves.keys():
			if Input.is_action_pressed(dir):
				facing = dir
				move(facing)
```


### child NPC node

```gdscript
func _ready():
	randomize()
	facing = moves.keys()[randi() % 4]

func _process(delta):
    if can_move:
        if not move(facing) or randi() % 10 > 5:
            facing = moves.keys()[randi() % 4]
```

---
weight: 5300
title: "Combat"
description: ""
icon: "article"
date: "2024-08-30T13:19:07+02:00"
lastmod: "2024-08-30T13:19:07+02:00"
draft: false
toc: true
---

We can decouple the combat by creating a combat system. Remember that Player scene is autoloaded, so any of its properties can be accessed globally from any script.

#### Orc.gd

```gdscript
extends CharacterBody2D

@onready var combat = load("res://Scripts/Logic/combat.gd").new()

export (Dictionary) var stats = {
		"strength" : 4,
		"intelligence" : 4,
		"dexterity" : 3,
		"endurance" : 6,
		"health" : 10,
		}

func get_stat(stat):
		return stats[stat]

func _ready():
	    add_child(combat)
# warning-ignore:return_value_discarded
		$HitBox.body_entered.connect(_on_Orc_body_entered)

func _on_Orc_body_entered(body):
		if body.get_name() == "Player":
				combat.attack(self)
```

#### _combat.gd

```gdscript
extends Node

func attack(enemy):
		var health = enemy.get_stat("health")
		randomize()
		var percentage = randi() % 10
		if percentage > Player.get_stat("dexterity"):
				print("%s Missed" % [Player.get_name()])
		else:
				var damage = randi() % Player.get_stat("strength") + 1
				health -= damage
				print ("%s was hit by %s for %d hit points." % [enemy.get_name(), Player.get_name(), damage])
		enemy.stats.health = health
		status(enemy,health)

func status(enemy,health):
		if health > 0:
				print("%s health is %d" % [enemy.get_name(), health])
		else:
				print("%s died" % [enemy.get_name()])
				enemy.queue_free()
```

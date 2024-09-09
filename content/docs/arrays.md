---
weight: 450
title: "Arrays"
description: "Working with arrays"
icon: "article"
date: "2024-09-06T00:32:33+02:00"
lastmod: "2024-09-06T00:32:33+02:00"
draft: false
toc: true
---


### Basic inventory

```gdscript
...

var inventory: Array[String] = ["Coin", "Potion", "Potion", "Coin", "Potion", "Gem", "Potion", "Coin", "Gem"]

...

func show_inventory():
	var a: String
	inventory.sort()
	for i in inventory:
		if a != i: 
			print(i + " x" + str(inventory.count(i)))
		a = i 
```

It will print:
```
Coin x3
Gem x2
Potion x4
```

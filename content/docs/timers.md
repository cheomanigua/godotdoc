---
weight: 700
title: "Timers"
description: "How to create and use timers"
icon: "article"
date: "2025-02-08T12:51:43+02:00"
lastmod: "2025-02-08T12:51:43+02:00"
draft: false
toc: true
---


[Godot Documentation](https://docs.godotengine.org/en/stable/classes/class_timer.html)

## Timer creation

Timers can be created in three different ways:

1. Adding a Timer node in the Editor and referencing it in code:

    {{< tabs tabTotal="2">}}
    {{% tab tabName="GDScript" %}}

```gdscript
@onready var timer: Timer = %Timer
```
{{% /tab %}}
{{% tab tabName="C#" %}}

```csharp
public override void _Ready()
{
    var timer = GetNode<Timer>("Timer");
}
```
{{% /tab %}}
{{< /tabs >}}

2. Creating the Timer node directly in code:

    {{< tabs tabTotal="2">}}
    {{% tab tabName="GDScript" %}}

```gdscript
var timer: Timer = Timer.new()

somefunction():
    add_child(timer)
    timer.wait_time = 2
```
{{% /tab %}}
{{% tab tabName="C#" %}}

```csharp
    private Timer timer = new();

public override void _Ready()
{
    AddChild(timer);
    timer.Start(2f);
}
```
{{% /tab %}}
{{< /tabs >}}

3. Creating a one-shot ephemeral timer. The code below shows a message during 5 seconds. The timer is deleted afterward automatically:

    {{< tabs tabTotal="2">}}
    {{% tab tabName="GDScript" %}}

```gdscript
$Label.text = message
await get_tree().create_timer(5.0).timeout
$Label.text = ""
```

{{% /tab %}}
{{% tab tabName="C#" %}}

```csharp
using Godot;
using System.Threading.Tasks;

public partial class MyNode : Node
{
	public async Task WaitForTimeout()
	{
		await ToSignal(GetTree().CreateTimer(5.0f), "timeout");
		GD.Print("Timeout reached");
	}
		
	public override void _Ready()
	{
        _ = WaitForTimeout();
	}
}
```

{{% /tab %}}
{{< /tabs >}}

## Signals

- `timeout()` Emitted when the timer reaches the end.
- We can activate methods when timer reaches an end: `timer.timeout.connect(_shoot)`
- We can detach from a connected method as well: `timer.timeout.disconnect(_shoot)`

This is useful for running a function every x seconds. A more detailed example can be found [further down](#timer-connected-to-function-in-body_entered-signal).

## Properties

- `timer.wait_time = 2.0` Timer cycle. The time required for the timer to end, in seconds. `1.0` is the default value.
- `timer.get_time_left()` The timer's remaining time in seconds. This is always 0 if the timer is stopped.
- `timer.time_left` The timer's remaining time in seconds. This is always 0 if the timer is stopped.
- `timer.set_paused(true)` pauses the timer.
- `timer.paused = true` pauses the timer.
- `timer.set_paused(false)` unpauses the timer.
- `timer.paused = false` unpauses the timer.
- `timer.is_paused()` checks if the timer is paused.

## Methods

- `timer.start()` starts the timer.
- `timer.stop()` stops the timer.
- `timer.is_stopped()` checks if the timer is stopped.


Once the timer has been created, we can set it up in different ways.

## Examples

### Do something every 3 seconds

{{< tabs tabTotal="2">}}
{{% tab tabName="GDScript" %}}

```gdscript
var timer: Timer = Timer.new()

func _ready():
	add_child(timer)
	timer.wait_time = 3.0 # 3 seconds
	timer.start()
	timer.timeout.connect(_on_timer_timeout)

func _on_timer_timeout():
	print("This message is printed every 3 seconds")
```

{{% /tab %}}
{{% tab tabName="C#" %}}

```csharp
using Godot;

public partial class Main : Node
{
    private Timer timer = new();

    public override void _Ready()
    {
        //var timer = new Timer()
        AddChild(timer);
        timer.Start(3f);
        timer.Timeout += OnTimerTimout;
    }

    private void OnTimerTimout()
    {
        GD.Print("This message is printed every 3 seconds");
    }
}
```

{{% /tab %}}
{{< /tabs >}}

### Timer connected to function in `body_entered()` signal

This example makes the node show a message every 2 seconds when player enters the Area2D, and stop showing the message when player leaves the Area2D.

`body_entered()` is in charge of starting and stoppint the timer, and also of activating the function `_shoot()` via the timer. This way, is the timer in the `body_entered` method who activates the function `_shoot`.

```gdscript
var timer: Timer = Timer.new()
var detected: bool = false

func _ready():
	add_child(timer)
	timer.wait_time = 2.0 # 2 seconds
	body_entered.connect(_on_body_entered)
	body.exited.connect(_on_body_exited)


func _on_body_entered(body):
	player = body
	detected != detected
	timer.start()
	timer.timeout.connect(_shoot)


func _on_body_exited():
	detected != detected
	timer.timeout.disconnect(_shoot)
	timer.stop()


func _shoot():
	print("This message is printed every 2 seconds when body is entered,
    but stop being printed when body exited")
```
<br>

### Timer not connected to function in `body_entered()` signal

This example makes the node show a message every 2 seconds when player enters the Area2D, and stop showing the message when player leaves the Area2D.

`_physics_process()` is in charge of activating the function `_shoot()` and to start the timer initially. Area2D `body_entered` helps starting and stopping the timer later on. This way, is `_physics_process()` who activates the function `_shoot()` directly.

```gdscript
var can_shoot: bool = true
var detected: bool = false
var player: CharacterBody2D
@onready var timer: Timer = Timer.new()

func _ready():
	add_child(timer)
	timer.timeout.connect(_on_timer_timeout)
	timer.wait_time = 2.0 # 2 seconds
	body_entered.connect(_on_body_entered)
	body_exited.connect(_on_body_exited)


func _physics_process(delta: float) -> void:
	if detected:
    	if can_shoot:
    		_shoot()
    		can_shoot = false
    		timer.start()

func _on_body_entered(body):
	player = body
	detected != detected
	timer.start()


func _on_body_exited():
	detected != detected
	timer.stop()


func _on_timer_timeout() -> void:
	can_shoot = true


func _shoot():
	print("This message is printed every second when body is entered, 
    but stop being printed when body exited")

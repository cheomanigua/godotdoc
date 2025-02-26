---
weight: 900
title: "User Input"
description: ""
icon: "article"
date: "2024-08-28T13:24:17+02:00"
lastmod: "2024-08-28T13:24:17+02:00"
draft: false
toc: true
---


## Input

### Processes in order of execution

The [Node](https://docs.godotengine.org/en/stable/classes/class_node.html) class contains the following methods:

- [_input](https://docs.godotengine.org/en/stable/classes/class_node.html#class-node-private-method-input)
	- This is the first input method that gets called.
	- For gameplay input, **_unhandled_input** and **_unhandled_key_input** are usually a better fit as they allow the GUI to intercept the events first.
    - [InputEvent](#inputevent) as parameter.
- [_gui_input](https://docs.godotengine.org/es/stable/classes/class_control.html#class-control-private-method-gui-input)
	- Second input method to be called.
	- It can only be used by **Control** nodes.
    - [InputEvent](#inputevent) as parameter.
- [_shortcut_input](https://docs.godotengine.org/en/stable/classes/class_node.html#class-node-private-method-shortcut-input)
	- This method can be used to handle shortcuts.
	- For generic GUI events, use **_input** instead.
	- Gameplay events should usually be handled with either **_unhandled_input** or **_unhandled_key_input**.
    - [InputEvent](#inputevent) as parameter.
- [_unhandled_key_input](https://docs.godotengine.org/en/stable/classes/class_node.html#class-node-private-method-unhandled-key-input)
	- This method can be used to handle Unicode character input with `Alt`, `Alt + Ctrl`, and `Alt + Shift` modifiers, after shortcuts were handled.
	- For gameplay input, this and **_unhandled_input** are usually a better fit than _input, as GUI events should be handled first.
	- This method also performs better than **_unhandled_input**, since unrelated events such as InputEventMouseMotion are automatically filtered, hence, mouse movements do not activate this function
	- For shortcuts, consider using **_shortcut_input** instead.
    - [InputEvent](#inputevent) as parameter.
- [_unhandled_input](https://docs.godotengine.org/en/stable/classes/class_node.html#class-node-private-method-unhandled-input)
	- The same as **unhandle_key_input**, but this methods also handles mouse events.
	- To handle only keyboard events, consider using **_unhandled_key_input** for performance reasons.
    - [InputEvent](#inputevent) as parameter.
- [_process](https://docs.godotengine.org/en/stable/classes/class_node.html#class-node-private-method-process)
	- Called during the processing step of the main loop. Processing happens at every frame and as fast as possible, so the `delta` time since the previous frame is not constant. `delta` is in seconds.
- [_physics_process](https://docs.godotengine.org/en/stable/classes/class_node.html#class-node-private-method-physics-process)
	- Called during the physics processing step of the main loop. Physics processing means that the frame rate is synced to the physics, i.e. the `delta` variable should be constant. `delta` is in seconds.

### Input types

The most common input types are: [Input](#input-1), [InputEvent](#inputevent-and-inputeventkey) and [InputEventKey](#inputevent-and-inputeventkey)

#### Input

A singleton for handling inputs.

- [Input](https://docs.godotengine.org/en/stable/classes/class_input.html) Use them inside one of the last two methods listed at [Processes in order of execution](#processes-in-order-of-execution) `_process(delta)` or `_physics_process(delta)`. The best use of Input is when a continous event is needed, like moving a character with keyboard keys.
  - [get_vector](https://docs.godotengine.org/en/stable/classes/class_input.html#class-input-method-get-vector)
  - [get_axis](https://docs.godotengine.org/en/stable/classes/class_input.html#class-input-method-get-axis)
  - [is_action_just_pressed](https://docs.godotengine.org/es/stable/classes/class_input.html#class-input-method-is-action-just-pressed) *
  - [is_action_just_released](https://docs.godotengine.org/es/stable/classes/class_input.html#class-input-method-is-action-just-released) *
  - [is_action_pressed](https://docs.godotengine.org/es/stable/classes/class_input.html#class-input-method-is-action-pressed) *
  - [is_key_pressed](https://docs.godotengine.org/en/stable/classes/class_input.html#class-input-method-is-key-pressed)
  - [is_physical_key_pressed](https://docs.godotengine.org/en/stable/classes/class_input.html#class-input-method-is-physical-key-pressed)
  - [is_mouse_button_pressed](https://docs.godotengine.org/es/stable/classes/class_input.html#class-input-method-is-mouse-button-pressed)
  - [get_mouse_button_mask](https://docs.godotengine.org/es/stable/classes/class_input.html#class-input-method-get-mouse-button-mask)

\* **is_action** is specified by a name (such as **"ui_right"**) defined in the **Project->Project Settings->Input Map** panel of the Editor. As well as the default actions, we may redefine them and add more of our own.

##### Example

{{< tabs tabTotal="2">}}
{{% tab tabName="GDScript" %}}

```gdscript

func _physics_process(_delta):
	var input_direction = Input.get_vector("ui_left", "ui_right", "ui_up", "ui_down")
	velocity = input_direction * speed
	move_and_slide()
```
{{% /tab %}}
{{% tab tabName="C#" %}}

```csharp
    public override void _PhysicsProcess(double delta)
    {
        Vector2 inputDirection = Input.GetVector("ui_left", "ui_right", "ui_up", "ui_down");
        Velocity = inputDirection * Speed;
        MoveAndSlide();
    }
```
{{% /tab %}}
{{< /tabs >}}

<br>

#### InputEvent / InputEventKey

- [InputEvent](https://docs.godotengine.org/en/stable/classes/class_inputevent.html) Abstract base class of all types of input events. Use them inside one of the first five methods listed at [Processes in order of execution](#processes-in-order-of-execution). The best use of InputEvent is when a single event is needed, like showing the inventory, pausing the game, jumping, etc. 

  - [is_action_pressed](https://docs.godotengine.org/en/stable/classes/class_inputevent.html#class-inputevent-method-is-action-pressed) *
  - [is_action_released](https://docs.godotengine.org/en/stable/classes/class_inputevent.html#class-inputevent-method-is-action-released) *
  - [is_pressed](https://docs.godotengine.org/en/stable/classes/class_inputevent.html#class-inputevent-method-is-pressed)
  - [is_released](https://docs.godotengine.org/en/stable/classes/class_inputevent.html#class-inputevent-method-is-released)

\* **is_action** is specified by a name (such as **"ui_right"**) defined in the **Project->Project Settings->Input Map** panel of the Editor. As well as the default actions, we may redefine them and add more of our own.

- [InputEventKey](https://docs.godotengine.org/en/stable/classes/class_inputeventkey.html) Represents a key on a keyboard being pressed or released. Use them inside one of the first five methods listed at [Processes in order of execution](#processes-in-order-of-execution). The best use of InputEventKey is when a single event is needed, like showing the inventory, pausing the game, jumping, etc.
    - [pressed](https://docs.godotengine.org/en/stable/classes/class_inputeventkey.html#class-inputeventkey-property-pressed)
    - [keycode](https://docs.godotengine.org/en/stable/classes/class_inputeventkey.html#class-inputeventkey-property-keycode)
    - [physical_keycode](https://docs.godotengine.org/en/stable/classes/class_inputeventkey.html#class-inputeventkey-property-physical-keycode)

##### Example 1

{{< tabs tabTotal="2">}}
{{% tab tabName="GDScript" %}}

```gdscript

func _unhandled_key_input(event: InputEvent) -> void:

	if event.is_action_pressed("ui_down"):
		print("Arrow down pressed")
	
	if event is InputEventKey:
		if event.pressed:
			match event.keycode:
				KEY_W:
					print("W pressed")
				KEY_E:
					print("E pressed")
			if event.keycode == KEY_T:
				print("T pressed")
		if event.pressed and event.keycode == KEY_ESCAPE:
			get_tree().quit()
```

{{% /tab %}}
{{% tab tabName="C#" %}}

```csharp

public override void _UnhandledKeyInput(InputEvent @event)
{
	if (@event.IsActionPressed("ui_down"))
	{
		GD.Print("Arrow down pressed");
	}

	if (@event is InputEventKey keyEvent)
	{
		if (keyEvent.Pressed)
		{
			switch (keyEvent.Keycode)
			{
				case Key.W:
					GD.Print("W pressed");
					break;
				case Key.E:
					GD.Print("E pressed");
					break;
			}
			if (keyEvent.Keycode == Key.T)
			{
				GD.Print("T pressed");
			}
		}

		if (keyEvent.Pressed && keyEvent.Keycode == Key.Escape)
		{
			GetTree().Quit();
		}
	}
}
```

{{% /tab %}}
{{< /tabs >}}

##### Example 2

In the example below, a square will  switch colors between blue and red when the left mouse button in clicked over it.


{{< tabs tabTotal="2">}}
{{% tab tabName="GDScript" %}}

```gdscript
extends Node2D

var clicked=false

func _unhandled_input(event: InputEvent) -> void:
	if (event.is_action_pressed("mouse_left_button")):
		clicked=!clicked
		queue_redraw()

func _draw():
	var r = Rect2(Vector2(), Vector2(40,40))
	if (clicked):
		draw_rect(r, Color(1,0,0))
	else:
		draw_rect(r, Color(0,0,1))
	set_process_input(true)

# For the left click to work, add "mouse_left_button" to Project Settings -> Input Map
```



{{% /tab %}}
{{% tab tabName="C#" %}}

```csharp

using Godot;

public partial class MyClass : Node2D
{
	bool clicked = false;

    public override void _UnhandledInput(InputEvent @event)
    {
        if (@event.IsActionPressed("mouse_left_button"))
		{
			clicked = !clicked;
			QueueRedraw();
		}
    }

	public override void _Draw(){
		var r = new Rect2(new Vector2(), new Vector2(100, -100));
		if (clicked)
		{
			DrawRect(r, new Color(1,0,0));			
		}
		else {
			DrawRect(r, new Color(0,0,1));
		}
		SetProcessInput(true);
	}
}

//  For the left mouse click to work, add "mouse_left_button" to Project Settings -> Input Map
```

{{% /tab %}}
{{< /tabs >}}


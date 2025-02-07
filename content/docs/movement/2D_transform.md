---
weight: 10050
title: "2D Transform"
description: "2D movement in Godot: vectors, matrices, rotation and position"
icon: "article"
date: "2025-01-24T19:24:58+02:00"
lastmod: "2025-01-24T19:24:58+02:00"
draft: false
toc: true
---

On this artile we explain how **Transform2D** works in the context of creating movement of a node. Matrices and vectors are used to change the **rotation** and **position** of a node, that is, to create movement.

References:

- Transform2D: [Godot Documentation](https://docs.godotengine.org/en/stable/classes/class_transform2d.html)
- Matrices and transforms: [Godot Documentation](https://docs.godotengine.org/en/stable/tutorials/math/matrices_and_transforms.html)
- Vector math: [Godot Documentation](https://docs.godotengine.org/en/stable/tutorials/math/vector_math.html#doc-vector-math)

## Rotation

In Godot, the property `rotation` can be set via inspector or via code. Setting the rotation in the inspector will visually show degrees, but internally the engine is using radians.

However, if we set the property `rotation` via code, the value we input will be radians. If we prefer to use degress, we must enclose the value with the function `deg_to_rad()`.

Also, Godot uses the positive `x` axis as `0` for the default `rotation` value. Rotating to the left produces negative values, and rotating to the right produces positive values:

(radians in red, degrees in blue)

![rotation](/images/rotation.webp)

So, if we want to rotate an object to face up, there are four ways we can do it:

1. Via Inspector: setting the **Rotation** property value to `-90`
2. Via code: `rotation = -1.57`
3. Via code: `rotation = deg_to_rad(-90)`
4. Via code: `set_rotation_degrees(-90)`

#### Values

In Godot, `180`º is the same as `-180`º. Likewise, `3.14` is the same as `-3.14`, and `0` is the same as `-0`. Some examples:

- **Degrees**: if we print to log the value of the rotation to the right starting from 178º, it gives us (omitting decimals): 178, 179, +/-180, -179, -178 until we reach, for instance, -90. At this point, although it prints -90, we could manually set the value to `270`, which is the same as `-90`. And `360` is the same as `-/+ 0`.
- **Radians**: if we print to log the value of the rotation to the right starting from 3.12, it gives us: 3.12, 3.13, +/-3.14, -3.13, -3.12 until we reach, for instance, -1.57. At this point, although it prints -1.57, we could manually set the value to `4.61`, which is the same as `-1,57`. And `6.28` is the same as `-/+ 0`.

Godot has the constants `PI` and `TAU` to deal with geometry:

- **PI** = `3.14` or `TAU/2` or 180º
- **TAU** = `6.28` or `PI*2`  or 360º

There are some built in helpers and convertion functions:

- If we want to get `rotation` property in degrees: `rotation_degrees`
- If we want to set `rotation` property in degrees: `set_rotation_degrees(90)`
- If we want to convert from degrees to radians: `deg_to_rad(90)`
- If we want to custom convert from radians to degrees: `180/PI*rotation`

<br>

#### Functions

***

##### deg_to_rad() [](https://docs.godotengine.org/en/stable/classes/class_@globalscope.html#class-globalscope-method-deg-to-rad)

- float **deg_to_rad(deg:** float **)** 

*(Globals)* Converts an angle expressed in degrees to radians.

`var r = deg_to_rad(180) # r is 3.141593`

***

##### rotation_degrees [](https://docs.godotengine.org/en/stable/classes/class_control.html#class-control-property-rotation-degrees)

- float **rotation_degrees** - getter ------ float **rotation_degrees(value)** - setter

*(Control)* Helper property to access `rotation` in degrees instead of radians.

`print(rotation_degrees)` or `print(get_rotation_degrees())` and `set_rotation_degrees(-90)`

***

##### angle_difference() [](https://docs.godotengine.org/en/stable/classes/class_@globalscope.html#class-globalscope-method-angle-difference)

- float **angle_difference(from:** float, **to:** float **)** 

*(Globals)* Returns the difference between the two angles, in the range of `[-PI, +PI]`. When `from` and `to` are opposite, returns `-PI` if `from` is smaller than `to`, or `PI` otherwise.

***

##### rotate() [](https://docs.godotengine.org/en/stable/classes/class_node2d.html#class-node2d-method-rotate)

- void **rotate(radians**: float **)**

*(Node2D)* Applies a rotation to the node, in radians, starting from its current rotation.

***

##### rotated() [](https://docs.godotengine.org/en/stable/classes/class_vector2.html#class-vector2-method-rotated)

- Vector2 **rotated(angle**: float **)**

*(Vector2)* Returns the result of rotating this vector by angle (in radians).

***

##### look_at() [](https://docs.godotengine.org/en/stable/classes/class_node2d.html#class-node2d-method-look-at)

- void **look_at(point**: Vector2)

{{< alert context="primary" text="`rotate_toward()` and `lerp_angle()` are better alternatives." />}}

*(Node2D)* Rotates **INSTANTLY** the node so that its local +X axis `points` towards the point, which is expected to use global coordinates.

`point` should not be the same as the node's position, otherwise the node always looks to the right.

`look_at(target.position)`

***

##### rotate_toward() [](https://docs.godotengine.org/en/stable/classes/class_@globalscope.html#class-globalscope-method-rotate-toward)

- float **rotate_toward(from**: float, **to**: float, **delta**: float **)**

{{< alert context="success" text="A better alternative to `look_at()`" />}}

*(Globals)* **SLOWLY** rotates `from` toward `to` by the `delta` amount. Will not go past `to`.

Similar to `move_toward()`, but interpolates correctly when the angles wrap around `@GDScript.TAU`.

If `delta` is negative, this function will rotate away from `to`, toward the opposite angle, and will not go past the opposite angle.

`rotation = rotate_toward(rotation, angle_to_target, delta)`

To calculate `angle_to_target`, check [Custom code](#custom-code)

***

##### lerp_angle() [](https://docs.godotengine.org/en/stable/classes/class_@globalscope.html#class-globalscope-method-lerp-angle)

- float **lerp_angle(from:** float, **to:** float, **weight:** float **)**

{{< alert context="success" text="A better alternative to `rotate_toward()`" />}}

*(Globals)* Rotates **GRADUALLY**. Linearly interpolates between two angles (in radians) by a `weight` value between 0.0 and 1.0.

Similar to lerp, but interpolates correctly when the angles wrap around `TAU`. To perform eased interpolation with `lerp_angle`, combine it with [ease](https://docs.godotengine.org/en/stable/classes/class_@globalscope.html#class-globalscope-method-ease) or [smoothstep](https://docs.godotengine.org/en/stable/classes/class_@globalscope.html#class-globalscope-method-smoothstep).

```gdscript
extends Sprite
var elapsed = 0.0
func _process(delta):
	var min_angle = deg_to_rad(0.0)
	var max_angle = deg_to_rad(90.0)
	rotation = lerp_angle(min_angle, max_angle, elapsed)
	elapsed += delta
```

**Note**: This function lerps through the shortest path between `from` and `to`. However, when these two angles are approximately `PI + k * TAU` apart for any integer `k`, it's not obvious which way they lerp due to floating-point precision errors. For example, `lerp_angle(0, PI, weight)` lerps counter-clockwise, while `lerp_angle(0, PI + 5 * TAU, weight)` lerps clockwise.

***

##### direction_to() [](https://docs.godotengine.org/en/stable/classes/class_vector2.html#class-vector2-method-direction-to)

- Vector2 **direction_to(to:** Vector2)

*(Vector2)* Returns the normalized vector pointing from this vector to `to`. This is equivalent to using `(b - a).normalized()`.

`position.direction_to(target.position)`

***

##### dot() [](https://docs.godotengine.org/en/stable/classes/class_vector2.html#class-vector2-method-dot)

- float **dot**(with: Vector2)

*(Vector2)* Returns the dot product of this vector and `with`. This can be used to compare the angle between two vectors. For example, this can be used to determine whether a node is facing another node, or to setup a field of view.



```gdscript
var to_target: Vector2 = position.direction_to(target.position)
var facing = Vector2(cos(rotation), sin(rotation))
var fov = to_target.dot(facing)

if fov > 0.5:
	print("Target detected, face the target!")
	rotation = lerp_angle(rotation, to_target.angle(), elapse * delta)
```
- `to_target`: Gets the vector that goes from `self` to **target**.
- `facing`: Gets the vector that `self` is currently facing to.
- `fov`: Setups the field of view of `self` in relation to the target.
- Finaly we check if the target is within a 90º arc in front `self`.

How does the field of view work? What `0.5` represents and what it has to do with a `90`º arc in front `self`?

`0.5` is the result of the **dot** product. When using unit (normalized) vectors, the result will always be between `-1.0` (180º angle) when the vectors are facing opposite directions, and `1.0` (0º angle) when the vectors are aligned. If the target is exactly 45º in front of `self` (the **Smiley** in the image below), left or right, the dot product result will be `0.5`.

![dot](/images/fov.webp)

So as per the graphic above, and given that the blue arrow indicates where **Smiley** is facing, if we wanted to check if the target was in a 180º arc in front of **Smiley**, we'd have used:

```gdscript
if fov > 0:     # 180 degree arc in front of smiley

# other values
if fov > 0.5:   #  90 degree arc in front of smiley
if fov > -0.5:  # 270 degree arc in front of smiley
if fov = 1:     # right in front of smiley
if fov = -1:    # right behind smiley
if fov < -0.5:  #  90 degree arc behind smiley
```

We could have achieved the same result with the code below. However, past the set field of view arc, **Smiley** could not rotate further, and hence, will lose the target.

```gdscript
var direction: float = rotation
var angle: float = (target.position - position).normalized().angle()

if angle_difference(direction, angle) < PI/4 and angle_difference(direction, angle) > -PI/4:
	print("Target detected, face the target!")
	rotation = lerp_angle(rotation, angle, elapse * delta)
```

<br>

#### Custom code

***

- Vector2 **(from:** Vector2 **- to:** Vector2 **).normalized()**

Returns the normalized vector pointing from this vector to `to`. This is equivalent to using: Vector2 **direction_to(to:** Vector2)

`var towards: Vector2 = (target.position - position).normalized()`

***

- float **(from:** Vector2 **- to:** Vector2 **).normalized().angle()**

Returns the angle between two points

`var angle: float = (target.position - position).normalized().angle()`

***

- float **from:** Vector2 **(to:** Vector2).**angle()**

Returns the angle between two points

`var angle: float = position.direction_to(target.position).angle()`



***

- Vector2 **(cos**(float), **(sin**(float))

Returns a Vector2 with the direction the node is facing

`var facing = Vector2(cos(rotation), sin(rotation))`

***

## Translation


Translation or movement is obtained by updating the `position` value every frame. `position` is a **Vector2** value relative to its node's parent. `position` can have its value updated by multiplying `velocity` and `delta`, like this: `position += velocity * delta`.

- `velocity` is a Vector2 variable and is calculated by multiplying a custom variable like `speed` by either:
    - `transform.x`
    - `Vector2(1, 0).rotated(rotation`
    - `Vector2.RIGHT.rotated(rotation)`
    - `Vector2.from_angle(rotation)`



    **Note**: When using `Vector2` instead of `transform.x`, if we don't add the method `rotated(rotation)` or `from_angle(rotation)`, the node will be moving to the same direction regardless of the rotation.
- `velocity` has to be declared and defined, except for **CharacterBody2D** nodes, which comes built in.
- **CharacterBody2D** is recommended to use the function `move_and_slide()` or `move_and_collide()` instead of `position += velocity * delta`.
- `delta` is a parameter that represents the time elapsed since the previous frame. Velocity measures the change in position per unit of time. The new position is found by adding the velocity multiplied by `delta` (here assumed to be one unit, e.g. 1 s) to the previous position.
- In a typical 2D game scenario, you would have a velocity in pixels per second, and multiply it by the delta parameter (time elapsed since the previous frame) from the `_process()` or `_physics_process()` callbacks. This way, `velocity` is time dependent and not frame dependent. We don't want a computer to move the node faster just because it has a better graphic card with higher frame per seconds processing.


### Key binding

When setting up the key binding for moving forward, backward, right and left, we have to take into consideration the following:

- Moving foward is on the `transform.x` axis, or `Vector2(1, 0)`, or `Vector2.RIGHT`
- Moving backwards is on the `-transform.x` axis, or `Vector2(-1, 0)`, or `Vector2.LEFT`
- Moving right is on the `transform.y` axis, or `Vector2(0, 1)`, or `Vector2.DOWN`
- Moving left is on the `-transform.y` axis, or `Vector2(0, -1)`, or `Vector2.UP`

**Note**: When using `Vector2` instead of `transform.x`, if we don't add the method `rotated(rotation)` or `from_angle(rotation)`, the node will be moving to the same direction regardless of the rotation.

![translation](/images/translation.webp)


### Recipes

#### 4 Axis movement

```gdscript
var speed: int = 400
var velocity = Vector2.ZERO			# Use for non CharacterBody2D. Don't use for CharacterBody2D

func get_input():
	if Input.is_action_pressed("ui_up"):
		velocity = Vector2.RIGHT.rotated(rotation) * speed
	elif Input.is_action_pressed("ui_down"):
		velocity = Vector2.LEFT.rotated(rotation) * speed
	elif Input.is_action_pressed("ui_right"):
		velocity = Vector2.DOWN.rotated(rotation) * speed
	elif Input.is_action_pressed("ui_left"):
		velocity = Vector2.UP.rotated(rotation) * speed
	else:
		velocity = Vector2.ZERO

func _physics_process(delta):
	get_input()
	position += velocity * delta    # Option 1 use for any node
	move_and_slide()                # Option 2 use for CharacterBody2D only (use it!!!)
```

#### 8 Axis movement

```gdscript
var speed: int = 400
var velocity = Vector2.ZERO			# Use for non CharacterBody2D. Don't use for CharacterBody2D

func get_input():
	var input_direction = Input.get_vector("ui_left", "ui_right", "ui_up", "ui_down")
	velocity = input_direction * speed

func _physics_process(delta):
	get_input()
	position += velocity * delta    # Option 1 use for any node
	move_and_slide()                # Option 2 use for CharacterBody2D only (use it!!!)
```

#### Rotate and move (push forward/backward)

For this type of movement, where pressing the **UP** arrow keys move forward, it is recommended to use `transform.x` or `Vector2.RIGHT.rotated(rotation`. The reason for this is that Godot uses the positive `x` axis as default `0` rotation value (check the section [Rotation](#rotation) for further info). This means that moving along `transform.x` is moving towards the default direction, and no further convertions/tweekings are needed.

In addition, the `Input.get_axis(-1, 1)` built in method can be used either for rotating or moving. The first parameter is `-1` and the second parameter is `+1`:

- For **rotation** in `rotation` we have to assign:
    - a key to the first parameter to turn left.
    - a key to second parameter to turn right.
- For **translation** in `transform.x` we have to assign:
    - a key to the first parameter to move backward.
    - a key to the second parameter to move forward.

Example:

```gdscript
var speed = 400
var rotation_speed = PI
var rotation_direction = 0
var velocity = Vector2.ZERO			# For [METHOD 2] only, use for non CharacterBody2D. Don't use for CharacterBody2D

func get_input():
	rotation_direction = Input.get_axis("ui_left", "ui_right")
	[METHOD 1]
	[METHOD 2]

func _physics_process(delta):
	get_input()
	rotation += rotation_speed * rotation_direction * delta
	position += velocity * delta    # Option 1 use for any node
	move_and_slide()                # Option 2 use for CharacterBody2D only (use it!!!)
```

*METHOD 1*

```gdscript
	velocity = Vector2.ZERO			# For CharacterBody2D only, velocity has to be defined here
	var velocity = Vector2.ZERO		# For non CharacterBody2D, velocity has to be declared and defined here
	if Input.is_action_pressed("ui_up"):
		velocity = transform.x * speed                      # Option 1
		velocity = Vector2.RIGHT.rotated(rotation) * speed  # Option 2
	if Input.is_action_pressed("ui_down"):
		velocity = -transform.x * speed                     # Option 1
		velocity = Vector2.LEFT.rotated(rotation) * speed   # Option 2
```

*METHOD 2*

```gdscript
	# Option 1
	velocity = transform.x * Input.get_axis("ui_down", "ui_up") * speed
	# Option 2
	velocity = Vector2.RIGHT.rotated(rotation) * Input.get_axis("ui_down", "ui_up") * speed
```

<br>

**Use Cases for Rotate and Move**:

The following use cases take the principles of rotate and move, and adapt it to different type of vehicles. The use cases code are for `CharacterBody2D`. We are only changing the `get_input()` method. The rest of the code is exactly the same as above.

##### Simulate wheeled vehicle (car, bus, etc)

To simulate the behavior of a wheeled vehicle, we can change the `get_input()` function to this:

```gdscript

func get_input():
	velocity = Vector2.ZERO
	if Input.is_action_pressed("ui_up"):
		rotation_direction = Input.get_axis("ui_left", "ui_right")
		velocity = transform.x * speed
	elif Input.is_action_pressed("ui_down"):
		rotation_direction = Input.get_axis("ui_right", "ui_left")
		velocity = -transform.x * speed
	else:
		rotation_direction = 0
```

##### Simulate track vehicle (tank, APC, etc)

To simulate the behavior of a track vehicle, we can change the `get_input()` function to this:

```gdscript

func get_input():
	velocity = Vector2.ZERO
	rotation_direction = Input.get_axis("ui_left", "ui_right")
	if Input.is_action_pressed("ui_up"):
		rotation_direction = Input.get_axis("ui_left", "ui_right")
		velocity = transform.x * speed
	elif Input.is_action_pressed("ui_down"):
		rotation_direction = Input.get_axis("ui_right", "ui_left")
		velocity = -transform.x * speed
```

#### Circular Translation

```gdscript
var speed = 400
var rotation_speed = PI

func _process(delta):
	rotation += rotation_speed * delta
	var velocity = Vector2.UP.rotated(rotation) * speed
	position += velocity * delta
```

#### Ricochet/Bounce

```gdscript
var collision: KinematicCollision2D = move_and_collide(velocity * delta)
if collision:
	var reflect = collision.get_remainder().bounce(collision.get_normal())
	velocity = velocity.bounce(collision.get_normal())
	move_and_collide(reflect)
```

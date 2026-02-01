---
title: vec2
summary: Represents a 2D vector (point) with x and y coordinates, commonly used for positions and directions.
icon: polyline
publish: true
---
# Introduction
Represents a 2D vector/point with `x` and `y` coordinates.

Although x and y are stored as floating-point values, they represent pixel coordinates. This allows points to be positioned with subpixel precision. For most basic drawing operations, these values are typically cast to integers before rendering.

# Properties

## x
The x coordinate measured in pixels. This property is a float value.

```python
v = vec2()
v.x = 50
v.y = 100
```

## y
The y coordinate measured in pixels. This property is a float value.

```python
v = vec2()
v.x = 50
v.y = 100
```

# Methods

## transform
Returns a new `vec2` with the specified matrix transformation applied.

**Parameters**

- `transform(m)`
    - `m`: A transformation matrix

**Returns**

- A new `vec2` representing the transformed position
---
title: rect
summary: Represents a 2D rectangle and provides helpful methods for manipulating and working with rectangular regions.
icon: activity_zone
publish: true
---
# Introduction
Represents a rectangular region defined by its top-left position (`x`, `y`) and size (`w` × `h`).

Although `x`, `y`, `w`, and `h` are stored as floating-point values, they represent pixel coordinates. This allows rectangles to be positioned with subpixel precision. For most basic drawing operations, these values are typically cast to integers before rendering.

# Properties

## x
The x-coordinate of the top-left corner of the rectangle. This property is a float value.

```python
r = rect()
r.x = 50
```

## y
The y-coordinate of the top-left corner of the rectangle. This property is a float value.

```python
r = rect()
r.y = 50
```

## w
The width of the rectangle. This property is a float value.

```python
r = rect()
r.w = 100
```

## h
The height of the rectangle. This property is a float value.

```python
r = rect()
r.h = 80
```

## l
The x-cordinate of the top-left corner of the rectangle. This property is a float value.

```python
r = rect()
r.l = 50
```

## r
The x-coordinate of the bottom-right corner of the rectangle. This property is a float value.

```python
r = rect()
r.r = 50
```

## t
The y-cordinate of the top-left corner of the rectangle. This property is a float value.

```python
r = rect()
r.w = 100
```

## b
The y-coordinate of the bottom-right corner of the rectangle. This property is a float value.

```python
r = rect()
r.h = 80
```

# Methods

## offset
Returns a new rectangle offset by the specified amount.

**Parameters**

- `offset(x, y)`
    - `x, y`: Amount to offset the rectangle by

**Returns**

- A new `rect` representing the offset rectangle

**Example code**

```python
r = rect(20, 20, 80, 80)
r1 = r.offset(10, 10)
```

## deflate
Returns a new rectangle with its area reduced in size.

**Parameters**

- `deflate(a)`
    - `a`: Amount to deflate each edge by

- `deflate(t, r, b, l)`
    - `t, r, b, l`: Amounts to deflate the top, right, bottom, and left edges

**Returns**

- A new `rect` representing the deflated rectangle

**Example code**

```python
r = rect(20, 20, 80, 80)
r1 = r.deflate(10)
r2 = r.deflate(10, 20, 10, 20)
```

## inflate
Returns a new rectangle with its area increased in size.

**Parameters**

- `inflate(a)`
    - `a`: Amount to inflate each edge by

- `inflate(t, r, b, l)`
    - `t, r, b, l`: Amounts to inflate the top, right, bottom, and left edges

**Returns**

- A new `rect` representing the inflate rectangle

**Example code**

```python
r = rect(20, 20, 40, 40)
r1 = r.inflate(10)
r2 = r.inflate(10, 20, 10, 20)
```

## intersection
Returns a new rectangle representing the overlapping area between this rectangle and another.

If the rectangles do not overlap, `None` is returned.

**Parameters**

- `intersection(other)`
    - `other`: The rectangle to intersect with

**Returns**

- A new `rect` representing the intersection of the two rectangles, or `None` if there is no overlap

**Example code**

```python
r1 = rect(20, 20, 40, 40)
r2 = rect(30, 30, 40, 40)
r3 = r1.intersection(r2)
```

## intersects
Returns `True` if this rectangle overlaps with another rectangle, otherwise `False`.

**Parameters**

- `intersects(other)`
    - `other`: The rectangle to test for intersection with

**Returns**

- `True` if the rectangles overlap, otherwise `False`

**Example code**

```python
r1 = rect(20, 20, 40, 40)
r2 = rect(30, 30, 40, 40)
if r1.intersects(r2):
  print("overlap!")
```

## contains
Returns `True` if this rectangle fully contains another rectangle, otherwise `False`.

**Parameters**

- `contains(other)`
    - `other`: The rectangle to test for containment

**Returns**

- `True` if the rectangle is fully contained, otherwise `False`

**Example code**

```python
r1 = rect(20, 20, 40, 40)
r2 = rect(30, 30, 5, 5)
if r1.contains(r2):
  print("r2 is inside r1!")
```

## empty
Returns `True` if this rectangle has a width and height of zero, otherwise `False`.

**Returns**

- `True` if the rectangle is empty, otherwise `False`

**Example code**

```python
r = rect(20, 20, 40, 40)
r.deflate(10)
r.empty() # returns True
```
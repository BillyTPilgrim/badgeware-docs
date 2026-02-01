---
title: shape
summary: Easily create primitive vector shapes as well as custom shapes, then modify them to build more complex vector graphics.
icon: shapes
publish: true
---
# Introduction
Unlike pixel-based graphics, vector shapes are described using geometry rather than fixed pixels. This allows them to be scaled, transformed, and positioned with much greater precision.

Shapes are defined by paths — a collection of points that make up the outline. Even with this simple representation, vector shapes are extremely useful for drawing clean UI elements, icons, and geometric artwork.

Because shapes are resolution-independent, they can be drawn at different sizes without becoming blocky or distorted. When combined with antialiasing, they allow you to create crisp, smooth graphics that would be difficult to achieve with bitmap drawing alone.

# Primitives
The following static methods create new `shape` objects.

## regular_polygon
Creates a new shape representing a regular polygon — a closed shape with evenly spaced sides and equal angles (for example: triangles, squares, pentagons, and so on).

**Parameters**

- `regular_polygon(x, y, radius, sides)`
    - `x, y`: Position of the centre point
    - `radius`: Radius of the polygon (distance from the centre to each corner)
    - `sides`: Number of sides (must be 3 or greater)

**Returns**

- A new `shape` object

**Example code**
```python
def update():
  sides = ((io.ticks // 500) % 10) + 3

  polygon = shape.regular_polygon(80, 60, 40, sides)
  screen.pen = color.red
  screen.shape(polygon)

  screen.pen = color.white
  screen.text(f"{sides} sides", 5, 5)
```

## circle
Creates a new `shape` representing a circle.

**Parameters**

- `circle(centre, radius)`
    - `centre`: Position of the centre point (`vec2`)
    - `radius`: Radius of the circle in pixels

- `circle(x, y, radius)`
    - `x, y`: Position of the centre point
    - `radius`: Radius of the circle in pixels

**Returns**

- A new `shape` object

**Example code**
```python
import math

def update():
  screen.pen = color.taupe
  circle = shape.circle(80, 60, 40)
  screen.shape(circle)
```

## rectangle
Creates a new `shape` representing a rectangle.

**Parameters**

- `rectangle(x, y, width, height)`
    - `x, y`: Coordinates of the top-left corner
    - `width, height`: Width and height of the rectangle

**Returns**

- A new `shape` object

**Example code**

```python
def update():
  screen.pen = color.blue
  rectangle = shape.rectangle(30, 30, 100, 60)
  screen.shape(rectangle)
```

## rounded_rectangle
Creates a new `shape` representing a rectangle with rounded corners.

Rounded rectangles are especially useful for modern UI elements such as buttons, panels, dialog boxes, and badges.

You can specify either a single corner radius for all corners, or provide individual radii to create asymmetric shapes.

**Parameters**

- `rounded_rectangle(x, y, width, height, radius)`
    - `x, y`: Coordinates of the top-left corner
    - `width, height`: Width and height
    - `radius`: Corner radius applied to all corners

- `rounded_rectangle(x, y, width, height, r1, r2, r3, r4)`
    - `x, y`: Coordinates of the top-left corner
    - `width, height`: Width and height
    - `r1, r2, r3, r4`: Corner radii (top-left, top-right, bottom-right, bottom-left)

**Returns**

- A new `shape` object

**Example code**

```python
def update():
  screen.pen = color.yellow
  rounded_rectangle = shape.rounded_rectangle(15, 10, 60, 60, 10)
  screen.shape(rounded_rectangle)

  screen.pen = color.navy
  rounded_rectangle = shape.rounded_rectangle(85, 50, 60, 60, 0, 20, 0, 20)
  screen.shape(rounded_rectangle)
```


## squircle
Creates a new shape representing a squircle — a smooth shape that sits somewhere between a square and a circle.

Squircles are useful for UI elements like icons and buttons, producing corners that feel softer and more natural than a standard rounded rectangle.

The optional squareness factor controls the shape: lower values are more circular, higher values more square-like.

**Parameters**

- `squircle(x, y, s[, n])`
    - `x, y`: Position of the centre point
    - `s`: Size of the squircle
    - `n`: Optional squareness factor (default 4)

**Returns**

- A new `shape` object

**Example code**

```python
def update():
  screen.pen = color.orange
  squircle = shape.squircle(80, 60, 40)
  screen.shape(squircle)
```

## arc
Creates a new shape representing an arc segment.

Arcs are useful for gauges, progress indicators, rings, and other circular UI elements. The arc is defined by an inner and outer radius, producing a curved band between two angles.

Angles are measured in degrees, where 0° points straight up, and values increase clockwise.

**Parameters**

- `arc(x, y, inner, outer, from, to)`
    - `x, y`: Position of the centre point
    - `inner`: Inner radius
    - `outer`: Outer radius
    - `from`: Start angle (degrees)
    - `to`: End angle (degrees)

**Returns**

- A new `shape` object

**Example code**

```python
def update():
  angle = io.ticks / 10
  arc = shape.arc(80, 60, 30, 40, angle + 30, angle + 150)
  screen.pen = color.cyan
  screen.shape(arc)
```

## pie
Creates a new shape representing a pie slice (think Pacman).

The slice is defined by an inner and outer radius.

Angles are measured in degrees, where 0° points straight up, and values increase clockwise.

**Parameters**

- `pie(x, y, r, f, t)`
    - `x, y`: Position of the centre point
    - `r`: Radius of the pie slice
    - `f`: Start angle
    - `t`: End angle

**Returns**

- A new `shape` object

**Example code**

```python
def update():
  a = io.ticks / 10
  p = shape.pie(80, 60, 40, a + 30, a + 150)
  screen.pen = color.green
  screen.shape(p)
```

## star
Creates a new shape representing a star.

**Parameters**

- `star(x, y, s, ro, ri)`
    - `x, y`: Position of the centre point
    - `s`: Number of points
    - `ro`: Outer radius (tip distance from centre)
    - `ri`: Inner radius (indent distance from centre)

**Returns**

- A new `shape` object

**Example code**

```python
def update():
  star = shape.star(80, 60, 7, 25, 40)
  screen.pen = color.latte
  screen.shape(star)
```

## line
Creates a new `shape` representing a line segment.

**Parameters**

- `line(x1, y1, x2, y2, w)`
    - `x1, y1`: Start position
    - `x2, y2`: End position
    - `w`: Line width

**Returns**

- A new `shape` object

**Example code**

```python
def update():
  line = shape.line(30, 30, 120, 80, 10)
  screen.pen = color.smoke
  screen.shape(line)
```

# Properties

## transform

A matrix transformation applied to this shape when it is drawn.

This allows shapes to be translated, rotated, scaled, or skewed without modifying the underlying path data. The transform is applied at render time, making it useful for animation and repositioning shapes efficiently.

```python
import math

def update():
  # define a unit square
  rectangle = shape.rectangle(-0.5, -0.5, 1, 1)

  # resize, rotate, and translate into the centre of the screen
  scale = math.sin(io.ticks / 1000) * 60
  rectangle.transform = mat3().translate(80, 60).rotate(io.ticks / 10).scale(scale)

  screen.pen = color.lime
  screen.shape(rectangle)
```

# Methods

## stroke
Returns a new shape representing the outline (stroke) of this shape.

Stroking is useful for drawing borders around filled shapes, creating hollow outlines, or generating thicker versions of existing geometry. The original shape is not modified — instead, `stroke()` produces a new shape that can be drawn like any other.

The supplied thickness controls where the outline is placed:

- If the thickness is positive, the stroke expands outward from the shape’s edge.
- If the thickness is negative, the stroke is applied inward, shrinking into the shape’s interior.

This makes it possible to create both outer borders and inset outlines depending on the effect you want.

**Parameters**

- `stroke(thickness)`
    - `thickness`: Thickness of the stroke in pixels

**Returns**

- A new shape object representing the stroked outline

**Example code**

```python
import math

def update():
  # create a star shape
  star = shape.star(80, 70, 7, 15, 25)

  # stroke it to a thickness of 10 pixels
  thickness = math.sin(io.ticks / 1000) * 8
  stroked = star.stroke(thickness)

  # draw the stroked star
  screen.pen = color.lime
  screen.shape(stroked)

  screen.pen = color.white
  screen.text(f"thickness {thickness:.1f} pixels", 5, 5)
```
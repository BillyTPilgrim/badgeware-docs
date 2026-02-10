---
title: mat3
summary: Defines 2D transformation matrices allowing shapes to be translated, rotated, scaled, or skewed during rendering.
icon: grid_3x3
publish: true
---
# Introduction
Matrices provide a unified way to handle transformations such as translation, rotation, scaling, and shearing. By combining these into a single matrix, you can apply complex transformations to vectors with a single operation.

Multiple transformations can also be chained together through matrix multiplication, making matrices both efficient and elegant tools for managing motion and geometry in 2D space.

# Methods
The following methods operate on matrices and return new matrices with the requested transformation applied. These methods can be chained to build up complex transformations.

## mat3()
Creates a new identity matrix.

## translate()
Returns a new matrix with a translation applied to the current matrix.

### Usage
- `.translate(x, y)`
    - `x, y`: Amount to translate by

## rotate()
Returns a new matrix with a rotation applied to the current matrix. The rotation angle is specified in degrees. To work in radians, use `rotate_radians` instead.

### Usage
- `.rotate(angle)`
    - `angle`: Rotation angle in degrees
- `.rotate_radians(angle)`
    - `angle`: Rotation angle in radians

## scale()
Returns a new matrix with a scale applied to the current matrix.

### Usage
- `.scale(x, y)`
    - `x, y`: Scale factors for the x and y axes

## multiply()
Returns a new matrix with another matrix multiplied with the current matrix.

### Usage
- `.multiply(m)`
    - `m`: The matrix to multiply with

## inverse()
Returns a new matrix that is the inverse of the current matrix. If the matrix is not invertible, the result is undefined.

# Reference

## Constructor
```python-raw
mat3() -> mat3
```

## Methods
```python-raw
inverse() -> mat3
multiply(m: mat3) -> mat3
rotate(angle: int|float) -> mat3
rotate_radians(angle: int|float) -> mat3
scale(x: int|float, y: int|float) -> mat3
translate(x: int|float, y: int|float) -> mat3
```
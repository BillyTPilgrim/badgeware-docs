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

## mat3
Creates a new identity matrix.

**Example code**

```code
t = mat3()
```

## translate
Returns a new matrix with a translation applied to the current matrix.

**Parameters**

- `translate(x, y)`
    - `x, y`: Amount to translate by

**Example code**

```code
t = mat3().translate(50, 20)
```

## rotate
Returns a new matrix with a rotation applied to the current matrix. The rotation angle is specified in degrees. To work in radians, use `rotate_radians` instead.

**Parameters**

- `rotate(angle)`
    - `angle`: Rotation angle in degrees

- `rotate_radians(angle)`
    - `angle`: Rotation angle in radians

**Example code**

```code
td = mat3().rotate(100)
tr = mat3().rotate_radians(0.2)
```


## scale
Returns a new matrix with a scale applied to the current matrix.

**Parameters**

- `scale(x, y)`
    - `x, y`: Scale factors for the x and y axes

**Example code**

```code
t = mat3().scale(2, 1)
```


## multiply
Returns a new matrix with another matrix multiplied with the current matrix.

**Parameters**

- `multiply(m)`
    - `m`: The matrix to multiply with

**Example code**

```code
t1 = mat3().scale(2, 1)
t2 = mat3().rotate(50)
t3 = t1.multiply(t2)
```


## inverse
Returns a new matrix that is the inverse of the current matrix.

If the matrix is not invertible, the result is undefined.

**Example code**

```code
t = mat3().scale(2, 1).rotate(20)
ti = t.inverse()
```

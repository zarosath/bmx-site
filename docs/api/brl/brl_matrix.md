---
id: brl.matrix
title: BRL.Matrix
sidebar_label: Introduction
---


Matrices are fundamental mathematical constructs used in various fields, such as computer graphics,
physics simulations, and linear algebra. They are especially useful for representing linear transformations,
including rotations, translations, and scaling operations. In the context of graphics and 3D geometry,
matrices are commonly used to manipulate vertices and objects in a scene, transforming their coordinates
and orientations.

A matrix is a rectangular array of numbers, organized into rows and columns. In BlitzMax,
`brl.matrix` provides several structs to work with matrices of different
sizes and types, such as [SMat2D](../../brl/brl.matrix/smat2d), [SMat3D](../../brl/brl.matrix/smat3d), [SMat4D](../../brl/brl.matrix/smat4d), [SMat2F](../../brl/brl.matrix/smat2f), [SMat3F](../../brl/brl.matrix/smat3f), [SMat4F](../../brl/brl.matrix/smat4f), [SMat2I](../../brl/brl.matrix/smat2i), [SMat3I](../../brl/brl.matrix/smat3i), and [SMat4I](../../brl/brl.matrix/smat4i).
The name of each struct indicates its size and the primitive type it uses: [Double](../../brl/brl.blitz/#double), [Float](../../brl/brl.blitz/#float), or [Int](../../brl/brl.blitz/#int).
In our examples, we'll focus primarily on the [Double](../../brl/brl.blitz/#double) structs, but the same concepts apply to the other
structs as well.

The following is a brief introduction to the three main types of matrices used in graphics and 3D geometry, which
BlitzMax provides structs for, along with a high-level overview of their applications:

## Types of Matrices

### 2x2 Matrices
A 2x2 matrix, represented by the [SMat2D](../../brl/brl.matrix/smat2d), [SMat2F](../../brl/brl.matrix/smat2f) and [SMat2I](../../brl/brl.matrix/smat2i) structs, is primarily used for 2D linear transformations,
such as rotation and scaling. These matrices can be applied to 2D vectors, transforming their
coordinates accordingly. For example, a 2x2 rotation matrix can be used to rotate a 2D vector
around the origin by a specified angle.

### 3x3 Matrices
3x3 matrices, represented by the [SMat3D](../../brl/brl.matrix/smat3d), [SMat3F](../../brl/brl.matrix/smat3f) and [SMat3I](../../brl/brl.matrix/smat3i) structs, are commonly used for 3D linear transformations that do
not involve translation, such as rotation and scaling. These matrices can be applied to 3D vectors,
transforming their coordinates without changing their position in the scene. For example, a 3x3 rotation
matrix can be used to rotate a 3D vector around a specific axis by a given angle.

The following example demonstrates the use of 3x3 matrices, by creating a simple solar system. The sun
is represented by a yellow circle, and the planets are represented by blue circles. The planets are
rotating around the sun, and their positions are calculated using 3x3 matrices.

```blitzmax
SuperStrict

Framework SDL.SDLRenderMax2D
Import brl.matrix

Graphics 800, 600, 0

Local sunX:Float = GraphicsWidth() / 2
Local sunY:Float = GraphicsHeight() / 2
Local sunRadius:Float = 50

Local planets:TPlanet[] = [New TPlanet(150, 20, 0.7), New TPlanet(200, 15, 0.5), New TPlanet(250, 10, 0.3)]

While Not KeyHit(KEY_ESCAPE)
    Cls

    ' Draw the sun
    SetColor(255, 255, 0)
    DrawOval(sunX - sunRadius, sunY - sunRadius, sunRadius * 2, sunRadius * 2)

    ' Draw the planets
    For Local planet:TPlanet = EachIn planets
        planet.Draw(sunX, sunY)
    Next

    Flip
Wend

Type TPlanet
    Field distance:Float
    Field radius:Float
    Field angle:Float
    Field speed:Float

    Method New(distance:Float, radius:Float, speed:Float)
        Self.distance = distance
        Self.radius = radius
        Self.speed = speed
    End Method

	Method Draw(centerX:Float, centerY:Float)
		' Calculate the planet's position
		Local planetMatrix:SMat3D = SMat3D.Identity()
		planetMatrix = planetMatrix.Rotate(angle)
		planetMatrix = planetMatrix.Translate(New SVec3D(distance, 0, 0))
		Local planetPos:SVec3D = planetMatrix.Apply(New SVec3D(0, 0, 1))
	
		' Draw the planet
		SetColor(0, 0, 255)
		DrawOval(Float(centerX + planetPos.x - radius), Float(centerY + planetPos.y - radius), radius * 2, radius * 2)
	
		' Update the angle for the next frame
		angle :+ speed
	End Method
End Type
```

### 4x4 Matrices
4x4 matrices, represented by the [SMat4D](../../brl/brl.matrix/smat4d), [SMat4F](../../brl/brl.matrix/smat4f) and [SMat4I](../../brl/brl.matrix/smat4i) structs, are the most versatile and widely used matrices in 3D
graphics. They can represent any combination of linear transformations, such as rotation, scaling,
and translation. A 4x4 matrix can be applied to a 3D vector, transforming its position, orientation,
and scale in the scene. For example, a 4x4 transformation matrix can be used to move an object in 3D space,
rotate it around a specific axis, and scale it uniformly or non-uniformly.

Creating a 3D-like effect in 2D can be achieved using simple orthogonal projection. In this example,
we will create a rotating cube using a 4x4 transformation matrix to apply rotation, scaling, and translation. 

```blitzmax
SuperStrict

Framework SDL.SDLRenderMax2D
Import brl.matrix

Graphics 800, 600, 0

Local cube:TCube = New TCube
Local angle:Float = 0

Local centerX:Int = GraphicsWidth() / 2
Local centerY:Int = GraphicsHeight() / 2

While Not KeyHit(KEY_ESCAPE)
    Cls

    ' Create the transformation matrix..
    ' Start with an identity matrix as the initial transformation.
    Local matrix:SMat4D = SMat4D.Identity()
    ' Move the object away from the camera along the Z-axis by a distance of 6 units.
    ' This translation ensures that the object is visible and centered in the view.
    matrix = matrix.Translate(New SVec3D(0, 0, -6))
    ' Rotate the object around the Z-axis (blue axis) by an angle scaled by 1.2.
    ' This rotation is applied first to achieve a "rolling" effect.
    matrix = matrix.Rotate(New SVec3D(0, 0, 1), angle * 1.2)
    ' Rotate the object around the Y-axis (green axis) by an angle scaled by 0.7.
    ' This rotation is applied after the first rotation, creating a "pitching" effect.
    matrix = matrix.Rotate(New SVec3D(0, 1, 0), angle * 0.7)
    ' Finally, rotate the object around the X-axis (red axis) by the base angle.
    ' This rotation is applied last, creating a "yawing" effect.
    matrix = matrix.Rotate(New SVec3D(1, 0, 0), angle)

    ' Draw cube
    cube.Draw(matrix, 100, centerX, centerY)

    Flip
    angle :+ 0.5
Wend

Type TCube
    Field vertices:Float[24] ' 8 vertices, each with 3 coordinates
    Field edges:Int[24] ' 12 edges, each with 2 vertex indices

    Method New()
        vertices = [ -1:Float, -1:Float, -1:Float, 1:Float, -1:Float, -1:Float, 1:Float, 1:Float, -1:Float, -1:Float, ..
					1:Float, -1:Float, -1:Float, -1:Float, 1:Float, 1:Float, -1:Float, 1:Float, 1:Float, 1:Float, ..
					1:Float, -1:Float, 1:Float, 1:Float ]
        edges = [ 0, 1, 1, 2, 2, 3, 3, 0, ..
                  4, 5, 5, 6, 6, 7, 7, 4, ..
                  0, 4, 1, 5, 2, 6, 3, 7 ]
    End Method

    Method Draw(matrix:SMat4D, scale:Float = 1, centerX:Int, centerY:Int)
        Local transformedVertices:Float[24]

		' Apply transformation and orthographic projection
		For Local i:Int = 0 Until 24 Step 3
			Local vertex:SVec3D = New SVec3D(vertices[i], vertices[i + 1], vertices[i + 2])
			vertex = matrix.Apply(vertex)
			transformedVertices[i] = centerX + (scale * vertex.x)
			transformedVertices[i + 1] = centerY - (scale * vertex.y)
		Next

		' Draw edges
		For Local i:Int = 0 Until 24 Step 2
			DrawLine(transformedVertices[edges[i] * 3], transformedVertices[edges[i] * 3 + 1],
					transformedVertices[edges[i + 1] * 3], transformedVertices[edges[i + 1] * 3 + 1])
		Next

    End Method
End Type
```

## Column-Major vs. Row-Major Matrices

In `brl.matrix`, we use column-major ordering for matrices. Column-major ordering is a convention
in which elements are stored column by column in memory. It is the opposite of row-major ordering, where
elements are stored row by row. This distinction is important when performing operations like matrix
multiplication or when applying transformations to vectors or other matrices.

Column-major ordering is commonly used in graphics programming and some mathematical libraries. In
this convention, when applying transformations, the order in which they are applied is reversed
compared to row-major ordering. This means that when you want to apply multiple transformations,
you should apply them in the reverse order that you would with row-major ordering.

For example, in column-major ordering, to rotate an object around the X, Y, and Z axes and then
translate it, you would first apply the translation, then the rotation around the Z axis, followed
by the rotation around the Y axis, and finally the rotation around the X axis.

## Determinant, Inverse, and Transpose in Matrices

### Determinant

The determinant is a scalar value associated with square matrices that carries essential
information about the matrix's properties. It has several important geometric interpretations
and applications in linear algebra.

In the context of 2D transformations, the determinant represents the area scaling factor when a
matrix is applied to a vector. If the determinant is positive, the transformation preserves the
orientation; if it's negative, the orientation is reversed. A determinant equal to zero indicates
that the transformation collapses the vector onto a lower-dimensional subspace (e.g., a line or a point),
making the matrix singular and non-invertible.

Mathematically, the determinant of a 2x2 matrix is calculated as follows:

```
|A| = |a  b|
      |c  d|

|A| = ad - bc
```

Here's an example illustrating how to calculate the determinant of a 2x2 matrix using the [SMat2D](../../brl/brl.matrix/smat2d) struct:

```blitzmax
SuperStrict

Framework BRL.StandardIO
Import BRL.Matrix

' Define matrix elements
Local a:Double = 3.0
Local b:Double = 2.0
Local c:Double = 5.0
Local d:Double = 4.0

' Create a 2x2 matrix
Local mat:SMat2D = New SMat2D(a, b, c, d)

' Calculate the determinant
Local det:Double = mat.Determinant()

' Print the determinant
Print "Determinant: " + det
```
When you run this example, it will create a 2x2 matrix with the specified elements,
calculate the determinant using the `Determinant()` method provided by [SMat2D](../../brl/brl.matrix/smat2d), and print the resulting
determinant value. In this case, the output will be:
```
Determinant: -2.0
```

For a 3x3 matrix, the determinant can be calculated using the following formula:
```
|A| = |a  b  c|
      |d  e  f|
      |g  h  i|

|A| = a(ei - fh) - b(di - fg) + c(dh - eg)
```

Here's an example illustrating how to calculate the determinant of a 3x3 matrix using the [SMat3D](../../brl/brl.matrix/smat3d) struct:
```blitzmax
SuperStrict

Framework BRL.StandardIO
Import BRL.Matrix

' Define matrix elements
Local a:Double = 1.0
Local b:Double = 2.0
Local c:Double = 3.0
Local d:Double = 4.0
Local e:Double = 5.0
Local f:Double = 6.0
Local g:Double = 7.0
Local h:Double = 8.0
Local i:Double = 9.0

' Create a 3x3 matrix
Local mat:SMat3D = New SMat3D(a, b, c, d, e, f, g, h, i)

' Calculate the determinant
Local det:Double = mat.Determinant()

' Print the determinant
Print "Determinant: " + det
```

When you run this example, it will create a 3x3 matrix with the specified elements,
calculate the determinant using the `Determinant()` method provided by [SMat3D](../../brl/brl.matrix/smat3d), and print the
resulting determinant value. In this case, the output will be:
```
Determinant: 0.0
```

### Inverse

The inverse of a matrix is another matrix that, when multiplied with the original matrix,
results in the identity matrix. In other words, for a matrix A, its inverse A⁻¹ exists if
and only if A * A⁻¹ = A⁻¹ * A = I, where I is the identity matrix. Not all matrices have
inverses; only square matrices (i.e., matrices with the same number of rows and columns) may
have inverses, and even then, not all square matrices are invertible.

For a 2x2 matrix:
```
| a  b |
| c  d |
```
The inverse can be calculated using the following formula:
```
1 / (ad - bc) * |  d  -b |
                | -c   a |
```

The term (ad - bc) is the determinant of the matrix. If the determinant is zero, the matrix
is singular and does not have an inverse.

For a 3x3 matrix, the process is more complicated and involves finding the matrix of minors,
the matrix of cofactors, and finally, the adjugate matrix. The inverse can then be computed
by dividing the adjugate matrix by the determinant of the original matrix.

Here is an example for finding the inverse of a 2x2 matrix using the [SMat2D](../../brl/brl.matrix/smat2d) struct:
```blitzmax
SuperStrict

Framework brl.standardio
Import brl.matrix

' Create a 2x2 matrix
Local a:Double = 3
Local b:Double = 2
Local c:Double = 5
Local d:Double = 3

Local mat:SMat2D = New SMat2D(a, b, c, d)

' Calculate the inverse
Local matInverse:SMat2D = mat.Invert()

' Print the original and inverse matrices
Print "Original matrix:~n" + mat.ToString()
Print "Inverse matrix:~n" + matInverse.ToString()
```
When you run this example, it will output the original 2x2 matrix and its inverse:
```
Original matrix:
3.000 2.000
5.000 3.000
Inverse matrix:
-1.000 0.667
 1.667 -1.000
```


And here is an example for finding the inverse of a 3x3 matrix using the [SMat3D](../../brl/brl.matrix/smat3d) struct:
```blitzmax
SuperStrict

Framework brl.standardio
Import brl.matrix

' Create a 3x3 matrix
Local a:Double = 1
Local b:Double = 2
Local c:Double = 3
Local d:Double = 0
Local e:Double = 1
Local f:Double = 4
Local g:Double = 5
Local h:Double = 6
Local i:Double = 0

Local mat:SMat3D = New SMat3D(a, b, c, d, e, f, g, h, i)

' Calculate the inverse
Local matInverse:SMat3D = mat.Invert()

' Print the original and inverse matrices
Print "Original matrix: " + mat.ToString()
Print "Inverse matrix: " + matInverse.ToString()
```
When you run this example, it will output the original 3x3 matrix and its inverse:
```
Original matrix:
 1.000 2.000 3.000
 0.000 1.000 4.000
 5.000 6.000 0.000
Inverse matrix:
 -24.000 18.000 5.000
 20.000 -15.000 -4.000
 -5.000 4.000 1.000
```

### Transpose

The transpose of a matrix is a new matrix obtained by interchanging its rows and columns.
In other words, the transpose of a matrix A is a new matrix Aᵀ, where the element Aᵀ[i, j] = A[j, i].
The transpose operation has several important properties and applications in linear algebra, including
simplifying matrix equations and working with symmetric matrices.

For a 2x2 matrix A:
```
A = |a  b|       Aᵀ = |a  c|
    |c  d|            |b  d|
```

For a 3x3 matrix A:
```
A = |a  b  c|       Aᵀ = |a  d  g|
    |d  e  f|            |b  e  h|
    |g  h  i|            |c  f  i|
```

Here's an example illustrating how to calculate the transpose of a 2x2 matrix using the [SMat2D](../../brl/brl.matrix/smat2d) struct.
```blitzmax
SuperStrict

Framework brl.standardIO
Import brl.matrix

' Define matrix elements
Local a:Double = 1.0
Local b:Double = 2.0
Local c:Double = 3.0
Local d:Double = 4.0

' Create a 2x2 matrix
Local mat:SMat2D = New SMat2D(a, b, c, d)

' Calculate the transpose
Local matTranspose:SMat2D = mat.Transpose()

' Print the original and transposed matrices
Print "Original matrix:~n" + mat.ToString()
Print "Transposed matrix:~n" + matTranspose.ToString()
```
When you run this example, it will create a 2x2 matrix with the specified elements, calculate
the transpose using the Transpose() method provided by the [SMat2D](../../brl/brl.matrix/smat2d) struct, and print the original
and transposed matrices. In this case, the output will be:
```
Original matrix:
1.0,  2.0
3.0,  4.0
Transposed matrix:
1.0  3.0
2.0  4.0
```

And an example for calculating the transpose of a 3x3 matrix using the [SMat3D](../../brl/brl.matrix/smat3d) struct.
```blitzmax
SuperStrict

Framework brl.standardIO
Import brl.matrix

' Define matrix elements
Local a:Double = 1.0
Local b:Double = 2.0
Local c:Double = 3.0
Local d:Double = 4.0
Local e:Double = 5.0
Local f:Double = 6.0
Local g:Double = 7.0
Local h:Double = 8.0
Local i:Double = 9.0

' Create a 3x3 matrix
Local mat:SMat3D = New SMat3D(a, b, c, d, e, f, g, h, i)

' Calculate the transpose
Local matTranspose:SMat3D = mat.Transpose()

' Print the original and transposed matrices
Print "Original matrix: " + mat.ToString()
Print "Transposed matrix: " + matTranspose.ToString()
```

When you run this example, it will create a 3x3 matrix with the specified elements,
calculate the transpose using the Transpose() method provided by the [SMat3D](../../brl/brl.matrix/smat3d) struct, and print the
original and transposed matrices. In this case, the output will be:
```
Original matrix:
1.0  2.0  3.0
4.0  5.0  6.0
7.0  8.0  9.0
Transposed matrix:
1.0  4.0  7.0
2.0  5.0  8.0
3.0  6.0  9.0
```


Understanding the transpose concept and its properties is important when working with matrices
in your BlitzMax projects. The transpose operation can help simplify matrix equations and work with
symmetric matrices, among other applications. By transposing a matrix, you can manipulate its elements
and apply various transformations as required in your project.

## Scaling, rotation, and shearing matrices

Scaling, rotation, and shearing matrices are fundamental linear transformations used in various fields, such as
computer graphics, physics simulations, and geometric modeling. These transformations can be applied to vectors or points
to change their position, orientation, or shape. 

### Scaling matrices

Scaling is the process of resizing an object uniformly or non-uniformly along its axes. Scaling matrices are diagonal matrices
that have scaling factors along the diagonal elements, and zeros elsewhere.

For 2D scaling:

```
| sx  0  |
| 0   sy |
```

For 3D scaling:
```
| sx  0   0  |
| 0   sy  0  |
| 0   0   sz |
```

Here, sx, sy, and sz are the scaling factors along the x, y, and z axes, respectively. To apply the scaling transformation, you multiply
the scaling matrix by the vector or point you want to scale.

### Rotation matrices

Rotation is the process of rotating an object around a point or an axis. Rotation matrices are used to represent the rotation
transformations in both 2D and 3D spaces.

For 2D rotation around the origin by angle θ:

```
| cos(θ)  -sin(θ) |
| sin(θ)   cos(θ) |
```

For 3D rotation around an arbitrary axis (x, y, z) by angle θ, the rotation matrix is more complex. One common method is using
the axis-angle representation, which involves the Rodrigues' rotation formula. The formula for the 3D rotation matrix is as follows:

```
R = I + sin(θ) * K + (1 - cos(θ)) * K^2
```

Where `I` is the identity matrix, `θ` is the angle of rotation, and `K` is the skew-symmetric matrix representation of the axis vector.

### Shearing matrices

Shearing is the process of transforming an object by displacing its points along one axis based on the displacement of points along a
parallel axis. Shearing matrices are used to represent these transformations.

For 2D shearing:

```
| 1  shx |
| shy  1 |
```

For 3D shearing along the x-axis:

```
| 1  shy  shz |
| 0   1    0  |
| 0   0    1  |
```

And similar matrices can be defined for shearing along the y-axis and z-axis. Here, `shx`, `shy`, and `shz` represent the shearing factors.

When applying these transformations, it's important to consider the order of operations, as matrix multiplication is not commutative.
Typically, scaling and shearing are applied first, followed by rotation, and finally translation. Also, remember that these matrices should
be applied to homogeneous coordinates for translation to work correctly. In 3D graphics, this often involves using 4x4 matrices with an
additional row and column for the homogeneous coordinate.

## Matrix multiplication

Matrix multiplication is an essential operation in linear algebra and has numerous applications in computer graphics, physics, and other fields.
Multiplying two matrices involves specific rules, and it's important to understand the dimensions of the matrices and how to perform the
multiplication element-wise.

### Rules for Matrix Multiplication

Given two matrices A and B, the product AB can only be computed if the number of columns in A is equal to the number of rows in B.
If matrix A has dimensions m × n and matrix B has dimensions n × p, then the product AB will have dimensions m × p.

`(AB)_ij = A_i1 * B_1j + A_i2 * B_2j + ... + A_in * B_nj`

where (AB)_ij represents the element in the i-th row and j-th column of the resulting matrix AB.

### Element-wise matrix multiplication

To multiply two matrices element-wise, follow these steps:

1. Verify that the dimensions of the matrices are compatible for multiplication. The number of columns in matrix A must be equal
to the number of rows in matrix B.

2. Create a new matrix C with dimensions m × p, where m is the number of rows in matrix A and p is the number of columns in matrix B.

3. For each element (i, j) in the resulting matrix C, calculate the value as the sum of the products of the corresponding row elements in matrix A
and the column elements in matrix B:
`C_ij = A_i1 * B_1j + A_i2 * B_2j + ... + A_in * B_nj`
Iterate through all elements in matrix C, computing their values using the above formula.

4. The resulting matrix C represents the product of matrices A and B.

It's important to note that matrix multiplication is not commutative, meaning that AB ≠ BA in general. However, it is
associative (A(BC) = (AB)C) and distributive over addition (A(B+C) = AB + AC).

The `CompMul()` method can be used to perform element-wise matrix multiplication.

### Matrix multiplication using the dot product

Matrix multiplication is a fundamental operation in linear algebra, and it is performed using the dot product.
It is important to understand that matrix multiplication is not the same as element-wise multiplication. In matrix
multiplication, the dot product of the rows of the first matrix and the columns of the second matrix is used to calculate the resulting matrix elements.

Consider two matrices, A and B, where A has dimensions m x n (m rows and n columns), and B has dimensions p x q (p rows and q columns).
For matrix multiplication to be possible, the number of columns in A (n) must be equal to the number of rows in B (p). The resulting
matrix, C, will have dimensions m x q (m rows and q columns).

Here is the formula for matrix multiplication using the dot product:

C[i][j] = Σ (A[i][k] * B[k][j]) for k = 0 to n-1

where i ranges from 0 to m-1 and j ranges from 0 to q-1.

In other words, each element in the resulting matrix C is the dot product of the i-th row of matrix A and the j-th column of matrix B.

Let's see an example with two 3x3 matrices:

```
A = | a b c |     B = | p q r |
    | d e f |         | s t u |
    | g h i |         | v w x |

C = | (a*p + b*s + c*v) (a*q + b*t + c*w) (a*r + b*u + c*x) |
    | (d*p + e*s + f*v) (d*q + e*t + f*w) (d*r + e*u + f*x) |
    | (g*p + h*s + i*v) (g*q + h*t + i*w) (g*r + h*u + i*x) |
```

The `Operator *()` method can be used to perform matrix multiplication using the dot product.

### Vector Multiplication

Matrix-vector multiplication is a fundamental operation in linear algebra, computer graphics, and many other fields. This operation
is used to apply transformations, such as scaling, rotation, and translation, to points and vectors in 2D and 3D space. In this section,
we will discuss how to multiply a matrix by a vector and how this operation can be applied to 2D and 3D vectors in BlitzMax.

#### 2D Vectors ( [SVec2D](../../brl/brl.vector/svec2d), [SVec2F](../../brl/brl.vector/svec2f), [SVec2I](../../brl/brl.vector/svec2i) )

To multiply a 2x2 matrix by a 2D vector, perform the following steps:

1. Multiply each element of the first row of the matrix by the corresponding element of the vector.
2. Sum the results from step 1 to obtain the first element of the resulting vector.
3. Multiply each element of the second row of the matrix by the corresponding element of the vector.
4. Sum the results from step 3 to obtain the second element of the resulting vector.

Mathematically, this can be represented as:

```
| a c | | x |   | a * x + c * y |
| b d | | y | = | b * x + d * y |
```

#### 3D Vectors ( [SVec3D](../../brl/brl.vector/svec3d), [SVec3F](../../brl/brl.vector/svec3f), [SVec3I](../../brl/brl.vector/svec3i) )

To multiply a 3x3 matrix by a 3D vector, follow these steps:

1. Multiply each element of the first row of the matrix by the corresponding element of the vector.
2. Sum the results from step 1 to obtain the first element of the resulting vector.
3. Multiply each element of the second row of the matrix by the corresponding element of the vector.
4. Sum the results from step 3 to obtain the second element of the resulting vector.
5. Multiply each element of the third row of the matrix by the corresponding element of the vector.
6. Sum the results from step 5 to obtain the third element of the resulting vector.

Mathematically, this can be represented as:

```
| a d g | | x |   | a * x + d * y + g * z |
| b e h | | y | = | b * x + e * y + h * z |
| c f i | | z |   | c * x + f * y + i * z |
```

You can use the `Apply()` method to multiply a matrix by a vector.

Here's an example:

```blitzmax
Local mat2D:SMat2D = SMat2D.Identity()
Local vec2D:SVec2D = New SVec2D(2, 3)
Local result2D:SVec2D = mat2D.Apply(vec2D)

Local mat3D:SMat3D = SMat3D.Identity()
Local vec3D:SVec3D = New SVec3D(2, 3, 4)
Local result3D:SVec3D = mat3D.Apply(vec3D)
```

## The Identity Matrix

The identity matrix is a special square matrix that has ones on the diagonal and zeros elsewhere. In mathematical notation,
an identity matrix of size n x n is denoted as Iₙ. Here's an example of a 3x3 identity matrix:

```
I₃ = | 1 0 0 |
     | 0 1 0 |
     | 0 0 1 |
```

The identity matrix plays a crucial role in matrix algebra, as it serves as the "neutral element" for matrix multiplication. When you
multiply any matrix by the identity matrix, the result is the original matrix unchanged. Mathematically, this property can be written as:

A * Iₙ = A

Iₙ * A = A

where A is any n x n matrix.

This property is analogous to the number 1 in scalar multiplication. Just as multiplying any number by 1 leaves the number unchanged
(e.g., 7 * 1 = 7), multiplying any matrix by the identity matrix leaves the matrix unchanged.

The identity matrix is also important for other matrix operations. For example, when calculating the inverse of a matrix, the goal is to
find a matrix B such that A * B = Iₙ. When this condition is met, matrix B is the inverse of matrix A.

The `Identity()` method allows you create an identity matrix.

## Linear Transformations

Matrices are often used to represent linear transformations in various fields, including computer graphics and linear algebra.
A linear transformation is a function that maps vectors from one vector space to another while preserving the operations of vector
addition and scalar multiplication. In this section, we will discuss some of the key properties of linear transformations and how
matrices can be used to represent them.

### Linearity

A linear transformation, T, has the property of linearity, which means it satisfies the following conditions:

1. T(u + v) = T(u) + T(v) for all vectors u and v.
2. T(c * u) = c * T(u) for all vectors u and any scalar c.

This property ensures that the transformation maintains the structure of the vector space, preserving
parallelism and proportions between the vectors.

### Invertibility

A linear transformation is invertible if there exists another transformation, called its inverse, that can undo the effect of the original
transformation. In terms of matrices, a matrix A is invertible if there exists a matrix B such that AB = BA = I, where I is the identity matrix.
Invertible transformations are essential in many applications, as they allow us to reverse the effects of a transformation and recover the original data.

### Effect on Geometry

Linear transformations can have various effects on the geometry of shapes, including:

1. Scaling: A transformation that changes the size of a shape, either uniformly or non-uniformly along different axes.
2. Rotation: A transformation that rotates a shape around a specified point or axis.
3. Shearing: A transformation that distorts a shape by shifting its points along one axis relative to the other axis.

## Orthogonal and Orthonormal Matrices

Orthogonal and orthonormal matrices are special types of square matrices that have unique properties, making them particularly useful in
various applications such as computer graphics, physics, and linear algebra.

### Orthogonal Matrices

A matrix A is called an orthogonal matrix if its transpose, A^T, is also its inverse, i.e., A^T * A = A * A^T = I, where I is the identity
matrix. In other words, the columns (and rows) of an orthogonal matrix are orthogonal to each other, meaning they form a 90-degree angle
and their dot product is zero.

The geometric interpretation of orthogonal matrices is that they represent transformations that preserve lengths and angles, such as
rotations and reflections.

### Orthonormal Matrices

An orthonormal matrix is an orthogonal matrix where the columns (and rows) are not only orthogonal but also have unit length (i.e., they
are normalized). Orthonormal matrices simplify calculations in various applications, as they do not change the length of the vectors they transform.

### Applications

Orthogonal and orthonormal matrices play a significant role in computer graphics, as they are used to represent transformations that maintain
the geometry of 3D objects. For instance, they can be employed to create rotation matrices, which are essential for rotating objects in 3D space.

Here's a BlitzMax example demonstrating the use of an orthonormal matrix to represent a 3D rotation:

```blitzmax
Local angleX:Float = 30 ' in degrees
Local angleY:Float = 45 ' in degrees
Local angleZ:Float = 60 ' in degrees

' Create orthonormal rotation matrices for each axis
Local rotationMatrixX:SMat3D = SMat3D.RotationX(angleX)
Local rotationMatrixY:SMat3D = SMat3D.RotationY(angleY)
Local rotationMatrixZ:SMat3D = SMat3D.RotationZ(angleZ)

' Combine the rotations
Local rotationMatrix:SMat3D = rotationMatrixX * rotationMatrixY * rotationMatrixZ

' Apply the rotation to a 3D vector
Local vec:SVec3D = New SVec3D(1, 2, 3)
Local rotatedVec:SVec3D = rotationMatrix.Apply(vec)
```

When the rotation matrix is applied to a 3D vector, it will rotate the vector according to the specified angles.
The order in which the rotations are combined (in this example, X, then Y, then Z) will determine the final rotation.


## Structs
| Struct | Description |
|---|---|
| [SMat2D](../../brl/brl.matrix/smat2d) | A 2x2 Matrix |
| [SMat3D](../../brl/brl.matrix/smat3d) | A 3x3 matrix. |
| [SMat4D](../../brl/brl.matrix/smat4d) | A standard 4x4 transformation matrix. |
| [SMat2F](../../brl/brl.matrix/smat2f) | A [Float](../../brl/brl.blitz/#float) backed 2x2 Matrix. |
| [SMat3F](../../brl/brl.matrix/smat3f) | A [Float](../../brl/brl.blitz/#float) backed 3x3 matrix. |
| [SMat4F](../../brl/brl.matrix/smat4f) | A standard [Float](../../brl/brl.blitz/#float) backed 4x4 transformation matrix. |
| [SMat2I](../../brl/brl.matrix/smat2i) | An [Int](../../brl/brl.blitz/#int) backed 2x2 Matrix. |
| [SMat3I](../../brl/brl.matrix/smat3i) | An [Int](../../brl/brl.blitz/#int) backed 3x3 matrix. |
| [SMat4I](../../brl/brl.matrix/smat4i) | A standard [Int](../../brl/brl.blitz/#int) backed 4x4 transformation matrix. |


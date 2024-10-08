---
id: brl.vector
title: BRL.Vector
sidebar_label: Introduction
---


BlitzMax provides a set of vector structs for working with 2D, 3D, and 4D coordinates. These
structs come in three variations for each dimension: [Double](../../brl/brl.blitz/#double) ( [SVec2D](../../brl/brl.vector/svec2d), [SVec3D](../../brl/brl.vector/svec3d), [SVec4D](../../brl/brl.vector/svec4d)), 
[Float](../../brl/brl.blitz/#float) ( [SVec2F](../../brl/brl.vector/svec2f), [SVec3F](../../brl/brl.vector/svec3f), [SVec4F](../../brl/brl.vector/svec4f)), and [Int](../../brl/brl.blitz/#int) ( [SVec2I](../../brl/brl.vector/svec2i), [SVec3I](../../brl/brl.vector/svec3i), [SVec4I](../../brl/brl.vector/svec4i)).
The choice of struct depends on the precision and coordinate system you require for your application.

Vectors are used to represent positions, directions, and magnitudes in coordinate systems.

## 2D Vectors
2D vectors are represented by the [SVec2D](../../brl/brl.vector/svec2d), [SVec2F](../../brl/brl.vector/svec2f), and [SVec2I](../../brl/brl.vector/svec2i) structs, each catering to
different data types: [Double](../../brl/brl.blitz/#double), [Float](../../brl/brl.blitz/#float), and [Int](../../brl/brl.blitz/#int), respectively. These structs consist of two
components, x and y, which correspond to the horizontal and vertical coordinates in a 2D space.
They are commonly used in 2D games, graphical applications, and geometric calculations.

### 2D Games
In 2D games, vectors play an essential role in various aspects of game development,
providing a powerful and efficient way to handle mathematical operations related to game
objects such as characters, projectiles, and other interactive elements.

**Positions** : Vectors are used to represent the position of game objects in a 2D space.
The x and y components of the vector store the object's coordinates, which define its location
on the screen. By manipulating these coordinates, game objects can be moved, placed, or
arranged in relation to other objects and the game environment.

In this example, the playerPos and targetPos are 2D vectors ( [SVec2F](../../brl/brl.vector/svec2f)) representing the positions of
the player object and the target object. The player object is moved by manipulating the x and y
components of the playerPos vector based on the arrow keys pressed. We also check if the player
object has reached the target object by calculating the distance between their positions using the
[DistanceTo](../../brl/brl.vector/svec2d/#method-distancetodoublebsvec2d) method. If the player reaches the target, the target object is moved to a new random position
on the screen.

```blitzmax
SuperStrict

Framework sdl.sdlrendermax2d
Import brl.random

Graphics 800, 600, 0

Local playerPos:SVec2F = New SVec2F(400, 300)
Local targetPos:SVec2F = New SVec2F(Rand(50, 750), Rand(50, 550))

While Not KeyHit(KEY_ESCAPE)
    Cls

	Local x:Float = playerPos.x
	Local y:Float = playerPos.y

    ' Update player position using arrow keys
    If KeyDown(KEY_LEFT) Then x :- 5
    If KeyDown(KEY_RIGHT) Then x :+ 5
    If KeyDown(KEY_UP) Then y :- 5
    If KeyDown(KEY_DOWN) Then y :+ 5

    ' Set playerPos with updated coordinates.
	playerPos = New SVec2F(x, y)

    ' Draw player
    SetColor 0, 255, 0
    DrawOval(playerPos.x - 10, playerPos.y - 10, 20, 20)

    ' Draw target
    SetColor 255, 0, 0
    DrawOval(targetPos.x - 5, targetPos.y - 5, 10, 10)

    ' Check if player reached the target
    If playerPos.DistanceTo(targetPos) < 15
        ' Move target to a new random position
        targetPos = New SVec2F(Rand(50, 750), Rand(50, 550))
    EndIf

    Flip
Wend
```

**Velocities and Accelerations** : Vectors can represent not only positions but also
velocities and accelerations. In this context, the x and y components of the vector
indicate the rate of change of an object's position or velocity, respectively. By adding
velocity vectors to position vectors, game developers can create smooth and realistic motion
for characters and other moving objects. Similarly, acceleration vectors can be used to simulate
the effects of gravity, friction, and other forces on the motion of game objects.

In this example, the `ballPos`, `ballVel`, and ballAcc are 2D vectors ( [SVec2F](../../brl/brl.vector/svec2f)) representing the ball's position,
velocity, and acceleration. We update the ball's position and velocity based on the acceleration
(gravity) and time elapsed (`deltaTime`). When the ball hits the floor, we reverse its vertical
velocity and apply a bounce coefficient to simulate the loss of energy during the bounce.

```blitzmax
SuperStrict

Framework SDL.SDLRenderMax2D

Graphics 800, 600, 0

Local ballPos:SVec2F = New SVec2F(400, 100)
Local ballVel:SVec2F = New SVec2F(0, 0)
Local ballAcc:SVec2F = New SVec2F(0, 9.81) ' Gravity
Local ballRadius:Float = 20
Local floorY:Float = 550
Local deltaTime:Float = 1.0 / 60.0
Local bounceCoefficient:Float = 0.8

While Not KeyHit(KEY_ESCAPE)
    Cls

    ' Update ball velocity and position using acceleration and deltaTime
    ballVel = ballVel + (ballAcc * deltaTime)
    ballPos = ballPos + (ballVel * deltaTime)

    ' Bounce the ball off the floor
    If ballPos.y + ballRadius > floorY And ballVel.y > 0
		ballVel = New SVec2F(ballVel.x, -ballVel.y * bounceCoefficient)
		ballPos = New SVec2F(ballPos.x, floorY - ballRadius)
    EndIf

    ' Draw ball
    SetColor 0, 255, 0
    DrawOval(ballPos.x - ballRadius, ballPos.y - ballRadius, ballRadius * 2, ballRadius * 2)

    ' Draw floor
    SetColor 255, 255, 255
    DrawLine(0, floorY, 800, floorY)

    Flip
Wend
```

**Collision Detection and Resolution** : Vectors are crucial in detecting and resolving collisions
between game objects. By comparing the positions and sizes of objects, developers can identify when
two objects are overlapping or in close proximity, triggering a collision event. Vectors can also
be used to calculate the response of objects upon collision, such as bouncing, sliding, or sticking
together. This is achieved by reflecting or altering the velocity vectors of the colliding objects
based on their properties, such as mass, elasticity, and friction.

**Distances and Angles** : Vectors can be used to calculate distances and angles between objects,
which is useful for various game mechanics, such as determining the line of sight for a character
or measuring the range of a weapon. By subtracting the position vectors of two objects, developers
can find the vector connecting them and calculate its length to obtain the distance between them.
Additionally, the dot product and other vector operations can be used to determine the angle between
two vectors, which is helpful for calculating relative orientations or aiming projectiles.

In this example, the `characterPos` and `targetPos` are 2D vectors ( [SVec2F](../../brl/brl.vector/svec2f)) representing the
character's and target's positions. We calculate the connecting vector by subtracting the
character's position from the target's position. We then use the `Length()` method to find the
distance between them and the [ATan2](../../brl/brl.math/#function-atan2double-ydoublexdouble-)() function to calculate the angle. The distance and angle
are displayed on the screen.

```blitzmax
SuperStrict

Framework SDL.SDLRenderMax2D
Import BRL.Math

Graphics 800, 600, 0

Local characterPos:SVec2F = New SVec2F(100, 300)
Local targetPos:SVec2F = New SVec2F(700, 400)
Local distance:Float
Local angle:Float

While Not KeyHit(KEY_ESCAPE)
    Cls

    ' Calculate the vector connecting the character and the target
    Local connectingVector:SVec2F = targetPos - characterPos

    ' Calculate the distance between the character and the target
    distance = connectingVector.Length()

    ' Calculate the angle between the character and the target
    angle = ATan2(connectingVector.y, connectingVector.x)

    ' Draw character
    SetColor 255, 0, 0
    DrawOval(characterPos.x - 10, characterPos.y - 10, 20, 20)

    ' Draw target
    SetColor 0, 255, 0
    DrawOval(targetPos.x - 10, targetPos.y - 10, 20, 20)

    ' Draw aiming line
    SetColor 255, 255, 255
    DrawLine(characterPos.x, characterPos.y, targetPos.x, targetPos.y)

    ' Display distance and angle
    SetColor 255, 255, 255
    DrawText("Distance: " + distance, 10, 10)
    DrawText("Angle: " + angle, 10, 30)

    Flip
Wend
```

In summary, 2D vectors are indispensable tools in game development, providing an efficient and
flexible way to handle a wide range of mathematical operations related to game objects and their
interactions within the game world.

### Graphical Applications

In graphical applications, 2D vectors play a vital role in representing and manipulating various
elements in a coordinate system. They enable developers to perform operations such as translation,
rotation, and scaling, which are essential for rendering and adjusting graphics on the screen.

**Points** : 2D vectors are used to represent points in a coordinate system, where the x and y
components store the horizontal and vertical coordinates, respectively. Points are fundamental
building blocks in graphical applications, as they define the vertices of lines, polygons, and other shapes.

**Lines and Shapes** : By connecting points using vectors, developers can create lines, curves,
and shapes in a graphical application. Vectors are used to store the vertices of these shapes
and calculate properties such as their lengths, areas, and perimeters. Additionally, vector operations
can be applied to determine intersections, containments, and other spatial relationships between different shapes.

In this example, we define a function `CalculateLineLength` that takes two 2D points ( [SVec2D](../../brl/brl.vector/svec2d)) as
input, representing the start and end points of a line segment. The function calculates the
connecting vector between the two points and returns its length. 

```blitzmax
SuperStrict

Framework BRL.StandardIO
Import BRL.Vector

Local startPoint:SVec2D = New SVec2D(100, 300)
Local endPoint:SVec2D = New SVec2D(700, 400)

Local lineLength:Double = CalculateLineLength(startPoint, endPoint)

Print "Line length: " + lineLength

Function CalculateLineLength:Double(p1:SVec2D, p2:SVec2D)
	Local connectingVector:SVec2D = p2 - p1
	Return connectingVector.Length()
End Function
```

**Translation** : Translation is the process of moving a shape or a group of shapes from one location
to another within the coordinate system. By adding a translation vector to the position vectors of
the shape's vertices, developers can shift the shape by a specified amount in both horizontal and
vertical directions. This operation is essential for animating objects, panning scenes, or aligning
graphical elements.

**Rotation** : Rotation is the process of rotating a shape or a group of shapes around a specific
point in the coordinate system. By applying a rotation matrix, which can be derived from trigonometric
functions or complex numbers, to the position vectors of the shape's vertices, developers can rotate
the shape by a given angle. This operation is useful for creating effects such as spinning objects,
rotating scenes, or adjusting the orientation of graphical elements.

**Scaling** : Scaling is the process of resizing a shape or a group of shapes by a specified factor in
the coordinate system. By multiplying the position vectors of the shape's vertices by a scaling vector,
developers can uniformly or non-uniformly scale the shape in horizontal and vertical directions. This
operation is crucial for zooming in and out, resizing objects, or adapting graphical elements to different
screen resolutions or aspect ratios.

In conclusion, 2D vectors are essential in graphical applications, providing a robust and efficient way
to represent and manipulate various elements within a coordinate system. They enable developers to perform
operations such as translation, rotation, and scaling, which are crucial for rendering and adjusting
graphics on the screen.

### Geometric Calculations

Geometric calculations play an essential role in numerous fields, including computer graphics, physics
simulations, and engineering applications. 2D vectors are invaluable tools for solving problems related
to distances, intersections, and areas of shapes, as well as performing linear algebra operations like
dot products and matrix multiplications.

**Distances** : Calculating distances between points, lines, and shapes is a common task in geometry.
Using 2D vectors, developers can determine the distance between two points by finding the length of the
vector representing the difference between their position vectors. Distances from points to lines or other
shapes can also be computed using vector operations, such as projections and dot products.

**Intersections** : Detecting intersections between lines, rays, and shapes is essential for various
applications, including collision detection in games and ray tracing in computer graphics. Using 2D vectors,
developers can apply algebraic and geometric techniques to determine if and where two elements intersect.
For example, the intersection point of two lines can be found by solving a system of linear equations formed
by their parametric equations, which are derived from their direction vectors.

In this example, we define a `TLine` type that stores the start and end points of a line segment.
The function `CalculateIntersection` takes two TLine objects as input and calculates their intersection
point, if it exists. If the lines are parallel, the function returns [False](../../brl/brl.blitz/#false).

```blitzmax
SuperStrict

Framework BRL.StandardIO
Import BRL.Vector

Local line1:TLine = New TLine(New SVec2D(100, 100), New SVec2D(700, 400))
Local line2:TLine = New TLine(New SVec2D(400, 100), New SVec2D(200, 600))

Local intersection:SVec2D
Local result:Int = CalculateIntersection(line1, line2, intersection)

If result Then
	Print "Intersection point: " + intersection.ToString()
Else
	Print "Lines are parallel, no intersection"
End If

Type TLine
	Field startPoint:SVec2D
	Field endPoint:SVec2D
	
	Method New(startPoint:SVec2D, endPoint:SVec2D)
		self.startPoint = startPoint
		self.endPoint = endPoint
	End Method
End Type

Function CalculateIntersection:Int(line1:TLine, line2:TLine, intersection:SVec2D Var)
	Local v1:SVec2D = line1.endPoint - line1.startPoint
	Local v2:SVec2D = line2.endPoint - line2.startPoint
	
	Local crossProduct:Double = v1.x * v2.y - v1.y * v2.x
	If Abs(crossProduct) < 0.000001 Then
		Return False ' Lines are parallel
	End If
	
	Local t:Double = ((line1.startPoint.x - line2.startPoint.x) * v2.y - (line1.startPoint.y - line2.startPoint.y) * v2.x) / crossProduct
	
	intersection = line1.startPoint + v1 * t
	Return True
End Function
```

**Areas** : Computing the areas of shapes, such as triangles, polygons, and circles, often involves 2D
vectors. For instance, the area of a triangle can be calculated using the cross product of two of its edge
vectors, while the area of a polygon can be determined by summing the signed areas of its constituent
triangles. Additionally, 2D vectors can be used to calculate the moments of inertia and centroids of shapes,
which are essential properties in mechanics and engineering.

**Dot Products** : The dot product is a fundamental operation in linear algebra that takes two vectors as
input and returns a scalar value. In the context of 2D vectors, the dot product can be used to determine
the angle between two vectors, project one vector onto another, or check if two vectors are perpendicular.
Dot products are widely used in computer graphics for shading calculations and in physics simulations for
force computations and collision responses.

**Matrix Multiplications** : Matrix multiplication is another essential linear algebra operation that
involves 2D vectors when dealing with transformations in a two-dimensional space. By multiplying a
transformation matrix, such as a translation, rotation, or scaling matrix, with a position vector,
developers can apply the corresponding transformation to a point, line, or shape in the coordinate
system. Matrix multiplications are fundamental in computer graphics for rendering and animating scenes
and in physics simulations for updating the positions and orientations of objects.


## 3D Vectors
3D vectors, represented by the [SVec3D](../../brl/brl.vector/svec3d), [SVec3F](../../brl/brl.vector/svec3f), and [SVec3I](../../brl/brl.vector/svec3i) structs, have three components:
x, y, and z, corresponding to coordinates in 3D space. They are essential in various applications,
including 3D games, computer graphics, physics simulations, and engineering analyses. While some
of the uses of 3D vectors overlap with those of 2D vectors, they extend to more complex scenarios
and additional functionality.

**3D Games and Animation** : In 3D games, vectors are crucial for defining the positions, velocities,
accelerations, and orientations of game objects, such as characters, vehicles, and cameras.
They also play a vital role in game mechanics like collision detection, pathfinding, and
physics-based animations. In character animation, 3D vectors are used in skeleton-based systems
for defining joint positions and rotations, enabling realistic and expressive movements.

In this example, we define a `TGameObject` type with a position and velocity. The `Update` method is
used to update the game object's position based on its velocity and a given time step (`dt`).
It demonstrates moving the game object along a simple path using positions and velocities. The
game object moves towards each point in the pathPoints array and switches
to the next target when it gets close enough.

```blitzmax
SuperStrict

Framework BRL.StandardIO
Import BRL.Vector

Global pathPoints:SVec3D[] = [New SVec3D(0, 0, 0), New SVec3D(5, 10, 0), New SVec3D(10, 5, 0)]
Global currentTargetIndex:Int = 0

Local gameObject:TGameObject = New TGameObject(pathPoints[currentTargetIndex], New SVec3D(0, 0, 0))
Local dt:Double = 0.5

For Local i:Int = 1 To 30
	Local target:SVec3D = pathPoints[currentTargetIndex]
	Local direction:SVec3D = (target - gameObject.position).Normal()
	
	gameObject.velocity = direction * 1.5 ' Set the speed to 1.5 units per step
	gameObject.Update(dt)
	
	Print "Step: " + i + " | " + gameObject.ToString()
	
	If gameObject.position.DistanceTo(target) < 0.1 Then ' If the game object is close to the target, switch to the next target
		currentTargetIndex = (currentTargetIndex + 1) Mod pathPoints.Length
	End If
	
	Delay(200) ' Add a small delay for readability
Next

Type TGameObject
	Field position:SVec3D
	Field velocity:SVec3D
	
	Method New(position:SVec3D, velocity:SVec3D)
		self.position = position
		self.velocity = velocity
	End Method
	
	Method Update(dt:Double)
		self.position = self.position + self.velocity * dt
	End Method
	
	Method ToString:String()
		Return "Position: " + position.ToString() + " | Velocity: " + velocity.ToString()
	End Method
End Type
```

**Computer Graphics** : 3D vectors are the backbone of computer graphics, representing points,
lines, and polygons in 3D space. They facilitate transformations, such as translation, rotation,
and scaling, which are vital for rendering 3D models and scenes. In addition, 3D vectors are used
in lighting calculations and surface shading, enabling the creation of realistic materials and textures.

**Physics Simulations** : In physics simulations, 3D vectors are used to represent quantities like
force, torque, and momentum. They enable calculations of physical properties, such as the center of
mass and moments of inertia for rigid bodies. Moreover, 3D vectors are used in numerical methods like
the Verlet integration and the Runge-Kutta method for simulating the motion of objects under various
forces and constraints.

This example demonstrates a simple Verlet integration for simulating the motion of a falling particle
under gravity. The `TParticle` type contains position, previous position, and acceleration.
The `Integrate` method updates the particle's position using Verlet integration. We create a particle
and simulate its motion for 100 iterations, printing its position at each step.

```blitzmax
SuperStrict

Framework brl.standardio
Import BRL.Vector

Const deltaTime:Float = 0.01
Const gravity:Float = -9.8
Const numIterations:Int = 100

Local p:TParticle = New TParticle(New SVec3F(0, 10, 0))

For Local i:Int = 0 To numIterations - 1
	p.Integrate()
	Print "Iteration: " + (i + 1) + " Position: " + p.position.ToString()
Next

Type TParticle
	Field position:SVec3F
	Field previousPosition:SVec3F
	Field acceleration:SVec3F
	
	Method New(pos:SVec3F)
		position = pos
		previousPosition = pos
		acceleration = New SVec3F(0, gravity, 0)
	End Method
	
	Method Integrate()
		Local temp:SVec3F = position
		position = position * 2 - previousPosition + acceleration * deltaTime * deltaTime
		previousPosition = temp
	End Method
End Type
```

**Engineering Analyses** : 3D vectors play a significant role in engineering analyses, including structural,
fluid dynamics, and thermal analyses. They are used to represent stress and strain tensors in structural
mechanics, velocity and pressure fields in fluid dynamics, and temperature gradients in thermal analyses.
Additionally, 3D vectors facilitate the visualization and interpretation of complex engineering data,
like stress distributions and flow patterns.

**Geospatial Applications** : In geospatial applications, 3D vectors are used to represent positions and
displacements on Earth's surface and in its atmosphere. They facilitate calculations of distances, angles,
and areas on the curved surface, as well as transformations between various coordinate systems, such as
geographic, Cartesian, and spherical coordinates.

**Cross Products** : Unlike 2D vectors, 3D vectors have a unique operation called the cross product.
The cross product takes two input vectors and returns a new vector that is perpendicular to both input
vectors and has a magnitude proportional to the sine of the angle between them. Cross products are used
in numerous applications, including calculating surface normals, generating coordinate systems, and
determining the torque produced by a force.

3D vectors are essential in various applications, extending the functionality of 2D vectors to more complex
scenarios and additional operations. 

## 4D Vectors
4D vectors, represented by the [SVec4D](../../brl/brl.vector/svec4d), [SVec4F](../../brl/brl.vector/svec4f), and [SVec4I](../../brl/brl.vector/svec4i) structs, have four components: x, y, z,
and w. While they can indeed represent points in 4D space, their primary use is for homogeneous
coordinates in 3D graphics, along with other specialized applications.

**Homogeneous Coordinates** : In 3D graphics, 4D vectors are employed to represent homogeneous coordinates,
which enable affine transformations, such as translation, rotation, scaling, and perspective projection.
By adding the w component to a 3D point, 4D vectors allow for these transformations to be represented
by matrix multiplications, simplifying the overall process. Once the transformations are applied, the
resulting 4D vector can be converted back to a 3D point by dividing the x, y, and z components by
the w component.

In this example, we define a `TTransformableObject` type with a 4D vector position to represent
the object's position in homogeneous coordinates. The `ApplyTransformation` method applies a given
transformation matrix to the object's position using the `Apply` method of [SMat4D](../../brl/brl.matrix/smat4d). The `Get3DPosition`
method converts the 4D vector back to a 3D point by dividing the x, y, and z components by the w component.

We create a TransformableObject and use the [SMat4D](../../brl/brl.matrix/smat4d) struct functions `Translation`,
`Rotation`, and `Scaling` to create translation, rotation, and scaling matrices. We then apply
these transformations to the object and print its position after each transformation.

```blitzmax
SuperStrict

Framework BRL.StandardIO
Import BRL.Vector
Import BRL.Matrix

Local obj:TTransformableObject = New TTransformableObject(New SVec3D(2.0, 3.0, 4.0))

Local translationMatrix:SMat4D = SMat4D.Translation(New SVec3D(5.0, -2.0, 3.0))
Local rotationMatrix:SMat4D = SMat4D.Rotation(New SVec3D(0.0, 1.0, 0.0), 45.0)
Local scalingMatrix:SMat4D = SMat4D.Scaling(New SVec3D(2.0, 0.5, 1.5))

Print "Original Position: " + obj.ToString()

obj.ApplyTransformation(translationMatrix)
Print "After Translation: " + obj.ToString()

obj.ApplyTransformation(rotationMatrix)
Print "After Rotation: " + obj.ToString()

obj.ApplyTransformation(scalingMatrix)
Print "After Scaling: " + obj.ToString()


Type TTransformableObject
	Field position:SVec4D
	
	Method New(position:SVec3D)
		self.position = New SVec4D(position.x, position.y, position.z, 1.0)
	End Method
	
	Method ApplyTransformation(transformationMatrix:SMat4D)
		self.position = transformationMatrix.Apply(self.position)
	End Method
	
	Method Get3DPosition:SVec3D()
		Return New SVec3D(self.position.x / self.position.w, self.position.y / self.position.w, self.position.z / self.position.w)
	End Method
	
	Method ToString:String()
		Return "Position: " + Get3DPosition().ToString()
	End Method
End Type
```

**Perspective Projection** : Perspective projection is a crucial aspect of 3D rendering, as it creates
the illusion of depth on a 2D screen. 4D vectors facilitate this process by allowing the perspective
divide, which is a transformation that scales x, y, and z coordinates by the reciprocal of the w component.
This division produces the desired depth effect, with objects appearing smaller as they move further away
from the viewer.

**Quaternions** : 4D vectors can also represent quaternions, which are mathematical constructs used for
efficient 3D rotation calculations. In this context, the x, y, and z components correspond to the
imaginary part of the quaternion, and the w component corresponds to the real part. Quaternions are
particularly useful in computer graphics and robotics for avoiding gimbal lock, interpolating between
orientations, and maintaining numerical stability.

**Color Representation** : In computer graphics, 4D vectors can be used to represent colors with an
additional alpha channel for transparency. The x, y, z, and w components correspond to the red, green,
blue, and alpha values, respectively. This representation allows for color blending and modulation
operations to be performed using vector arithmetic.

**Splines and Curves** : 4D vectors can be employed to represent control points for splines and curves
in higher-dimensional spaces. For example, in computer-aided design (CAD) and computer graphics,
4D vectors can define control points for NURBS (Non-uniform rational B-spline) surfaces or Bézier
patches, enabling smooth and precise modeling of complex shapes.

**Hyperplanes** : In machine learning and computational geometry, 4D vectors can be used to represent
hyperplanes in 4D space. These hyperplanes can be employed for tasks like data classification,
clustering, or nearest-neighbor searches in four-dimensional data sets.

4D vectors have specialized uses, primarily in homogeneous coordinates for 3D graphics, along with
other applications such as perspective projection, quaternion representation, color representation,
splines and curves, and hyperplanes. While sharing some similarities with 2D and 3D vectors,
4D vectors extend their functionality to accommodate unique scenarios and mathematical constructs.


## Structs
| Struct | Description |
|---|---|
| [SVec2D](../../brl/brl.vector/svec2d) | A 2-element structure that can be used to represent positions and directions in 2D-space. |
| [SVec3D](../../brl/brl.vector/svec3d) | A 3-element structure that can be used to represent positions and directions in 3D-space. |
| [SVec4D](../../brl/brl.vector/svec4d) | A 4-element structure. |
| [SVec2F](../../brl/brl.vector/svec2f) | A [Float](../../brl/brl.blitz/#float) backed 2-element structure that can be used to represent positions and directions in 2D-space. |
| [SVec3F](../../brl/brl.vector/svec3f) | A [Float](../../brl/brl.blitz/#float) backed 3-element structure that can be used to represent positions and directions in 3D-space. |
| [SVec4F](../../brl/brl.vector/svec4f) | A 4-element structure. |
| [SVec2I](../../brl/brl.vector/svec2i) | An [Int](../../brl/brl.blitz/#int) backed 2-element structure that can be used to represent positions and directions in 2D-space. |
| [SVec3I](../../brl/brl.vector/svec3i) | An [Int](../../brl/brl.blitz/#int) backed 3-element structure that can be used to represent positions and directions in 3D-space. |
| [SVec4I](../../brl/brl.vector/svec4i) | A 4-element structure. |


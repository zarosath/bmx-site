---
id: brl.quaternion
title: BRL.Quaternion
sidebar_label: Introduction
---


Quaternions are a mathematical concept that can be used to represent and manipulate 3D rotations in a
way that avoids some of the issues encountered with other methods, such as Euler angles and rotation
matrices. A quaternion is a four-dimensional complex number that can be written as `q = w + xi + yj + zk`,
where `w`, `x`, `y`, and `z` are real numbers, and `i`, `j`, and `k` are imaginary units with the property
`i^2 = j^2 = k^2 = ijk = -1`. In the context of 3D rotations, w represents the scalar (real) part,
while `x`, `y`, and `z` together form the vector (imaginary) part.

Quaternions have several advantages over other rotation representations:

1. **Compactness** : Quaternions only require four numbers to represent a rotation, while rotation matrices
require nine numbers.

2. **Numerical stability** : Quaternions can be easily normalized, which helps maintain numerical stability
during calculations.

3. **Interpolation** : Quaternions can be smoothly interpolated using a method called Spherical Linear
Interpolation (SLERP), which provides a more intuitive and accurate interpolation between rotations
than other methods.

4. **Avoiding Gimbal Lock** : Quaternions do not suffer from gimbal lock, a problem that occurs with
Euler angles when two of the three axes of rotation become aligned, resulting in a loss of one degree
of freedom. Gimbal lock is named after the mechanical gimbals used in early navigation systems,
where the alignment of two gimbals would cause the system to lose its ability to rotate freely in
all three dimensions. In the context of 3D rotations, gimbal lock can cause unexpected and undesirable
behavior, such as sudden jumps in rotation or the inability to perform certain rotations. Quaternions,
on the other hand, maintain their full range of motion without encountering gimbal lock, providing a
more robust and reliable method for representing and manipulating 3D rotations.

5. **Efficiency** : Quaternion operations, such as multiplication and inversion, are generally faster
than their matrix counterparts.

Despite these advantages, quaternions can be less intuitive to work with than Euler angles or rotation
matrices, and some operations, like extracting a specific axis of rotation, can be more complicated.
However, the benefits of using quaternions often outweigh these drawbacks, making them a popular choice
for many 3D applications, including computer graphics and game development.

In the following sections, we'll explore how to use the `SQuatD`, `SQuatF`, and `SQuatI` structs provided
by the `brl.quaternion` module in BlitzMax to work with quaternions, and how they can be applied to various
rotation tasks.

### Euler Angles
Euler angles are a set of three angles that describe the orientation of an object in
three-dimensional space. They represent a sequence of three rotations applied around specific
axes in a specified order. The angles are named after the Swiss mathematician Leonhard Euler,
who extensively studied the topic.

The three angles are often referred to as pitch, yaw, and roll:

1. **Pitch (θ)** : The rotation around the X-axis. It represents the up and down tilting motion,like nodding your head.
2. **Yaw (ψ)** : The rotation around the Y-axis. It represents the side-to-side turning motion, like shaking your head.
3. **Roll (ϕ)** : The rotation around the Z-axis. It represents the side-to-side tilting motion, like tilting your head.

Euler angles can be applied in different orders, such as XYZ, ZYX, or YXZ. The order in which the angles
are applied matters because rotation is not commutative. For example, rotating around the X-axis and
then the Y-axis will produce a different result than rotating around the Y-axis and then the X-axis.

While Euler angles are intuitive and easy to understand, they can be problematic in certain situations,
such as when they lead to gimbal lock. Quaternions are often used as an alternative to Euler angles
because they avoid gimbal lock and provide smoother interpolation between rotations.

## Basic Operations with Quaternions
### Creating Quaternions
To create a quaternion using the SQuatD class, you can use the constructor:

```blitzmax
Local quat:SQuatD = New SQuatD(x, y, z, w)
```
The `x`, `y`, `z`, and `w` parameters represent the components of the quaternion. You can also create a
quaternion from Euler angles or a rotation matrix using the provided functions:

```blitzmax
Local euler:SVec3D = New SVec3D(pitch, yaw, roll)
Local quatFromEuler:SQuatD = SQuatD.CreateFromEuler(euler)
Local quatFromMatrix:SQuatD = SQuatD.CreateFromRotation(mat)
```

### Multiplying Quaternions
To combine two rotations represented by quaternions, you can multiply them together:

```blitzmax
Local combinedQuat:SQuatD = quat1 * quat2
```

### Inverting Quaternions
Inverting a quaternion refers to finding the quaternion that represents the opposite (or inverse) rotation.
When you apply the inverse rotation to an object that has already been rotated by the original
quaternion, the object returns to its initial orientation.

A quaternion `q` is represented as `(x, y, z, w)`. Its inverse, denoted as `q_inv`,
is calculated by taking the conjugate of `q` and then normalizing the result. The conjugate of a
quaternion is obtained by negating the vector part (`x`, `y`, `z`) while keeping the scalar part
(`w`) the same. The normalization is necessary to ensure that the inverse quaternion has a
magnitude of 1, just like the original quaternion.

Mathematically, the inverse quaternion `q_inv` is computed as follows:

```
q_inv = (x, y, z, w)^* / ||(x, y, z, w)||^2
```
where `^*` denotes the conjugate and `|| ||` denotes the magnitude (or norm) of the quaternion.

In practice, you can use the Invert() method to find the inverse of a quaternion. Once you
have the inverse quaternion, you can apply it to an object to undo the rotation applied by
the original quaternion. This can be useful in situations where you need to reverse or cancel
out a previous rotation.

```blitzmax
Local invertedQuat:SQuatD = quat.Invert()
```

### Interpolating Between Quaternions
To smoothly interpolate between two rotations, you can use either linear interpolation
(Interpolate) or spherical linear interpolation (SphericalInterpolate). The main difference
between these two methods lies in how the interpolation is performed:

1. **Linear Interpolation (Interpolate)** : Linear interpolation is the simplest method, where
each component of the quaternion is linearly interpolated between the start and end values
based on the interpolation factor. This method is faster to compute but can result in
non-uniform motion and changes in speed during the interpolation. Linear interpolation does
not guarantee that the resulting interpolated quaternions have a unit length, so it's necessary
to normalize the result afterwards.

2. **Spherical Linear Interpolation (SphericalInterpolate)** : Spherical linear interpolation,
also known as *slerp*, takes into account the spherical geometry of quaternions. This method
interpolates along the shortest path on the quaternion's hypersphere, resulting in uniform
motion and constant speed during the interpolation. Slerp is more computationally expensive
than linear interpolation, but it provides smoother and more natural transitions between rotations.
Since slerp preserves the unit length of quaternions, there's no need to normalize the result.

In summary, linear interpolation is faster but can produce non-uniform motion and requires
normalization, while spherical linear interpolation provides smoother, more natural transitions
at the cost of increased computational complexity.

```blitzmax
Local interpolatedQuat:SQuatD = quat1.Interpolate(quat2, t)
Local slerpedQuat:SQuatD = quat1.SphericalInterpolate(quat2, t)
```

## Quaternion Applications
Quaternions can be used to perform various operations on 3D rotations, such as converting between
different rotation representations, rotating points or vectors, and generating rotation matrices.

### Converting Between Representations
To convert a quaternion to Euler angles:

```blitzmax
Local eulerAngles:SVec3D = quat.ToEuler()
```

To convert a quaternion to a rotation matrix:

```blitzmax
Local mat3:SMat3D = SQuatD.ToMat3(quat)
Local mat4:SMat4D = SQuatD.ToMat4(quat)
```

### Rotating Points or Vectors
To rotate a point or vector using a quaternion, you can first convert the quaternion to a
rotation matrix and then apply the matrix to the point or vector. This approach is advantageous
for several reasons:

1. **Compatibility** : Converting a quaternion to a rotation matrix ensures compatibility with other
systems or libraries that expect or require the use of matrices for transformations. This way, you
can seamlessly integrate quaternion-based rotations with existing matrix-based systems.

2. **Efficiency** : When you need to rotate multiple points or vectors, converting the quaternion to
a rotation matrix first and then applying the matrix to each point or vector can be more efficient.
This is because the conversion from quaternion to matrix is done once, and the resulting matrix can
be reused for all the subsequent rotations.

3. **Clarity** : For some users, working with matrices might be more intuitive or familiar than working
with quaternions. By converting the quaternion to a rotation matrix, you can leverage the familiarity
and ease of understanding associated with matrix-based transformations.

```blitzmax
Local rotationMatrix:SMat4D = SQuatD.ToMat4(quat)
Local rotatedPoint:SVec3D = rotationMatrix * point
```

### Generating Rotation Matrices

Quaternions can be used to generate rotation matrices that can be used in various transformations.
Here are some examples:

#### Applying Quaternion to a Matrix
```blitzmax
Local mat3:SMat3D = SQuatD.ToMat3(quat)
Local mat4:SMat4D = SQuatD.ToMat4(quat)
```

#### Creating Translation and Rotation Matrix
```blitzmax
Local translation:SVec3D = New SVec3D(x, y, z)
Local rotTransMatrix:SMat4D = SQuatD.RotTrans(quat, translation)
```

#### Creating Translation, Rotation, and Scaling Matrix
```blitzmax
Local translation:SVec3D = New SVec3D(x, y, z)
Local scaling:SVec3D = New SVec3D(scaleX, scaleY, scaleZ)
Local origin:SVec3D = New SVec3D(originX, originY, originZ)
Local rotTransOriginMatrix:SMat4D = SQuatD.RotTransOrigin(quat, scaling, origin)
```


## Structs
| Struct | Description |
|---|---|
| [SQuatD](../../brl/brl.quaternion/squatd) | A Quaternion. |
| [SQuatF](../../brl/brl.quaternion/squatf) | A [Float](../../brl/brl.blitz/#float) backed Quaternion. |
| [SQuatI](../../brl/brl.quaternion/squati) | An [Int](../../brl/brl.blitz/#int) backed Quaternion. |

## Enums
| Enum | Description |
|---|---|
| [ERotationOrder](../../brl/brl.quaternion/erotationorder) | The order in which to apply rotations. |


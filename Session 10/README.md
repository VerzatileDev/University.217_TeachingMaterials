# Session 10 - 3D Rotations

### Table of Contents

1. [Euler Angles](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/tree/master/Session%2010#euler-angles)
2. [Axis-Angle Representation](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/tree/master/Session%2010#axis---angle-representation)
3. [Rotation Matrices](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/tree/master/Session%2010#rotation-matrices)
4. [Quaternions](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/tree/master/Session%2010#quaternions)
5. [Homework](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/tree/master/Session%2010#homework)

In the previous tutorials, we have only rotated around one axis of rotation to rotate a 2D rigid body.

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%2010/Readme%20Pictures/2D%20rotation.JPG">
</p>

If we want to rotate in 3D space, we will have to rotate around all 3 axis of rotation. This can cause some issues when we use what is called Euler angles (which we currently are using). 

Rotations will look incorrect or not work at all. In this session, we will discuss the ways you can represent rotations and why some methods are better (or worse) than others.

For a reminder, this is what rotations around each object looks like for a right handed co-ordinate system.

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%2010/Readme%20Pictures/right%20handed%20coordinate%20rotation.JPG">
</p>

> This session is different to the others as it is not a guide on how to achieve these things - It is more a starting point for you to learn about the methods and then be able to research into them more yourself for the coursework. (Remember, part of university is actually researching things yourself!)

## Euler Angles

### Representation

In 2D rotation, we used a single float to orientate the rigid body in space. This is because we only needed rotation around a single axis (in this case, the +Z axis, which in turn, moved the vertices in the X-Y plane around the rigid body's center). This is classed as one rotational degree of freedom.

> A degree of freedom is some quantity that we can change independent of others.

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%2010/Readme%20Pictures/Z%20axis%20rotation.JPG">
</p>

For 3D rotation, we will need to use 3 angles to represent the orientation in all 3 axes of space: X, Y and Z. This is classed as three rotational degrees of freedom. As you can guess, this will take 3 floats.

These are called the roll, pitch and yaw.

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%2010/Readme%20Pictures/Explanation-of-roll-pitch-and-yaw-movements-of-a-gyroscope.png" height="250" width="250">
</p>

We would then rotate an object in each axis at a time by each of the 3 floats to get an end position. (We would need to stick to an order of rotation for everything or the same rotations in a different order give different end results!)


<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%2010/Readme%20Pictures/Order%20of%20Rotation.png">
</p>

### Advantages

The main advantage for using euler angles is that they work fine in 2D and that it only takes 3 floats worth of memory to hold the orientation information of an object. (As you will find out, this is the cheapest memory wise out of the methods.)

They are also the easiest to visualize and understand.

### Disadvantages 

The biggest problem with Euler angles is *Gimbal Lock*. This is when any of the angles becomes 90 or -90 meaning that the object has rotated an axis the same as another axis. These axis are now aligned. For example, if you use your hand to make a right handed co-ordinate system and then rotate your thumb (the X axis) 90 degrees, it will be the same as your middle finger (the Z axis).

This is sometimes a hard concept to grasp. Hopefully the below pictures show the issue of gimbal lock further. By having an axis lock, it will lower the rotational degrees of freedom from three to two, as moving either one of the same axes that are locked together will result in the same rotational movement.

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%2010/Readme%20Pictures/Gimbal-lock-When-the-pitch-Y-rotates-90-degrees-the-roll-X-and-yaw-Z-axes-become.png" height="200" width="600">
</p>

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%2010/Readme%20Pictures/On-the-left-a-normal-situation-the-three-gimbals-are-independent-On-the-right-the.png">
</p>

> From Ian Millington's Games Physics Engine Development
> _"In NASA’s Apollo moon program, to avoid the craft reaching gimbal lock and
finding it impossible to represent the orientation of the spacecraft, restrictions were
placed on the way that astronauts could control it. If the craft got too near gimbal
lock, a warning would sound. There was no physical reason why the craft couldn’t
orient in that way; it was purely a feature of the ability to measure and do calculations
with the orientation of the craft."_

Another issue is that concatenating (adding together) the angles of each axis together does not work too well in practice.

## Axis - Angle Representation

### Representation

Combinations of rotations can be represented as one rotation about a certain axis. This is 4 floats of information (1 float for the angle and 3 floats for the axis).

This gives us four degrees of freedom. However, the axis has to be normalized so it becomes only three degrees of freedom.

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%2010/Readme%20Pictures/axis%20angle.JPG">
</p>

### Advantages

We can lower this representation to 3 floats by using a "scaled axis representation" - Combining the axis and angle into a single 3D vector.

### Disadvantages 

We have to make sure the angle is always in the correct range of (-180, 180).

Manipulating this represention is not simple. It is unclear how we can concatenate or combine rotations easily as both the axis and the angle needs to change. Because of this, it is rarely used.

> One way to do this is to convert them to matrices, multiply them and convert it back to axis-angle. (But if we did this, we could just use matrices and save the converting back and forth trouble. The fight between space vs time as always!)

## Rotation Matrices

### Representation

Rotation matrices are 3x3 or 4x4 matrices that, when multipled with a point or a vector, result in the rotation of that point/vector about some axis to a new position.

You have seen these in action (and hopefully used them by now!) with 212CR's coursework and modern OpenGL (inside the vertex shader). By using a rotation matrix (called the model view matrix) you have moved vertices from model space to world space, ready to be displayed to the screen (via the projection matrix).

If it is a 3x3 matrix, it uses 9 floats. A 4x4 matrix uses 16 floats.

There are different rotation matrices based on which axis we wish to rotate on. As you can see, for each axis, the rotation matrix holds a 1 so that the vertices are not altered on that axis. (There is a unified matrix that handles all three axes at the same time.)

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%2010/Readme%20Pictures/rotation%20matrices.png">
</p>

### Advantages

We can combine rotations very easily in multiple axes as well as other operations such as translation and scale into one matrix via matrix multiplication. (Again, think of our model view matrix which contains both the needed model operations and the viewing operations.)

> Yes, there was a reason we went over these operations in Lecture 1!

To combine rotations, we have to combine them in reverse order. To rotate an object by rotation R1 and then rotate it by R2, we would have to code it as such: Ranswer = R2 * R1. (Don't believe me? Check your vertex shader to see which order you do the MVP multiplication to vertices.)

No need to bounds check the angle.

In games, we normally need a rotation matrix to send to the renderer to draw an object. We can use rotation matrices for physics as well and save on any extra effort.

### Disadvantages 

If we used Euler angles before, we've tripled (or more for 4x4 matrices) the amount of memory used to store rotational information of an object. This information is the same, in regards to rotation degrees of freedom, as using Euler angles.

The fact we have to combine rotations in reverse order always catches students out when they code it and then can't see the object or it's not in the right position.

The rotation matrix needs to keep orthogonal with a determinant of 1 so that each column in the matrix represents a unit vector, each at right angles to one another. Numerical errors such as roundoff and truncation cause this and can cause the rotational matrix to scale and translate the object as well as rotate it. (This can be checked with matrix transpose * matrix = identity matrix)

> If you are feeling up to it, you can calculate the initial rotation matrix from the orientation angles and never directly calculate it again (to save on slower sin and cos calculations). What would happen is that the forces/moments applied to the rigid body would apply changes to the angular velocity which in turn you would be multiplied by the current rotation matrix.
>
> If not, you can just use the orientation angles every frame to build a new rotation matrix but this would incur the computational cost of the multiple cos and sin uses.

## Quaternions

### Representation

A quaternion is a four dimensional quantity so it is made up of 4 components. As is normally written as:

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%2010/Readme%20Pictures/quat1.JPG">
</p>

(where q0 is now w and gx, gy, gz is now x, y, z)

(i, j and k are thought of as the standard basis for quaternions so are normally written as x, y,z. They are also all imaginary numbers (sqrt(-1)) which you may have touched upon in school mathematics lessons.)

As you can notice, we can make the x, y, z part a vector so a quaternion then becomes:

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%2010/Readme%20Pictures/quat2.JPG">
</p>

(where *v* is (x, y, z))

For rotation, *v* is the direction in which the axis of rotation points. w is the angle of rotation.

> For example, if we wish to rotate around a unit vector (What makes a vector a unit vector?) *u* with an angle of *a* we can calculate this via:

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%2010/Readme%20Pictures/quat3.JPG">
</p>

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%2010/Readme%20Pictures/quaternion%20rotation%20formula.JPG">
</p>

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%2010/Readme%20Pictures/quaternion%20rotation.JPG">
</p>

To rotate points/vectors, we can do the following:

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%2010/Readme%20Pictures/quat3.JPG">
</p>

(where q* is the conjugate)

We can also create a quaternion from a set of Euler angles and get a set of Euler angles from a quaternion, and convert a quaternion into a matrix.

> If you need to represent a vector as a quaternion, you can do so by making w = 0 and the vector as *v*.

### Advantages

4 floats are used to store the rotational motion, making them less memory heavy than rotational matrices and only a float more than using Euler angles. 

However, quaternions do not suffer from gimbal lock (which Euler angles do), are simple to concatenate (like rotational matrices) and rotate objects efficiently (no angle bounds checking needed and it uses less sin and cos calls than rotational matrices [for a singular axis at least]).

Interpolating orientation is done best with quaternions.

> Like mentioned above with rotational matrices, we can calculate the quaternion once at the start with the initial orientation needed and then only update (rather than recalculate a new quaternion) via the new orientation every frame, which would be the angular velocity.

### Disadvantages 
 
Trying to visualize (both on paper and in your head) 4D space is scary!

As quaternions have four degrees of freedom to represent only three degrees of rotation freedom we need to make the magnitude of the quaternion 1. This can be done with Pythagoras' theorem like vectors - Just with the w value added in as well.

While quaternions are good for rotations, they aren't for translations or scales.

## Homework
* Consider, research and implement one of the methods to allow your objects to rotate in 3D space for your coursework. (HINT: glm will help here. You have already used it for vectors and matrices.)

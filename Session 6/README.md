# Session 6 - Rotational Motion

#### Table of Contents
1. [What is rotational motion?](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/tree/master/Session%206#what-is-rotational-motion)
2. [What is a rigid body?](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/tree/master/Session%206#what-is-a-rigid-body)
3. [Making a 2D RigidBody class](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/tree/master/Session%206#making-a-2d-rigidbody-class)
4. [Making a rigid body move](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/tree/master/Session%206#making-a-rigid-body-move)
5. [Turning this into a 3D rigid body](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/tree/master/Session%206#turning-this-into-a-3d-rigidbody)
6. [Homework](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/tree/master/Session%206#homework)

Last session, we added linear motion into our scene with the use of Euler's method.

For this week's sessions, we will be implementing rotational motion as well as neatening up our current code base to give it a more "game engine" like design.

## What is rotational motion?

While linear motion moves an object in terms of it's position in space, rotational motion spins an object in regards to an origin point.

Some real life examples of rotational motion include a car engine transmitting power from the driveshaft and gearbox to the wheels so the car moves, adding spin to a golf shot to increase the distance or to stick the ball to the green, or a pitcher throwing a curve ball by adding spin to the throw.

To make our lives easier (which for games programming we always want to do!), we will consider rotational motion to happen about the object's center of mass. **Remember from the lecture - By doing this, we can seperate linear and rotatonal motion and treat them as seperate.**

In 2D, we can represent any object's motion with a position and an orientation (an angle rotated in a certain direction). For a 2D game on the X and Y axes, we rotate around 1 axis only - the Z axis. **Think back to the maths refresher in Lecture 1 with the hand gestures!** This is because we only have 1 degree of freedom. Of course, this will cause issues when doing 3D rigidbodies because we will need 3 degrees of freedom, which is not as easy as just using 3 orientation values!

By using both linear and rotational motion, we add another level of immersion to our games as objects react and interact (which we will see in the collision stuff later) with each other like they do in real life.

## What is a rigid body?

A rigid body is a system of particles that are absolutely fixed with respect to each other. A solid object can be thought of as a collection of an infinite number of particles. When we rotate a rigid body, all these particles move in a circular motion around the center of mass together.

> We cannot compute infinity.... Imagine trying to work out the movement for infinite particles a frame just to move a box!

In games, we normally assume that a rigid body is an object that will not deform in any way. What we mean by that is it's shape will not change (for example, moving of vertices, denting etc.) and that it's mass will not change. 

_(With mass changing, it is less of an issue as you can recalculate the mass per frame if needed. - If you are cool, you could do something like the following:- The player has rockets that, when fired, lower the mass of the overall player.)_

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%206/Readme%20Pictures/surprisedpikachu.png">
</p> 

## Making a 2D RigidBody class

While particles only had linear motion, rigid bodies have both linear and rotational motion. This means we will need all the variables for linear motion as well as different ones for rotational motion.

### The variables

> Write a list of the variables the _RigidBody2D_ class will need. Try this yourself first (and try to do the linear ones from memory instead of looking at your _Particle_ class!) and then look at the hidden list below. Make sure your understand what these variables are used for.

<br/>
<details>
  <summary> List of variables for a 2D Rigid Body </summary>
	
  | Linear Motion Variables | Rotational Motion Variables |
  | ------------- | ------------- |
  | mass | inertia |
  | linear acceleration | angular acceleration |
  | linear velocity |  angular velocity | 
  | position | orientation |
  | linear forces acting on it | angular forces/moments acting on it |
  | | Some kind of shape information |
</details>
<br/>

Once you have worked out the list of variables needed, we will need to think about what type each variable is. We did this last week for the linear variables and these will not change.

> Look at the hidden variable list above if you have not already. For each rotational variable, write down the type you think it should hold. Try if yourself before looking at the below hidden list. **Remember, this is for a 2D rigid body.**

<br/>
<details>
  <summary> Variable types for rotational motion </summary>
	
| Rotational Motion Variables | Type |
  | ------------- | ------------- |
  | inertia | float |
  | angular acceleration | vec3 |
  | angular velocity | vec3 |
  | orientation | float |
  | Some kind of shape information | Depends on the shape! |
  | angular forces/moments acting on it | vec3 |
</details>
<br/>

### Creating the class

As before with the _Particle_ class, we are now able to code this class. 

You've done this before so I believe in you to do it yourself! Because of this, I won't show the end result - Feel free to ask me in lab sessions to look over your implementation!

> Create a new class for 2D Rigid Bodies. This should:
> * Inherit from GameObject
> * Include all the variables discussed above.
> * Include and overwrite the functions from GameObject.
> * Include a constructor. What parameters should it contain? What would be the helpful variables to set up whenever you make an instance of this class?

In terms of displaying this on screen, this will differ depending on the shape used. This could be a circle, a square, a capsule etc. Each of these will have different drawing code.

> What else would be different for different objects?

### Drawing the class

In this example, we will draw a rectangle rigid body. I've added extra white space and indentation to make it easier for you to see how this works. **As always, this is using legacy OpenGL and is not enough to pass the 212CR module!**

The key take aways from this are:
* In the code I have typed the _glTranslatef_ line first and then the _glRotatef_ line. Behind the scenes, OpenGL is keeping the model matrix in memory and these 2 lines alter it by using the matrix mathematics we all know and love. Due to the ordering of matrix multiplication what really happens is the _rotation_ happens first and then it is _translated_ to the position needed.
	* Why do we do this? That's more a 212CR question but it is important so I'll answer it here. 
		* When we rotate in graphics, we rotate around the origin. If we were to translate first, we move away from that origin. If we then rotate, we rotate against the original origin which will give us the wrong result (aka the object is rotated around that point).
		* By rotating first, we rotate at the origin and the object rotates around its middle. We then translate that result to the correct position.
* I use the shape information (in this case, length and width for a rectangle) to draw a quad (4 points) to the screen. The z value is 0 but this could be changed depending on the camera position and target.
	* As the origin is moved due to the translate done above it in the code, I just use + and - values to make a quad 'around' the new origin.
	* I also draw a dot. This is at the middle of the object and is the rotation point for the graphics. When we rotate with our physics code, it will rotate around this point.
* The orientation variable is what our physics code will work out. This in turn will update the rotation of the graphical representation, showing rotational movement.
* _Still confused? Ask me for a demo on the board._

Make sure you make an instance of the rigid body in your scene and then call the _Draw_ and _Update_ functions in the needed places or you will not see anything as it does not exist!

```C++
void RigidBody2D::Draw()
{
	glPushMatrix(); //dont affect other objects, only this one so take a copy of the matrix and put it on the stack
		glTranslatef(position.x, position.y, position.z); //then this happens
		glRotatef(orientation, 0, 0, 1); //this really happens first
		glColor3f(1.f, 1.f, 1.f);

		glBegin(GL_QUADS);
			glVertex3f(-length, width, 0); //top left
			glVertex3f(length, width, 0); //top right
			glVertex3f(length, -width, 0); //bottom right
			glVertex3f(-length, -width, 0); //bottom left
		glEnd();

		glPointSize(5.0f); // so we can see the point better
		
		glColor3f(0.f, 0.f, 0.f);
		glBegin(GL_POINTS);
			//at the middle of object
			glVertex3f(0, 0, 0);
		glEnd();
	glPopMatrix(); //forget about what we've done to this object so push off the stack - we are back to before the glPushMatrix() happened
}
```

In this example below, the width and height are both 1 and the object has not been rotated.

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%206/Readme%20Pictures/Rigidbody.PNG">
</p> 

## Making a rigid body move

Now the class is set up, we can worry about making the rigid body move.

From the lecture, we know the following information.

Per frame/update, we need to:
* Reset the forces and moments to zero
* Identify and quantify all forces and moments currently acting on the rigid body
* Take vector sum for linear and angular movement
* Solve equations of motion for linear and angular accelerations
* Integrate with respect to time to find the linear and angular velocities
* Integrate again to find the linear and angular displacements

By adding the forces _and now the moments_, we can work out both the linear and rotational motion seperately.

### Linear motion

> Add a _CalculateForces_ function to the rigid body class. This does not return anything or take any arguments. 
> * This should reset the classes total force and total moment variables.
> * Add the needed areas for linear motion. (These are the same as for particles. You can test this with a gravity force.)

> Add an _Update_ function if you have not already. This does not return anything and takes a float argument for delta time. 
> * Call _CalculateForces_ at the top of this function.
> * Incorporate Euler's method into the rigidbody class. (This will be the same as for particles.)
> * Check the rigid body moves with gravity. This is only linear motion.

### Rotational motion

We will need to do a bit of work to get rotational motion working for our rigid body.

> * Work out the required inertia for the rigid body in it's constructor. This should be after you assign the shape information to it. For different object types, the formula for this will change.

We will also have to work out the torque the forces apply to the rigid body, because at the moment we only work out linear motion (even with the above changes).

#### Working out torque

> Look at the below picture. What 3 pieces of information do we need to know in order to work out the torque? Look at the hidden answer once you think of all 3.

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%206/Readme%20Pictures/torque%201.png">
</p> 

<br/>
<details>
  <summary> Answer </summary>

We need to know:
* The pivot point and the position the force is applied 
	* Allowing us to work out the distance from the pivot point and force
* The amount or strength of the force

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%206/Readme%20Pictures/torque%202.png">
</p> 

</details>
<br/>

Using this information we can use the torque formula in the _CalculateForces_ function to work out the total moment force present in that frame/update.

> As before in linear motion, we will use a variable to sum up all the moments acting on the rigid body each frame/update. 
> Add a variable to the class that will hold the total moments.

In _CalculateForces_, we will:
* Reset this total moment variable to zero.
* Add the torque forces one at a time to that variable to sum all the moments acting on the rigidbody.

> Add the above bullet points to your _CalculateForces_ function under the linear motion section.
> **Any force applied for linear motion also needs to be applied for rotational motion so each force needs a position where it is affecting the ridigbody.**

Before, we just updated the force value and used that. Because each force now needs a position, we will have to add that information into our class. There are two ways to do this:
* World Co-ordinates - The position is based off the world's origin point at (0,0,0)
* Local / Body Co-ordinates - The position is based off another object's origin.

> What do you think are the advantages and disadvantages of using either of these?

For this tutorial, we will use body co-ordinates because it makes the torque calculation easier for us. This means that the force positions we add in are **relative** to the object and not the world origin. 

For example, the object is at position (0, 10, 0) and the force's relative position is at (0, 1, 0). This means that the force is really at (0, 11, 0). 

If we move the object to (3, 5, 0), the force's relative position does not change (so it is still at (0, 1, 0)), but the force's actual position is now at (3, 6, 0).

If the torque formula is _**torque = (distance from pivot and force position) x (force value)**_, we can use this relative position as the distance from the pivot easily.

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%206/Readme%20Pictures/torque%20formula.JPG">
</p> 

> Add a force position variable of (1, 0, 0) and a force value variable of (2, 0, 0) into your rigid body.
> Use the force value variable in the _CalculateForces_ function to update linear motion.
> Use the torque formula and the above variables in the _CalculateForces_ function to update rotational motion. (Hint: GLM has a function for the x part!)

We then need to calculate acceleration, velocity and orientation based off these summed force inside the _Update_ function.

> * Work out the angular acceleration using the _F = ma_ formula inside the _Update_ function, using the new total moment variable.
> * Use Euler's to then work out angular velocity and then orientation. Remember to take into account delta time.
>	* We will want to dampen the angular velocity (like we did for linear motion so it does not keep moving forever).
> **As the rigid body is 2D but the variables we use are 3D, we will only use the z axis of angular velocity for the orientation.**	

> Now this is done, test your code out by running it (Turn off any extra forces like gravity etc. if you added them earier). What happens? (If it is nothing, you've done something wrong!

<br/>
<details>
  <summary> Answer </summary>
The rigidbody should move in the x axis only. There is no rotation / angular motion.... Why?
	<br/>
	<details>
	  <summary> Answer to the Answer </summary>
	The force is going through the center of mass meaning no rotation motion is created by the force being applied to the object.
	</details>
	<br/>

</details>
<br/>

> To see angular motion, change the force position from (1,0,0) to (1, 1, 0) and run it again. What happens this time?

The rigidbody should move in linear space but also rotate. However, the rotation does not look like it is affecting the body much. It is not really rotating. Below is an example of the rigidbody with the above force applied over a few frames.

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%206/Readme%20Pictures/Force%20applied%20over%20time.png">
</p> 

> What could be causing this? Print out the value of orientation after you update it. What is it? What should it be? Give it a think before you look at the hidden answer below.

<br/>
<details>
  <summary> Issue with Rotation answer </summary>
	Torque is measured in radians instead of degrees. OpenGL uses degrees for it's rotation code. Find and use the GLM function that converts radians to degrees. You should get an improved result with the object rotating a lot around it's center of mass (which at the moment is the middle of the object).
</details>
<br/>

## Turning this into a 3D rigidbody

This example is 2D only. What would have to change to make this 3D?

I'll leave this up to you to research but consider:
* Inertia
* Center of Mass
* The torque formula and it's operations
* Force positions
* How to rotate in 3D space

Make sure to read the lecture slides again about these.

## Homework
* You will want the user to control the player. Think about how the placement of forces, along with keyboard controls, can be used to allow this to happen. You will need multiple forces and a way to turn them on and off!
* This tutorial did not consider any other forces such as drag and gravity. Think about and try to add these in whichever way you see fit.
* Once forces are present that rotate the object in your code, you will notice that your the relative positions of the forces are not working as expected. For example, a relative force on the X axis will still move the object on the X axis even when the object is rotated 90 degrees.
	* This is because the force positions are in body space while they really should be in world space so that when the objet rotates, the linear force created does as well.
	* To fix this, you will have to rotate the total linear force by the orientation at the end of the _CalculateForces_ function. Look into GLM functions that will allow you to do this or code your own _Rotate_ function that takes in the orientation and total linear force and rotates it via a rotation matrix. (Hint: The x and y values can be hardcoded with the rotation matrix maths.)
* Try different shapes of rigidbodies and alter the inertia and drawing code for them. How do they differ in terms of movement?
* Hardcoding force positions and values will increase the amount of similar code written. Perhaps you could use a _vector_ to hold forces and use _for_ loops to calculate final forces and updating? These values would have to be placed in a _struct_ first for the _vector_ to hold the information.

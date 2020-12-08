# Session 5 - Linear Motion

#### Table of Contents
1. [Downfalls of last session's movement](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%205/README.md#downfalls-of-last-sessions-movement)
2. [Making a Particle class](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%205/README.md#making-a-particle-class)
3. [Making the Particle Move](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%205/README.md#making-the-particle-move)
4. [Adding forces](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%205/README.md#adding-forces)
5. [Implementing Euler's Method](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%205/README.md#implementing-eulers-method)
6. [Improvements](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%205/README.md#improvements)
7. [Using keyboard controls with linear motion](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/tree/master/Session%205#using-keyboard-controls-with-linear-motion)
8. [Homework](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%205/README.md#homework)

Last session, we added looked into keyboard input, use of an _Update_ function, delta time and controlling objects.

For this session, we will be using Euler's method to give objects in our scene linear motion.

## Downfalls of last session's movement

In last session's tutorial, the movement of an object is done by just updating the object's position directly. This would be the same as teleporting the object to the new position. _This is not realistic, does not happen in real life and will cause issues later when trying to implement collision detection._

To make our motion more realistic, we will have to take into consideration the acceleration and velocity of the object to calculate it's new position. We will need to make a new class that holds all the variables needed to do this.

As we are focusing on linear motion (aka no rotational motion), this class should be a Particle class as particles only have linear motion.

## Making a Particle class

Using Newton's Second Law, we realize that the formula of F = ma means that the Particle needs to have a mass and acceleration with some force(s) acting on it.

Looing at Euler's Method, we also realize that to work out the position, we use the acceleration to work out the new velocity and the velocity to work out the new position.

This gives us a list of variables the Particle will have to contain to work all this out.

> Write a list of the variables the Particle class will need. Try this yourself first and then look at the hidden list below. Make sure you understand why these variables are used!
<br/>
<details>
  <summary> List of variables for a particle </summary>
	
  | Variables Needed | 
  | ------------- |
  | mass      | 
  | acceleration      | 
  | forces acting on it |  
  | velocity |  
  | position |  
  
</details>
<br/>
Once we have the variables, we will need to consider what type each variable will be.

> Look at the hidden variable list above if you have not already. For each variable, write down the type it should be. Try it yourself before looking at the list below.

<br/>
<details>
  <summary> Variable types </summary>
	
  | Variables Needed | Data type  |
  | ------------- | ------------- |
  | mass      | float  |
  | acceleration      | vec3 |
  | forces acting on it | vec3 |
  | velocity | vec3 |
  | position | vec3 |
  
</details>
<br/>

Now we know the variables and the types we can start to code this, with the GameObject as a base class.

> Create a new class called Particle which inherits from GameObject. Add the variables and needed functions. No need to worry about the integrator yet - Just get the class structure in place. Once you are done, check your class against the one below.

<br/>
<details>
  <summary> The Particle class </summary>

```C++
#pragma once
#include "GameObject.h"

class Particle : public GameObject
{
private:

	float mass;

	glm::vec3 acceleration;
	glm::vec3 velocity;
	//Particle already has position from GameObject!

	glm::vec3 totalForce;

public:
	void Draw();
	void Update(float);
};
```

```C++
#include "Particle.h"

void Particle::Draw()
{
}

void Particle::Update(float deltaTime)
{
}

```
</details>
<br/>

Once the overall class structure is in place, we can add the constructor for a Particle in, allowing us to create instances of a Particle in our scene.

```C++
public:
	Particle(float m, glm::vec3 pos);
	~Particle();
```

```C++
Particle::Particle(float m, glm::vec3 pos) : GameObject(pos)
{
	mass = m;
	velocity = glm::vec3(0, 0, 0);
	acceleration = glm::vec3(0, 0, 0);
	totalForce = glm::vec3(0, 0, 0);
}

Particle::~Particle()
{
}
```

Finally (with the power of polymorphism), we can create an instance in our _main/Source.cpp_.

```C++

#globally

GameObject* particle = new Particle(1.0f, glm::vec3(0, 0, 0));
 ...
 
 # in setup
 	objects.push_back(particle);
```

> Run the project to make sure you have not gained any errors. The scene will not have changed because we do not have anything in the Particle's _Draw_ function.

The graphics will not be marked for 217CR but we need some to see the physics in action! (**Remember, the graphics used in 217CR are not enough to pass 212CR!**)

> Update the _Draw_ function in the Particle class. _(Yes, I know particles are not 3D, but it makes them easier to see!)_

``` C++
void Particle::Draw()
{
	glPushMatrix();
	glColor3f(0.f, 1.f, 0.f);
	glTranslatef(position.x, position.y, position.z);
	glutSolidSphere(0.5f, 10, 10);
	glPopMatrix();
}
```

I've commented out the rest of the objects so it only draws the particle.

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%205/Readme%20Pictures/A%20particle.PNG">
</p> 

## Making the Particle move

Now the Particle class has been set up, we can look at implementing Euler's method.

Firstly, we will need to incorporate forces into our code. By adding forces that affect objects, we can then work out the resulting changes to acceleration, velocity and position to those objects.

### Adding forces

We will add forces via a function in the Particle class. As we will discuss later, this is far from ideal but serves to create a working example.

Per frame, we need to:

* Reset the "forces acting on the particle" variable to zero (_totalForce_ in my example)
* Add forces one at a time to that variable to sum all the forces acting on the particle
* Calculate acceleration, velocity and position based off this summed force

> Add a _CalculateForces_ function to the Particle class. This does not return anything or take any arguments. This should reset the "forces acting on the particle" variable to zero. Look at the example after you have tried this yourself.

<br/>
<details>
  <summary> CalculateForces function </summary>

```C++
class Particle : public GameObject
{
  ...
public:
  void CalculateForces();
  ...
};
```

```C++
void Particle::CalculateForces()
{
	totalForce = glm::vec3(0, 0, 0);
}
```
</details>
<br/>

Once this is done, we can add forces to the "forces acting on the particle" variable in this function.

> Add a gravity force in this function. Think about the value needed for Earth-like gravity. As always, give it a go yourself before you look at the example below.

<br/>
<details>
  <summary> Gravity added </summary>

```C++
void Particle::CalculateForces()
{
	totalForce = glm::vec3(0, 0, 0);
  	totalForce += glm::vec3(0, -9.8f, 0) * mass;
}
```
</details>
<br/>

Now a force is being applied, we need to run the integrator to work out the acceleration, velocity and position for the particle. This will make the particle move!

### Implementing Euler's Method

With a force applied, we need to use a numerical method to calculate an approximation of the next position. For this example, we will use the one covered in Lecture 3, called **"Explicit Euler"**. This is a basic requirement for the coursework.

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%205/Readme%20Pictures/Eulers%20Method.png">
</p> 

> Looking at the above formulae, turn this into code and add it to the _Update_ function of the Particle class. **I would suggest that you really do try it yourself as turning formulae (or even word problems) into code is a programming interview standard and the more practice the better!**

>Remember, for explicit Euler, we use the current velocity and not the new one, so we will have to update velocity after the calculation has been done.

> Run your project. Does the particle move? It shouldn't at the moment! This is because we have not called _CalculateForces_ anywhere. 

> Add a call to _CalculateForces_ at the top of the _Update_ function in the Particle class. Run the project again and it should move downwards.

## Improvements

The obvious downfall is that the force addition calculations are done inside the class. This means that all Particles will have the same forces applied to them. We might not want all Particles to fall due to gravity or other forces.

One way we could get around this would be to make child classes of Particle which had different _CalculateForces_ function definitions, meaning we would have classes like GravityParticle and DragParticle. 

The only issue with this is each new force, or each new combination of forces, would require a new class to be made. The function definitions would also share a lot of code between them if you started to mix forces together in different combinations.

A better way to solve this problem could be use some sort of registration system where particles and linked to a force generator.

A force generator would be a seperate class that would hold a force to apply to a particle and some internal logic to use the correct formula needed to work out the final force for that particle. This force generator would hold a _std::vector<Particle*>_ to determine which particles it should work on.

The overall logic would look something like this:

* At the Initialization stage, register a particle to a force generator by adding a _Particle*_ of that particle to the force generator's _vector_.
* At the Initialization stage, add a force to the force generator. For example, for the Gravity Force Generator, add _vec3(0, -9.8, 0)_ to it.

* Every frame, all particle's total forces are reset to zero.
* Every frame, all generators go through their respective vectors of _Particle*_, calculating and adding the force they hold to each particle's total force variable.
* Every frame (after the above has happened), the particles will have all the forces applied (and summed together to the final force total) (via the Generators), so calculate the new position based on whatever integrator they are using.

This would allow us to pick and choose which forces affect which particles.

Another improvement might be to debug the velocity of the particle so you can see the direction and magnitude of it.

The following code could be added to the _Draw_ function of particles to see this.

```C++
	glPushMatrix();
	glColor3f(1.f, 0.f, 1.f);
	glBegin(GL_LINES);
	glVertex3f(position.x, position.y, position.z);
	glVertex3f(position.x + velocity.x, position.y + velocity.y, position.z + velocity.z);
	glEnd();
	glPopMatrix();
```

If you would rather print _glm::vec3_ s to the console, you can also do this easily by using _#include<glm/gtx/string_cast.hpp>_ and then using the function _glm::to_string_ in a _std::cout_. Of course, if you do this every frame, it will fill up the console fast! (Hint: Perhaps you could link it to a keypress?)

An example would be:

```C++
	std::cout << "Acceleration: " << glm::to_string(acceleration) << std::endl;
```

## Using keyboard controls with linear motion

Having objects react to a gravity force is good but normally players want to interact with the game world via their input. With what we have learnt, let us use keyboard controls to move an object correctly.

We will create a basic Player class that works very much like the Particle but has keyboard controls instead of a gravity force.

> Create a Player class which inherits from GameObject. Give it the following variables and functions:

| Variables        | Functions           |
| ------------- |-------------| 
| mass     | Constructor (that takes a mass and position value) |
| acceleration      | Destructor (empty)      | 
| velocity | Draw     |  
| force total | Update (that takes the deltaTime value) |

Once this is created, add the following code for the Player's _Draw_ function.

```C++
void Player::Draw()
{
	glPushMatrix();
	glColor3f(1.f, 0.f, 1.0f);
	glTranslatef(position.x, position.y, position.z);
	glutSolidSphere(0.5f, 10, 10);
	glPopMatrix();
}
```

The _Update_ function will run every frame so we will check for keyboard controls in this. The overall logic for Player's _Update_ will be as follows:

* Reset the total force to zero.
* Check for key presses. If any on the arrow keys happen, add a certain force to the total force.
* Integrate as normal to work out the acceleration, then velocity, then position.

> Try implementing this yourself in the Player's _Update_ function. Once this is done, add an instance of Player to your scene and make sure it is in the object's _vector_ (so it will be updated!). Check your code to the example below when you are done.

<br/>
<details>
	<summary> Update Code Example </summary>
	
```C++

void Player::Update(float deltaTime)
{
	totalForce = glm::vec3(0, 0, 0);

	if (GameObject::specialKeys[GLUT_KEY_UP])
	{
		totalForce += glm::vec3(0, 0, 5);
	}
	if (GameObject::specialKeys[GLUT_KEY_DOWN])
	{
		totalForce += glm::vec3(0, 0, -5);
	}

	//YOUR EULER CODE HERE
}
```
</details>
<br/>

Once this is done, run the project and press one of the arrow keys once and only once. What do you notice?

<br/>
<details>
	<summary> The issue </summary>
	
Even when no force (and therefore no acceleration) is affecting the Player object, the object will continue to move in that direction.
</details>
<br/>

<br/>
<details>
	<summary> The issue explained </summary>
	
	 This is due to use updating velocity with the new velocity at the end of the calculation. 
	
	Remember Newton's Law 1? A body travelling in a straight line will maintain constant velocity unless acted upon by an external force! (Velocity + 0 is still velocity.)
	
	To change this, we will need to implement a very basic damping force so the velocity lowers over time (until it hits zero and the object finally comes to a stop).
	
	Add this line of code to the end of the _Update_ function to add damping.
	
	velocity *= pow(0.5, deltaTime);
	
	Make sure to tweak this damping number between 0 and 1 until you are happy with the result. (0 will make it stop instantly and above 1 will actually increase the amount of velocity added over time.)
</details>
<br/>


## Homework

* We are currently using "Explicit Euler" which is not the best integrator. For higher marks in 217CR, you will need to implement other integrators. **Either way, knowing the different integrators, what makes them different and to what effect is need-to-know knowledge. Time to research!**
* When calculating the acceleration, we do force / mass. A division in C++ code takes more clock cycles than multiplication. How could we improve this calculation? (Hint: it's to do with the mass never needing to change.) (Think about this for a while and then ask me about it!)
* Add more forces to the Particle such as drag or wind.
* Add some debugging to your project so you can get a better understanding of how the Particle is updated.
* Add keyboard controls for movement in the other axes.
* Make it so there is one object that is affected by the keyboard and others that are affected by forces for your coursework.
* Having keyboard controls in the _Update_ function is slightly messy. We could move the keyboard code into new function (perhaps called something like "CheckForInput" which we then call in the _Update_ function where the code used to be. - A small change but it will instantly neaten your code!

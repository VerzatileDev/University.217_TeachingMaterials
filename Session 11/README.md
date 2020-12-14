# Session 11 - Mass Springs and Soft Bodies
#### Table of Contents

1. [Mass Spring Systems - Rope](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%2011/README.md#mass-spring-systems---rope)
2. [Considerations for programming](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%2011/README.md#considerations-for-programming)
3. [Mass Spring Systems - Cloth/Water Surfaces](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%2011/README.md#mass-spring-systems---clothwater-surfaces)

For this tutorial, we will discuss mass spring systems and how we can use these for soft bodies. **I am not expecting anyone to do this for the coursework, but if you are interested in the concept (or want a challenge), this is something you could look into.**

## Mass Spring Systems - Rope

A mass spring system is a collection of particles with springs connecting the particles together in some fashion. For rope, this is a 1D construct where particles are connected to their immediate neighbours with 1 spring between them.

In the lecture, we talked about the steps needed to create such a system.

In any mass spring system, we will need to set up the rope by:
* Creating the particles (or rigidbodies) that will need to be connected
* Connecting the objects with springs
* Computing and storing the rest length (aka the initial length) of each spring in the system.

> The rest length is used by the spring force (which is the restoring force) to return the spring to it's equilibrium position (aka at it's rest length) if the spring has been displaced/moved from its equilibrium position in any way.

Once this is set up, each frame, we will need to update the mass spring system. This is exactly the same as how we have done particles and rigidbodies previously. Now the only difference is the calculation of the spring force (and the addition of springs to calculate this).

Each frame we will need to:
* Reset each particle's total force to 0
* Compute the current length of each spring
* Compute the spring force (using Hooke's Law and damping)
* Apply the force to each particle
* Inegrate via the equations of motion (Euler's, Verlet, RK2, RK4, etc.)

## Considerations for programming 

> For the _Spring_ struct, what variables do you think we will need to store? Consider both Hooke's Law and the damping equation.

<br/>
<details>
  <summary> Spring variables </summary>

* Either an index for the position of the first game object the spring is connected to in the vector 
	* or a GameObject* to the first object the spring is connected to
* An index or pointer to the second object the spring is connected to
* The stiffness constant / spring co-efficient
* The damping constant / co-efficient
* The rest/initial length of the spring
  
</details>
<br/>

It will take some time to get the right stiffness and damping values to make something look realistic. Try a damping value of 1 and a stiffness value of 100, then a damping value of 100 and a stiffness value of 1000 to see what happens differently.

> For the _Rope_ class, what variables would this need to hold?

<br/>
<details>
  <summary> Rope variables </summary>

This could go a number of ways but for ease I suggest:

* A vector for spring pointers
* A vector of objects that will be connected pointers 
  
</details>
<br/>

One way to program this would be to generate the objects outside of the class and then pass these into the _Rope_ class. (Of course, you could generate these inside the class as well.) You may also want to pass in the stiffness and damping constants through the constructor (or just hard code these where needed).

Once the objects are in the class, you will then go through all the objects and connect them to the next object along by creating a spring between them, setting the variables for the spring inside the same loop.

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%2011/Readme%20Pictures/spring%20rest%20length.png">
</p>

After this, you may want to either hard code which particles/rigid bodies will not move or have a seperate function that takes in a number of indices and updates the objects to tell them they are non moving. By non-moving, we mean they will not be updated by the equations of motion and as such, stay fixed in space. This will allow the other objects to move around the fixed ones instead of just falling in digital space forever.

An easy way to do this is as follows:

```C++
// IN THE HEADER OF PARTICLE

class Particle: public GameObject
{
	...
	public:
		bool blocked;
		
		void SetBlocked(bool block) { blocked = block; };
};

//IN THE CPP FILE OF PARTICLE

void Particle::Update(float deltaTime)
{
	if(!blocked)
	{
		...
		//Integrate with the equations of motion as normal
		...
	}
}

//ANYWHERE YOU NEED IT IN ROPE
	...
	// objects here is a std::vector<GameObject*> which holds the Particles of the rope
	objects[i]->SetBlocked(true);
	...
```

Finally, all the set up is complete. In the _Rope's_ update, we will want to reset each objects total force variable to a zero vector. We will then use Hooke's Law to work out the spring force (as all this information is stored in the _Spring_ class or can be accessed via the game object pointers/indices). We will also work out the damping force (again, with all the information we can access). Then we add the spring and damping forces together and add this final force to each object's total force, making sure to minus the final force for the second object.

> We will also want to add an external force or we will not see the rope move. For this, we can add a gravity force to each object.

```C++
	objects[i]->AddForce(glm::vec3(0, -9.8f, 0) * objects[i]->GetMass()); 
```

This is the difference when no gravity is applied and when gravity is applied. In this case, for the moved rope, 2 particles are blocked from updates so the rope has a small catenary (a curve when a chain/rope is supported by its ends) and the left hand part which is dangling due to gravity. 

> As you can see, without an external force (such as gravity) the rope will not move past its initial position (aka the rope of the left). We need this external force to move the rope out of it's equilibrium position!

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%2011/Readme%20Pictures/springs%20gravity%20vs%20none.png">
</p>

Then each object's _Update_ function is called with deltaTime to update the acceleration, which updates the velocity, which updates the position of the objects.

## Mass Spring Systems - Cloth/Water Surfaces

The same logic as above for ropes still stands. The changes for a 2D topology mass spring system are as follows:

* We will most likely want to use a 2D vector/array structure to hold all the objects (particles/rigidbodies).
* We will have to add more logic to the spring creation part. We cannot just add a spring to the next object along the vector. Now we need to figure out the neighbours of each object and connect them together. For 2D, this would be a maximum of 8 connections (or 12 connections if you wish to add the further away connections as mentioned in the lecture).

The below picture shows a 10 by 5 cloth. 

> If the below pictures are a bit pixelated, click them to see a bigger version.

From left to right:
* This cloth is without any connections.
* Spring connections in the X and Y axes to immediate neighbours only. (right, left, up, down) 
	* Shown in magenta
* Diagonal springs to immediate neighbours only.
	* Shown in yellow
* Far springs to neighbours that are 2 positions away. (left, right, up, down)
	* Shown in cyan
* The final cloth with all the spring connections above. Due to them all being on the same Z axis, it looks like a few are missing but all the previous springs shown are present!

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%2011/Readme%20Pictures/connections.png">
</p>

With the left hand side and right hand side particles of the cloth blocked from movement and updates, this is how the spring forces and gravity forces act. The damping value is 1 and the stiffness value is 100 for all the springs.

From left to right:
* Spring connections in the X and Y axes to immediate neighbours only. (right, left, up, down)
* Diagonal springs to immediate neighbours only.
* Far springs to neighbours that are 2 positions away. (left, right, up, down)
* Both axes springs to immediate neighbours and diagonal springs.
* All the spring connections above. 

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%2011/Readme%20Pictures/gravity%20on%20cloth.png">
</p>

Note: For the far springs, as these connections are neigbours that are 2 away, the cloth splits into two.

It will take some time to get the right stiffness and damping values to make something look realistic. Try a damping value of 1 and a stiffness value of 100, then a damping value of 100 and a stiffness value of 1000 to see what happens differently. You may also want to have different stiffness and damping values for each "type" of spring (in the axes, diagonal and far springs).

# Session 8 - Collision Detection

#### Table of Contents
1. [What is collision detection](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%208/README.md#what-is-collision-detection)
2. [Object representation](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%208/README.md#object-representation)
3. [Collision Detection](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%208/README.md#collision-detection)
4. [Collision Data](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%208/README.md#collision-data)
5. [Multiple Collider Types](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%208/README.md#multiple-collider-types)
6. [Homework](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%208/README.md#homework)

We currently have objects moving around the game scene realistically with both linear and angular motion. However, these objects do not react to one another. Moving one object into another object is allowed and we cannot guage when a collision has happened.  This means our games will have no game logic nor object interaction. 

To solve this, we will look into collision detection.

## What is collision detection?

In collision detection, there are 4 key areas. These areas allow us to find out what objects are colliding, what they are colliding with and the information of the collision. As with most games programming, this needs to be efficient and fast, as physics needs to be worked out in milliseconds (along with all the other subsystems) for a game to run smooth (and correctly). 

| Area | Description |
| ------------- | ------------- |
| Object representation | Proxy geometry is created and used for physics calculations instead of graphical geometry |
| Broadphase | Find sets of possible collisions between objects in the scene and generate pairs to check further |
| Narrowphase | Sets of possible collisions are checked for actual contact |
| Collision Data | Data from an actual collision is saved for collision resolution |

## Object representation

As we found out in the lecture, graphical geometry is too complex and numerous to check against every other object every frame. To cull possible collisions, we can improve performance by avoiding computational work in the first place. Bounding volumes/proxy geometry are course fitting volumes to cover an object, which are guaranteed to hold all that object inside.

By using simple shapes for our volumes/physics geometry, we lower the calculations needed to check if two objects are colliding.

> What 2D and 3D geometry could we use for bounding volumes? Make a quick list before looking at the hidden answer list below.

<br/>
<details>
  <summary> Possible proxy geometry </summary>

2D
* Circle
* Rectangle
* 2D Plane
* Disk

3D
* Spheres
* Planes
* Cubes
* Cylinders
* Capsules
* Polygon meshes (We could use, but we don't want to use normally! Of course, some games might need to check exact points of collision)

</details>
<br/>

> Which ones do you think you should use for your coursework? This will depend on the object!

In our code, we will have to give each object a physics representation in the form of proxy geometry. This means that each object has a graphical representation (for 212CR) and a physics representation (for 217CR).

> If we wanted to create sphere collider, what variables would we need for the class? Think about it yourself before you look at the hidden answer below.

<br/>
<details>
  <summary> Sphere collider variables </summary>

* Radius
* Position

</details>
<br/>

> Create a Sphere Collider class with the following:
> * The variables mentioned above
> * A constructor that takes in the needed arguments to fill the variables
> * A CollideCheck function that takes in a reference to a Sphere Collider and returns a boolean.
> * A Draw function (so we can see the collider!)

For the CollideCheck function, the lecture slides have the logic and diagrams for checking if two spheres collide.

A rundown of the logic is as follows:

| Sphere - Sphere Collision Check | 
| --------------------- | 
| Find the distance vector between both spheres | 
| Calculate the length of this vector |
| Add both radii together to get a radius total |
| Check if the length of the distance vector is less than or equal to the radius total | 

If this is true, there is a collision. If this is false, there is no collision.

For the Draw function, the following code could be used. **As usual, this is legacy OpenGL. For 212CR, you need to use modern OpenGL or you will fail that module's coursework!**

```C++

void SphereCollider::Draw()
{
	glPushMatrix();
		glColor3f(1.f, 0.f, 1.0f); //Magenta is the best debug colour!
		glTranslatef(position.x, position.y, position.z);
		glutWireSphere(radius, 10, 10);
	glPopMatrix();
}

```

> Once this is all done, add a Sphere Collider to one of your objects in the scene and add the collider's _Draw_ function to the object's _Draw_ function code so that it renders both to the screen. Make sure you can see both in the scene at the position which looks right.

> If you move the object (which has the collider) around the scene, the collider will not move with the object. Why is this the case? How could you solve it?

## Collision Detection 

> Make sure a few objects in your scene have a sphere collider attached to them and that one object is movable by the player. This is so we can implement collision detection.

> If we move an object into another, there still is no detection of collisions. Why?

<br/>
<details>
  <summary> Reason </summary>
This is because we have yet to call the collide's CollideCheck function anywhere in our code! We will have to do this in the GameEngine class.
</details>
<br/>

We will discuss the most basic of collision detection checks - Every object checking every other object. **(Of course, we could improve this in various ways which you will have to think about for your coursework!)**

To do this, we will need to check each object one at a time, with all the other objects in the game world. This can be done with 2 for loops.

A further check inside the 2 for loops is to not check for collisions between the current object and itself (aka i == 0 and j == 0) as this would always give a positive... (Think about it, the object is in the same space as the same object so a collision is always detected!)

> Code this check into the _Update_ function of the _GameEngine_ class. This would be after the deltaTime calculation but before the part where you update all the objects in the scene.

However, this method is O(n^2), which is a poor performer. This is will work fine for a small number of objects, but if your scene consists of a lot of objects, this method of collision detection will slow your game down!

> A small change to the second for loop will improve this method from O(n^2) to O(1/2 * n^2). This involves thinking about which objects have been checked as we go through the vector of game objects and that we do not need to check them again. 
>
> (Think of the improvement that can be done for Bubble Sort but instead of the improvement being due to the end of each iteration of Bubble Sort, it is at the start for each interation.)

If your _GameObject_ class has a _colour_ variable, you could change this colour when a collision has been detected between two objects. If a collision happens, you can set the colour of objects _i_ and _j_ to a colour of your choosing to show that a collision has happened.

(If you wanted colours to switch back after a collision stops happening, you may need each object to also store it's original colour and it's current colour as variables. You will then set all object's current colour to their individual original colours at the start of the _Update_ function. When a collision happens between two objects, you change their current colours to a hardcoded "collision" colour. This means every frame, non colliding object colours reset back and colliding objects change colour.)

## Collision Data

Currently, our collision check only tells us that two objects are colliding. However, this is not enough information for our physics engine when it comes to collision response. 

We will need more information about the collision that has happened so we can resolve collisions correctly.

> What information do you think is needed about a collision? Make a list and then check this against the hidden list below.

<br/>
<details>
  <summary> Possible Collision Information </summary>

* The 2 objects involved
* Collision point(s)
* Collision normal
* Penetration depth
* Restitution
* Friction values

</details>
<br/>

Some of the above pieces of information may be needed more than others so do not worry about working them all out straight away! This information can be stored in a struct.

> Why a struct instead of a class? What are the key differences between structs and classes? **A bit of a tangent, but this is sometimes a programming interview question!**

<br/>
<details>
  <summary> Struct vs Class </summary>

The only difference between a struct and a class is that a struct's variables are public by default while a class's variables are private by default.

Structs are normally used as POD objects. POD stands for "Plain Old Data" meaning the structs just hold data and do not have any functions.

```C++

struct StructExample
{
	int foo; //Note there is no public: or private: above this!
	float bar; //Both of these variables can be accessed outside the struct
}

class ClassExample
{
	int foo; //Note there is no public: or private: above this!
	float bar; //Both of these variables cannot be accessed outside the class
	
	void DoSomething(); //Normally classes have functions associated with them
}

// IN MAIN
StructExample structExample;
std::cout << structExample.foo << std::endl; //This would work fine (because it is public)

ClassExample classExample;
std::cout << classExample.foo << std::endl; //This would cause an error (because it is private)
```

</details>
<br/>

When we detect a collision, we will need to work out the collision information and save this in our struct.

> For Sphere-Sphere collisions, the current checks we do are enough to work out the :
> * Collision normal
> * Collision Point
> * Penetration Depth
>
> Get a piece of paper and a pen, draw 2 sets of spheres: One set just touching and the other set penetrating. Add the positions of each and the radius of each and figure out the above 3 pieces of information. Once you are happy with how to work them out, code them into your coursework.

If you have not already, you may wish to hold all the possible collision pairs, generated from the Collision Detection phase, inside a std::vector of collision information structs. By including pointers to both game objects you should be able to access (and change if you want to) all the needed information from them if needed. This allows your code to consider each collision pair in turn and do the required computation (such as Collision Response next week or just to easily change the colour of the 2 colliding objects!).

## Multiple Collider Types

**Although this tutorial only discusses sphere colliders, it is expected that students have multiple types of colliders such as Plane colliders and Axis Aligned Bounding Boxes (AABBs).**

These are left for students to complete for the coursework. Make sure to look at the lecture material and research online for techniques. **(Don't forget to reference anything from online sources!)**

Please read over the below methods to help guide you with creating such a system. It can get quite confusing getting a collision detection system up and running!

> There are a few methods to allow multiple collider types to work (each with their own benefits and issues):
>
> * You could either store the collision logic inside each class, or have a _CollisionManager_ class that stores the collision logic, while the _Collider_ classes just store the data needed for each collider type.
>
> * You may want to use a base abstract Collider class which all the other colliders will inherit from. All colliders would have a _Draw_ function and a _CollideCheck_ function. An issue that arises is that when you have more than one collider type, you will have to add more _CollideCheck_ functions to deal with the new collider types.
> 	* For example, you would have a _CollideCheck(Sphere*)_, _CollideCheck(AABB*)_ and _CollideCheck(Plane*)_ function and each collider type will need each of these so all colliders can collide with one another. The function body for each would change depending on what formula is needed for each object pair (Sphere-Sphere, Sphere-Plane, Sphere-AABB etc).
> 		* One way of doing this is via _double dispatching_. More information can be found at https://en.wikipedia.org/wiki/Double_dispatch.
> * You could tag colliders with a string name. Your code would then check when 2 objects collide what the names of each collider are and then pick the correct collision function to use.
> 	* For example, you would have _CollideCheck(Sphere*, Sphere*)_, _CollideCheck(Sphere*, Plane*)_, _CollideCheck(Plane*, Sphere*)_ functions to name but a few.

## Homework
* Complete Sphere-Sphere collision detection before moving onto other collider types.
* Draw collision information such as collision point and collision normals on screen when a collision happens. If it's good enough for Unity with Gizmos, it's good enough for us!
* Have objects change colour when colliding. _I always go with magenta (1, 0, 1) for debug colours!_
* Implement multiple collider types for your coursework.
* For higher marks, you will need to do broadphase and narrowphase for your coursework. Research into some broadphase methods and try to implement one.
* The tutorial uses a collider check function that returns only a boolean. Create another set of functions (or edit your current functions) so that instead of a boolean, useful collision information is passed out of the function and saved in the double for loop used in _GameEngine's Update_ function everytime there is actually a collision.

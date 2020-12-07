# Session 3 - Inheritance and Polymorphism

#### Table of Contents
1. [The issues with adding classes](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%203/README.md#the-issues-with-adding-classes)
2. [Using inheritance](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%203/README.md#using-inheritance)
3. [Using Polymorphism](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%203/README.md#using-polymorphism)
4. [Homework](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%203/README.md#using-polymorphism)

Last session, we created a class in our base C++ project and discussed documentation.

We saw how to make the class self contained, giving it a position (and a colour if you did your homework!), so that you could easily create multiple _Cube_ instances and place them around the scene.

However, games are made up of more that cubes (unless you are making Minecraft!)...

For this week's session, we will be setting up more classes and looking into the methods needed to keep adding new classes cleaner for both designing them and using them in our code.

## The issues with adding classes

To make a new class (such as a Sphere or Cone class), we might think of copying and pasting our new code over to a new _header_ and _C++_ file, renaming it and using that as it is a similar class.

This seems like an easy way to do things - Write it once and copy it over each time, adding changes as we need. However, this can cause a host of issues including:

* By copying the code over, we now have 2 copies of the code at different places in the project. If someone else reads the code, it is hard to tell that they do the same thing with slight differences.
* Copied code means a bigger overall code base. (This adds up when building large projects with 100's or 1000's of files. Thats why larger games have build engineers!)
* By copying the code over, any errors/bugs in the code are also copied over and are in different places in the project. Have fun patching 10's of classes because of this! As said in previous weeks, you will miss a class, meaning bugs will re-appear later on in the project you thought you had fully fixed. (Welcome to the world of day 1 patches for games.)
* We have 2 classes that are conceptually similar to humans (2 shapes in the game scene), but the compiler does not see these as similar which causes some coding problems.

> You think changing your coursework assignment code is bad between weeks? Read this and feel lucky! https://www.engadget.com/2014/10/06/the-crouch-that-changed-assassins-creed/

Let us see an example if we made a new class by copying and pasting code with the changes needed.

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%203/Readme%20pictures/Similar%20Classes.png">
</p> 

Hopefully you see that only the class name and the 1 line in the _Draw_ function has changed!

Now let us think about storing these instances in our code. 

We could use the current way of storing these globally.

```C++
  ...

Cube cube(glm::vec3(1, 0, 0));
Cube cube2(glm::vec3(0, 2, 0));
Cube cube3(glm::vec3(3, 0, 1));

Sphere sphere(glm::vec3(2, 0, 0));
Sphere sphere2(glm::vec3(0, -2, 0));
Sphere sphere3(glm::vec3(4, 0, 1));

void drawScene()
{
   ...
	cube.Draw();
	cube2.Draw();
	cube3.Draw();

	sphere.Draw();
	sphere2.Draw();
	sphere3.Draw();
   ...
```

You might think this doesn't look too bad... Now imagine it with 100's of those instances and with an extra 10 classes. Long lists of both instance creation and _Draw_ function calls make your code a pain to follow.

> Today's tutorial will solve similar class code re-use and the _Draw_ calls. You will have to wait a while to solve the global instance creation.

> How could we improve storing these instances? Look into the _vector_ container C++ class before continuing. (And yes, _vec3_ is a mathematical vector while _vector_ is a container type!)

We can solve some of the problems mentioned earlier using inheritance.

## Using Inheritance

Inheritance involves us setting up a system of base (or parent) and derived (or child) classes. You will have been taught about this in previous modules but to reiterate, the base class should contain the shared code between objects such as variables and functions. The derived classes can add extra functionality for it's needs or use different (but similar in terms of function input and output) functionality than the parent classes above it.

We can do this by making a new class that we will use for the base class of the rest. We shall call this class _"GameObject"_.

> Create a new class using 1 of the 3 methods discussed last week and call it _GameObject_. Make sure it holds the position variable, has both a default constructor and a constructor that takes in a _vec3_ position and has a _Draw_ function.

<br/>
<details>
	<summary> GameObject Example </summary>
	
```C++
#pragma once

#include <GL/glew.h>
#include <GL/freeglut.h>
#include <glm/glm.hpp>

class GameObject
{
protected:
	glm::vec3 position;
public:
	GameObject();
	GameObject(glm::vec3 pos);
	~GameObject();

	void Draw();
};
```

```C++
#include "GameObject.h"

GameObject::GameObject()
{
}

GameObject::GameObject(glm::vec3 pos)
{
	position = pos;
}


GameObject::~GameObject()
{
}

void GameObject::Draw()
{
}

```

> Why did we use protected instead of private? Did you check for your Session 2 homework?

</details>
<br/>

Once this is made, we need to alter the _Cube_ class to allow it to inherit from the _GameObject_ class in the following ways:

* We need to make the _Cube_ class inherit off the _Gameobject_ class.
* We need to remove the position variable from _Cube_ (as it is now in _Gameobject_)
* We need to alter the constructor to include the _Gameobject_ constructor (and pass the position argument into the variable in _Gameobject_)

> Do the above alterations to the _Cube_ class. Make sure to try this all yourself before you check against the example below.

<br/>
<details>
	<summary> Cube with inheritance </summary>
	
```C++
#pragma once

#include "GameObject.h"

class Cube : public GameObject
{
public:
	Cube();
	Cube(glm::vec3 pos);
	~Cube();

	void Draw();
};
```

```C++
Cube::Cube(glm::vec3 pos): GameObject(pos)
{
}
```

</details>
<br/>

Running the project should still show the cubes in the scene. Now the base class is down we can use Polymorphism to further improve our code design.

## Using Polymorphism

Polymorphism allows us to do 3 powerful things:

* We can have pointers of the type of the base class be used for the derived classes.
* We can use virtual methods that allow different classes to do different things while using the same function call.
* We can create abstract base classes which hold the overall function definions of the class but have no definions for the function bodies.

Let us take each of these in turn.

### Base Class Pointers

Instead of just creating instances of the type of the current class such as _Cube cube(glm::vec3(0, 0, 0));_, we can create a pointer of the base class of _Cube_ and use this instead.

> How would you write this pointer line? Have a go before looking at the answer below.

<br/>
<details>
	<summary> Cube with inheritance </summary>
	
	It differs if you have an instance already made. If you do, you can use the cubePtr way. If not, you have to use the _new_ keyword which can cause memory leaks! If you do use _new_, use _delete_ at the end of your program to stop the memory leaking!
	
```C++

Cube cube(glm::vec3(1, 0, 0));
GameObject* cubePtr = &cube;

GameObject* cubePtr2 = new Cube(glm::vec3(1, 0, 0));
```

</details>
<br/>

> Why is this useful? Find out next week! (A hint: It's useful for storing different inherited class types together.)

### Virtual Methods

A virtual method allows us to redefine an inherited function for later child classes. This means that by using one function definition, each class can do the same function differently when called.

We can do this in our code using the _virtual_ keyword. This goes in front of the base classes function definition.

```C++
class GameObject
{
 ...
public:
	...
	virtual void Draw();
};
```

Now, the cube will use it's version of _Draw_ (because we gave it the function body in the _Cube.cpp_) while any other class inheriting from _GameObject_ will use _GameObject's_ version of _Draw_ (if that new class does not have a function body for _Draw_).

In our case, _Draw_ must return void and take no arguments for all those derived classes (because that is what GameObject's one is like).

> Add this addition to your own _GameObject_ and make sure the scene still looks the same.

### Abstract Base Classes

Now, in our example, _GameObject_ has an empty function body for _Draw_. This seems a bit pointless because we do not want to make any instances of _GameObject_ (What really is a GameObject visually?) so why have even an empty definition for the _Draw_ function we never use?

Currently, we could make an instance of _GameObject_ in our code but we will not need to. Let's change that so GameObject can only be used as a base class and not actually instanced in our code as an object.

By changing the virtual function into a pure virtual function, we are telling our code that we do not have a definition for that function. Furthermore, we are telling all derived classes that they **must** have a definition for that function or the compiler will throw an error and refuse to compile and run.

> To do this, remove the function body of _Draw_ for _GameObject_ and add a _= 0;_ to the end of _Draw_ in the header file.

```C++
class GameObject
{
 ...
public:
	...
	virtual void Draw() = 0;
};
```

> Once this is done. Try this yourself to prove the magic of abstract classes. Comment out the _Draw_ function in _Cube_, both in the header and C++ files. Run the project. What does the error message say?

Why are virtual methods and abstract classes useful? We can now make objects off a base class that adhere to the base classes definition and know which functions are default for all derived classes or which functions can be changed on a per class basis. Now if we make new classes off GameObject, we know we have to give these classes at least a _Draw_ definition for them to be used. In big projects with multiple people working on them, this keeps a level of forced assumption and focus at the writing code level.

Next week, we will use the full power of this for storing all these instances. Some of this week's homework talks about this.

## Homework

* Add a Cone class that inherits from GameObject. You will need to find the line of OpenGL code to draw a Cone rather than a cube or sphere. Add some instances to your scene and make sure they show.
* Make the Sphere class a child of GameObject. 
* Edit the Sphere class so that you can pass in a radius for the sphere when you create an instance of one. You will have to change something in the Sphere's _Draw_ to use this new variable of the class.
* Start planning the classes needed for your assignment. What can inherit from what and how? What functions would you need? Why?
* Use some Base class pointers to hold your instances of the objects. You will find that the _Draw_ functions for these now throw an error and have a red underline when you try to run the project. What causes this and how do you solve it?
* Use vectors to hold each type of created instance class of all your objects. You could then use a for loop to iterate through each vector to call the _Draw_ function for that class. (We will improve/explain this next week.)

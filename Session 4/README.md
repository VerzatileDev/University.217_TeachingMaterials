# Session 4 - Movement

#### Table of Contents
1. [Using polymorphism for storing instances](https://github.coventry.ac.uk/217CR-1920JANMAY/Teaching-Material/tree/master/Session%204#using-polymorphism-for-storing-instances)
2. [Basic keyboard input](https://github.coventry.ac.uk/217CR-1920JANMAY/Teaching-Material/tree/master/Session%204#basic-keyboard-input)
3. [Updating](https://github.coventry.ac.uk/217CR-1920JANMAY/Teaching-Material/tree/master/Session%204#updating)
4. [The use of delta time](https://github.coventry.ac.uk/217CR-1920JANMAY/Teaching-Material/tree/master/Session%204#the-use-of-delta-time)
5. [Improving on keyboard input](https://github.coventry.ac.uk/217CR-1920JANMAY/Teaching-Material/tree/master/Session%204#improving-on-keyboard-input)
6. [Moving with the keyboard](https://github.coventry.ac.uk/217CR-1920JANMAY/Teaching-Material/tree/master/Session%204#moving-with-the-keyboard)
7. [Homework](https://github.coventry.ac.uk/217CR-1920JANMAY/Teaching-Material/tree/master/Session%204#homework)

Last session, we added inheritance and polymorphism to our project in the form of inherited classes with an abstract base class.

For this week's session, we will be looking into getting our objects to move around the scene.

## Using polymorphism for storing instances

This week we will remove the suspense of how polymorphism can be used to store instances in an efficient manner!

If we used the _vector_ container to hold our instances, it would look like this. This is because while we can see they are similar in concept, the compiler cannot, meaning we have to seperate them out. However, it stops the long amount of _Draw_ calls by using a for loop to do the work for us.

```C++
  ...
  
std::vector<Cube> cubes;
std::vector<Sphere> spheres;
std::vector<Cone> cones;

  ...
# at some point in the code (normally the initialize part and not the draw part)

cubes.push_back(cube);
cubes.push_back(cube2);

spheres.push_back(sphere);
...

# in the draw function
	for (int i = 0; i < cubes.size(); ++i)
	{
		cubes[i].Draw();
	}

	for (int i = 0; i < spheres.size(); ++i)
	{
		spheres[i].Draw();
	}

	for (int i = 0; i < cones.size(); ++i)
	{
		cones[i].Draw();
	}
 ``` 

We can see that there is a lot of similar code being used in the form of the for loops, the adding of objects to the vectors and the multiple vectors. Now, imagine if we had to do this everytime we added a new class!

With polymorphism, we have the extra power of using base class pointers as pointers for the derived class. Why this is so powerful can only be shown with an example.

```C++
  ...
  
std::vector<GameObject*> objects;
  ...
# at some point in the code (normally the initialize part and not the draw part)

GameObject* cube = new Cube(glm::vec3(1, 0, 0));
GameObject* cube2 = new Cube(glm::vec3(3, 0, 1));

GameObject* sphere = new Sphere(glm::vec3(2, 0, 0));
GameObject* sphere2 = new Sphere(glm::vec3(4, 0, 1));

objects.push_back(cube);
objects.push_back(cube2);

objects.push_back(sphere);
objects.push_back(sphere2);

...

# in the draw function
	for (int i = 0; i < objects.size(); ++i)
	{
		objects[i]->Draw();
	}
  
...

# in cleanup / end of the program
 
	for (int i = 0; i < objects.size(); ++i)
	{
		delete objects[i];
	}
 ``` 

Note the use of _GameObject*_  and the _new_ keyword. We now use a _vector_ of GameObject pointers to store any class that can derive from GameObject (in this example, Cubes and Spheres) and can go through the _vector_ and use the correct _Draw_ function depending on what object it is. 

_(How does it do this? That's a homework task!)_

> Make sure **every new usage has a corresponding delete usage in your code!** 

This level of code is easier to read, is smaller and is more viable to debug. If spheres draw incorrectly but the rest are fine, we can go to the Sphere class to look.

> Make sure you understand the reasoning for the above example and why we altered it. If you are unsure, just ask a member of staff about it!

## Basic Keyboard Input

A static scene is boring **(and also does not pass the requirements for the 217CR coursework!)**. Let us add basic keyboard input to make an object move.

Looking at our code, we can see two areas that work with keyboard input. These are:

``` C++ 
void keyInput(unsigned char key, int x, int y)
{
	switch (key)
	{
	case 27:
		exit(0);
		break;
	default:
		break;
	}
}
``` 
 
``` C++
	glutKeyboardFunc(keyInput);
``` 

We can add some more keys for players. I will be using the following axes for movement. You can edit this to suit you.

| Key  | What it does |
| ------------- | ------------- |
| w  | Move forward (-Z)  |
| s  | Move backward (+Z)  |
| a  | Move left (-X) |
| d  | Move right (+X) |

Before we use them for moving, we could debug them via console printouts. Make sure to _#include <iostream>_ at the top of the _main/Source.cpp_.
	
``` C++
void keyInput(unsigned char key, int x, int y)
{
	switch (key)
	{
	case 27:
		exit(0);
		break;
	case 'w':
		std::cout << "Moving forward in - Z" << std::endl;
		break;
	default:
		break;
	}
}
``` 

> I have done the first move input. Implement the rest.

Now this works, we can look into using the arrow keys.

> Replace the _case 'w':_ line with _case GLUT_KEY_UP:_. What happens?

<br/>
<details>
	<summary> Answer </summary>
	
It worked with 'w' before but now does not work... This is because glut has two functions for keyboard input callbacks. We are using _glutKeyboardFunc(...)_ but for arrow keys and other special keys we need to use _glutSpecialFunc(...)_.
</details>
<br/>

Put 'w' back in the switch statement and make another callback function for the special keys. Make sure to register the callback in the _main_ function (like what has been done for _glutKeyboardFunc_. (You might have to look into what are the callback arguments for this - https://www.opengl.org/resources/libraries/glut/spec3/node54.html)

Try it yourself first and check your result against the example below.

<br/>
<details>
	<summary> Special Keys Example </summary>
	
``` C++
void keySpecialInput(int key, int x, int y)
{
	switch (key)
	{
	case GLUT_KEY_UP:
		std::cout << "Moving forward in - Z" << std::endl;
		break;
	case GLUT_KEY_DOWN:
		std::cout << "Moving backward in +Z" << std::endl;
		break;
	case GLUT_KEY_LEFT:
		std::cout << "Moving left in -X" << std::endl;
		break;
	case GLUT_KEY_RIGHT:
		std::cout << "Moving right in +X" << std::endl;
		break;
	default:
		break;
	}
}
``` 
	
``` C++
	glutSpecialFunc(keySpecialInput);
``` 
</details>
<br/>

## Updating

In every video game, we will want to look at each object and work out their new positions/rotations/states/reactions etc. Incrementing these by a small amount every frame gives the objects an illusion of motion. Every frame we render an image. Render enough images fast enough and the objects move.

We will want to do the same to our objects at some point, either when the player presses a key or because time has passed. (Games would be boring if the game world only updated when you pressed a key - _Although SuperHot made that its whole concept!_ https://youtu.be/A1jothqmqHw?t=11)

This means we will have to add an _Update_ function to all our objects. Thankfully because of inheritance, these changes can be done quickly. We can add a _virtual_ function to the GameObject class and then override the definition of it in the objects that need it.

> This could be either a pure virtual or just a virtual function. In this example, we will stick with a virtual function. This means that derived classes that do not have their own _Update_ definition will use the base classes one (aka GameObject's empty one). I do this so objects that have no _Update_ are static and do not move. If we used a pure virtual, we would have to give a definition to each class meaning each class would have to include an empty _Update_ which is a waste of time and adds more unneeded code.

> Add a virtual _Update_ function to GameObject. Give it an empty definition in the C++ file. Then add _Update_ to the Cube class.

Let us see _Update_ in action! Add this code to your Cube class. It will change it's position via the Y axis, making it fall. Run the project after the change.

``` C++
void Cube::Update()
{
	position.y -= 0.1f;
}
``` 
Nothing happened! Why is this? Think about it and check the answer.

<br/>
<details>
	<summary>Answer</summary>
	
	We never call the _Update_ function anywhere so the function (and the code inside the function) is never ran!
	
	To fix this, we need to think about where the call could go... We have an _idle_ function that we can do this in. (Why? That's a homework task!)	
</details>
<br/>

Look at the answer if you have not already. How would we fix the issue? Try it yourself before you see the code answer below. Your cubes should fall (while the other objects stay still) if done right.

<br/>
<details>
	<summary>Code answer</summary>
	
``` C++
	void idle()
{
	for (int i = 0; i < objects.size(); ++i)
	{
		objects[i]->Update();
	}

	glutPostRedisplay();
}
``` 
</details>
<br/>

## The use of delta time

This looks fine in regards to cubes falling. However there is an underlying issue which you might remember from your Unity ALL days.

At the moment, the _Update_ happens everytime a frame is drawn. On a slow computer, fewer frames are drawn (because it takes a longer time for the hardware to create the frame) meaning fewer _Update_ calls are done (because these only happen after the frame is drawn). On a faster computer, the opposite happens - More frames = more _Update_ calls.

> In video games this can cause a number of issues from:

> * Older games running too fast on newer computers (Hitman 1 anyone? - https://www.youtube.com/watch?v=zVPQovf5i8Y).
> * Glitches arising from the game engine running too fast (I thought I was doing something wrong in Assassin's Creed for ages when I replayed it recently! - https://www.youtube.com/watch?v=hLehVlWTejo - After many forum searches, I needed to force V-Sync on to fix it!).
> * The general issue of the frame rate dictating the rate of updating (For online games, this is unacceptable... Looking at you COD:AW laser gun - https://www.youtube.com/watch?v=KiTF6jdmfOA).

> Hopefully, I've proven my point!

To counter this, we need to update based on the time passed rather than the amount of frames. This means that 1 frame taking 1 second will be the same as 10 frames taking 0.1 seconds each so players should have the same experience no matter the frame rate.

The way to do this is to work out delta time, which is the amount of time that has passed between frames. We can then pass that into the _Update_ function and use this to work out movement etc. so that it stays coherent regardless of frame rate.

> Firstly, edit the _Update_ function in both Cube and GameObject so that a float is used for an argument. Then, update the Cube _Update_ so that the position change is multiplied by this argument. Finally, hardcode some values for the _Update_ call in _main/Source.cpp_ to see the changes when this value differs. (Try 0.16f, 0.33f and some more so you can see how delta time affects the movement.)

Once you have done this (or get stuck after a while), look at the completed code below.

<br/>
<details>
	<summary>Update with delta time</summary>
	
``` C++
	...
	class GameObject
{
	...
	virtual void Draw() = 0;
	virtual void Update(float);
};
```

``` C++
void GameObject::Update(float deltaTime)
{
}
```

``` C++
	...
class Cube : public GameObject
{
	...
	void Draw();
	void Update(float);
};
```

``` C++
void Cube::Update(float deltaTime)
{
	position.y -= 0.1f * deltaTime;
	//position.y -= 0.1f;
}
```

``` C++
void idle()
{
	for (int i = 0; i < objects.size(); ++i)
	{
		objects[i]->Update(0.33f); # hard coded for now
	}

	glutPostRedisplay();
}
```
</details>
<br/>

Let us now work out the actual delta time between frames and use this value for our _Update_ function. FreeGLUT gives us a function for this via _glutGet(GLUT_ELAPSED_TIME)_ which gives us the number of milliseconds since the first call of _glutGet_.

> If you would rather use the C++ _clock_ code, you can use that too - http://www.cplusplus.com/reference/ctime/clock/

Using this, we can:
* Take a record of the last time
* Get the new time by calling _glutGet_
* Work out the delta time by subtracting the last time from the new time (giving us the milliseconds difference)
* Dividing the delta time by 1000 to give us delta time in seconds
* Set the last time to this new time (ready for the next frame)

> Add 2 new global int variables in the _main/Source.cpp_ called oldTimeSinceStart and newTimeSinceStart. Then copy this code into your _idle_ function. I've added some print outs so you can see how delta time works. Once you are happy with how it works, you can remove the _cout_ lines to stop your console getting spammed!

``` C++
void idle()
{
	oldTimeSinceStart = newTimeSinceStart;
	newTimeSinceStart = glutGet(GLUT_ELAPSED_TIME);

	std::cout << " --------------------------- " << std::endl;
	std::cout << "OldTimeSinceStart: " << oldTimeSinceStart << std::endl;
	std::cout << "NewTimeSinceStart: " << newTimeSinceStart << std::endl;

	float deltaTime = (newTimeSinceStart - oldTimeSinceStart);
	std::cout << "Delta Time (ms): " << deltaTime << std::endl;
	deltaTime /= 1000.f;
	std::cout << "Delta Time (seconds): " << deltaTime << std::endl;
	std::cout << " --------------------------- " << std::endl;

	for (int i = 0; i < objects.size(); ++i)
	{
		objects[i]->Update(deltaTime);
	}

	glutPostRedisplay();
}
```

## Improving on keyboard input

In our project, we have hardcoded our key presses in the _main/Source.cpp_ meaning only the keys we coded in are being used. Also, objects don't have an easy way to query what keys are pressed.

We would like to be able to use all the keys on the keyboard without the pain of having to manually code a case for each one. Luckily, we can use the _map_ data structure along with the glut callbacks to our advantage.

> Don't know what the _map_ data structure is? Time to read up! - http://www.cplusplus.com/reference/map/map/ 

The glut keyboard function callbacks take in either an _unsigned char_ (for the normal keys) or an _int_ (for the special keys) as an argument. At the moment, we take that argument and test it against our switch. What we can do is use a _map_ to hold this key and a Boolean value (true if it is pressed and false if it is not pressed). 

The keyboard map will hold:

| Map Section | Type  | Reasoning |
| ------------- | ------------- | ------------- |
| Key  | char  | For the keycode |
| Value  | bool  | Is it pressed |

The special keyboard map will hold:

| Map Section | Type  | Reasoning |
| ------------- | ------------- | ------------- |
| Key  | char  | For the keycode |
| Value  | bool  | Is it pressed |

We will change the glut keyboard function callbacks so that it sets these to _true_ or _false_ when a key is pressed or let go.

The magic behind this is if the key is not present in the map yet, it is like it is set to false (meaning no random keypresses appearing), and the first time we press the key, it will automatically add it in.

To sum up, the changes involve:
* Create a map for special key presses
* Create a map for normal key presses
* Updating the _glutKeyboardFunc_ function
* Updating the _glutSpecialFunc_ function
* Adding the (new to you) callbacks when a key is let go (_glutKeyboardUpFunc_ and _glutSpecialUpFunc_)

### Creating the maps

For the maps, we will place them into the GameObject class. 

> "Why in that class?" you may ask. This is so we can access the keypresses directly in GameObject (or it's derived classes). In a real engine, this would be in it's own "Input" module that we would poll.

We don't want each instance of GameObject to have a copy of both maps, as this would be a waste of memory. What we can do instead is make them _static_ variables, which means there is one copy for the class, rather than one copy per instance. Useful eh?

> Create 2 static maps in the GameObject class as public variables. One that holds a <char, bool> pair (call this keys) and one that holds a <int, bool> pair (call this specialKeys). Remember to put the include in the header.

Try it yourself before you look at the code example below.

<br/>
<details>
	<summary> Code Example </summary>

```C++
	...
#include <map>

class GameObject
{
	...
	static std::map<char, bool> keys;
	static std::map<int, bool> specialKeys;
	...
};
```

</details>
<br/>

As they are static variables, you will need to create the one instance the class holds. Add this to the top (after the _#include_) of the GameObject C++ file. Note the _GameObject::_ before the variable names which shows they are for the overall class and not each instance.

```C++
#include "GameObject.h"

std::map<char, bool> GameObject::keys;
std::map<int, bool> GameObject::specialKeys;

GameObject::GameObject()
	...
```

### Updating the keyboard callback functions

Now these are in place we can update the current glut keyboard callback functions to use these maps instead of our hardcoded switches.

```C++
void keyInput(unsigned char key, int x, int y)
{
	GameObject::keys[key] = true;
	std::cout << "Key pressed: " << key << " : " << GameObject::keys[key]  << std::endl;
	//If we press escape, quit
	if (key == 27)
		exit(0);
}
```

> Run the project and smash the keyboard a bit to see that all normal keys are being noted in the console and the values are being updated in the _map_! Once you are happy, you can remove the _cout_ line if you want.

### Adding new callback functions for a key up

At the moment, we can't make them false again, so the _map_ will falsely think previously pressed keys are still being pressed! We can fix that with the following.

```C++
void keyInputUp(unsigned char key, int x, int y)
{
	GameObject::keys[key] = false;
	std::cout << "Key pressed: " << key << " : " << GameObject::keys[key] << std::endl;
}

...

# down in the main()

	glutKeyboardFunc(keyInput);
	glutKeyboardUpFunc(keyInputUp); //we have now registered our new callback function

```

> Test this again until you are happy that the key presses and letting go of a key works! Remove the _cout_ line if you'd rather have a littered console.

> Do the same process on you own for the special key callback and add a special key up callback and register it in the _main_ function.

## Moving with the keyboard

To end with this week's tutorial, let us add the ability to move an object with the keyboard. Add this code to the Cube C++ file.

``` C++
void Cube::Update(float deltaTime)
{
	if (GameObject::specialKeys[GLUT_KEY_UP] == true)
		position.z -= 1.f * deltaTime;
	if (GameObject::specialKeys[GLUT_KEY_DOWN] == true)
		position.z += 1.f * deltaTime;
}
```
> Run the project and try the up and down arrow keys to see if they work. Once they work, add in the left and right arrow keys as well.

> With this code, all instances of cube will move. How could we solve this? (Hint: Either make a new "Player" class that moves or mark instances with a Boolean that when _true_, allows them to move when keys are pressed, and when _false_, does not move them when keys are pressed.)

## Homework

* Why did I use ++i in my for loop? Look around online for the reason. How can we further improve the for loop? (Hint: it's to do with _size_ in the loop.)
* For virtual functions, C++ uses a vtable or "virtual table". This sometimes comes up in programming interviews so look into what one is and what it means in terms of performance.
* Memory leaks also come up in programming interviews. Microsoft tests all games before going on the store in many ways - One of these is leaving the game on for 24 hours straight, which catches any memory leaks (as the game will crash at some point). Make sure you understand the use of new and delete along with how memory leaks work. (An interesting example - https://www.youtube.com/watch?v=86yh_VJcvX0) 
* Why do we put the Update code in the idle callback and not elsewhere? Research around to see why.
* Think about how you could use the Update function as the integrator step of your own coursework to make objects move based on acceleration, velocity and position.
* The new keyboard callbacks are only a few lines of code. Use lambdas instead to remove the need to create a seperate callback function to register. (We want neat code right?)

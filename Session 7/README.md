# Session 7 - Putting it into a game engine format

#### Table of Contents
1. [Issues with the current code](https://github.coventry.ac.uk/217CR-1920JANMAY/Teaching-Material/tree/master/Session%207#issues-with-the-current-code)
2. [What we can do to allievate this?](https://github.coventry.ac.uk/217CR-1920JANMAY/Teaching-Material/tree/master/Session%207#what-we-can-do-to-allievate-this)
3. [The GameEngine class](https://github.coventry.ac.uk/217CR-1920JANMAY/Teaching-Material/tree/master/Session%207#the-gameengine-class)
4. [Changing the main.cpp](https://github.coventry.ac.uk/217CR-1920JANMAY/Teaching-Material/tree/master/Session%207#changing-the-maincpp)
5. [Homework](https://github.coventry.ac.uk/217CR-1920JANMAY/Teaching-Material/tree/master/Session%207#homework)

For this session, we will take all the code we have created so far and adjust it so it comes together into a more coherent "Game Engine" format.

Of course, this is far from what a normal game engine looks like but it is a good start to one and makes the project easier to navigate. It is also good practice for your third year!

## Issues with the current code

At the moment, most of the code is present in the _main.cpp_ making this a long and unwieldy piece of code to read through whenever we need to do a change or addition. The main concern is when we add more to the project, this bloated file will just get bigger.

Another issue is that we are using a lot of global variables. Global variables are the ones at the top of the file that are not inside a function.

> Why do you think global variables are an issue?
<br/>
<details>
  <summary> Answer </summary>
  Global variables can be accessed anywhere at anytime in your code. This means they can be changed/altered anywhere anytime, making it hard to debug code when a value can be affected at any point in the project. 

It also is considered poor coding form in real life projects due to this and the sign of sloppy code and a lack of planning. Your code should do exactly what you want and nothing more.
	
	
	For further reading on this with a real life example (10000 global variables used - Yes, bad code can cause deaths!), I would suggest http://www.safetyresearch.net/blog/articles/toyota-unintended-acceleration-and-big-bowl-%E2%80%9Cspaghetti%E2%80%9D-code 
</details>
<br/>

## What we can do to allievate this?

What we can do is move our code out of _main.cpp_ and into a class so we know where things are and what happens at which time. This class could be reusable, allowing us to make different games by having a solid back end structure and changing key areas such as NPC data and game logic.

> This is a very basic layout for a game engine. In an actual game engine there is the concept of compartmentalizing code into subsystems. Each subsystem does one area such as Audio, Physics, AI, Graphics etc. For example, you could have an OpenGL, DirectX, Vulkan and Metal graphics subsystem which you could switch between for different machines. You will learn more about this (and do it yourself) in third year.

## The GameEngine class

The _GameEngine_ class will store the fuctions every game would need.

> What are we currently doing at the moment in our code? Look through the code and take a note of the key areas. Look at the answers below after you have written them all down.

<br/>
<details>
  <summary> Key Areas </summary>

From the top of the code to the bottom (aka they are not ranked in any order)

  | Key Area | Description |
  | ------------- | ------------- |
  | vector<GameObject*> objects | A way to store and access created objects in the game world. |
  | Global time variables | To work out deltaTime in between updates. |
  | drawScene | The function to draw all objects to the screen. |
  | setup | The function to get things ready. |
  | resize | The function to alter the window contents if the screen changes shape. |
  | Keyboard functions | Allow the player to interact with the game world in some fashion. |
  | Idle | Update the objects and work out deltaTime between frames. |
  | main | The function to set up OpenGL and GLUT, start the game world and then delete the objects afterwards. |
  
</details>
<br/>

We can take these key areas and code them ourselves into this new class. By doing this, the _main.cpp_ will be less cluttered and if we need to change anything we can find the key area in the _GameEngine_ class.

> Make a new class called _GameEngine_. Include all the includes you are currently using such as _GameObject_ header and the vector header. Add the following functions to the _GameEngine_ header. All the functions output _void_.

| Function or Variable | Parameters (if a function) | Access Level | Key Area |
| ------------- | ------------- |  ------------- |  ------------- |
| vector<GameObject*> objects | N/A | private |A way to store and access created objects in the game world. |
| int oldTimeSinceStart | N/A | private | To work out deltaTime in between updates. |
| int newTimeSinceStart | N/A | private | To work out deltaTime in between updates. |
| UpdateGame() | void | private | Update the objects and work out deltaTime between frames. |
| DrawGame() | void | private | The function to draw all objects to the screen. |
| CleanupEngine() | void | private | Delete the objects afterwards. |
| InitEngine() | int argc, char** argv, const char* windowTitle, int width, int height  | public | The function to set up/initialize Opengl and GLUT and the function to get things ready (but not run). |
| ResizeWindow() | int w, int h | private | The function to alter the window contents if the screen changes shape. |
| AddGameObject() | GameObject* | public | A way to add objects to the game world. | 
| StartEngine() | void | public | Start the game world. |

Once this is done, we can then create the function bodies for the functions. We can move most of the code from the _main.cpp_ over and then tweak it to our needs.

### InitEngine() function

For this function we can move most of the _main_ function from the _main.cpp_ file over. We will get everything until the _glutMainLoop()_ line. We do this so we only setup OpenGL and GLUT and not start the game engine (which _glutMainLoop_ does).

> Move the needed code from _main_ over to the _InitEngine_ function. Look at the code below after you have tried this to make sure you are correct.

<br/>
<details>
  <summary> InitEngine() </summary>
	
```C++
void GameEngine::InitEngine(int argc, char** argv, const char* windowTitle, int width, int height)
{
	glutInit(&argc, argv); //this is why we need to use argc and argv

	glutInitContextVersion(2, 0);
	glutInitContextProfile(GLUT_COMPATIBILITY_PROFILE);

	glutInitDisplayMode(GLUT_DOUBLE | GLUT_RGBA | GLUT_DEPTH);
	glutInitWindowSize(width, height); //changed to include the parameters
	glutInitWindowPosition(100, 100);
	glutCreateWindow(windowTitle); //changed to include the parameters

	glewExperimental = GL_TRUE;
	glewInit();

	glutDisplayFunc(DrawGame); //changed to include the GameEngine function
	glutReshapeFunc(ResizeWindow); //changed to include the GameEngine function
	glutIdleFunc(UpdateGame); //changed to include the GameEngine function
	
	glutKeyboardFunc(keyInput);
	glutKeyboardUpFunc(keyInputUp);

	glutSpecialFunc(keySpecialInput);
	glutSpecialUpFunc(keySpecialInputUp);
}
```
</details>
<br/>

As you will see, the code cannot find the callback function parameters and gives us an error. These are the DrawGame, ResizeWindow, keyInput, keyInputUp, keySpecialInput, keySpecialInputUp, UpdateGame parts of the above code.

There are two ways to get around this. We can either use lambda functions or use static functions inside the _GameEngine_ class. 

For lambda functions, instead of defining the function and function body for the callback function parameter, you instead write the function body directly into the brackets. This is best used for smaller function bodies, such as the keypress callbacks. 

**Note the [] before the parameters and that all that is inside the () of the glutKeyboardFunc line.**

```C++
	glutKeyboardFunc([](unsigned char key, int x, int y)
		{
			GameObject::keys[key] = true;
			std::cout << "Key pressed: " << key << " : " << GameObject::specialKeys[key] << std::endl;
			//If we press escape, quit
			if (key == 27)
			{
				CleanupEngine(); //changed to include the GameEngine function - this function will have to be static!
				exit(0);
			}
		}
	);
```
For static functions, we define that function as a function of the entire class rather than for the instance of the class. We have done static variables in the past for the _GameObject_ class and its key and specialKey maps so you should already have seen this.

We add the keyword _static_ before the output part of the function in the header. As these are functions and not variables, we do not have to declare them at the top of the .cpp file (like we did for the maps in _GameObject_). This is best used for bigger function bodies, such as DrawGame, UpdateGame, CleanupEngine and ResizeWindow.

**PLEASE NOTE: The only issue is that if the static function needs to access variables from the _GameEngine_ class, those variables also need to be static variables!**

```C++
	//2 examples - You will need more to be static
	static void CleanupEngine();
	static void ResizeWindow(int w, int h);
```

> Once you have sorted all the callback functions in whatever way you wish, it should clear up the errors and you can move on to the other functions. Give it a go yourself first and read below to guide you if needed after. **As always, ask for help if you need it!**

You will need to populate the following functions of the _GameEngine_ class:
* ResizeWindow
* DrawGame
* UpdateGame
* StartEngine
* CleanupEngine
* AddGameObject

### ResizeWindow() function

We can move the resize function from the _main.cpp_ into the new ResizeWindow function. Nothing changes in the actual code. This can be a static function or a lambda function. It's up to you which one you use for this.

```C++
static void ResizeWindow(int w, int h);

...

void GameEngine::ResizeWindow(int w, int h)
{
	glViewport(0, 0, w, h);
	glMatrixMode(GL_PROJECTION);
	glLoadIdentity();
	gluPerspective(60.0, (float)w / (float)h, 1.0, 500.0);
	glMatrixMode(GL_MODELVIEW);
}

...

glutReshapeFunc(ResizeWindow);
```

Or

```C++
glutReshapeFunc([](int w, int h)
		{
			glViewport(0, 0, w, h);
			glMatrixMode(GL_PROJECTION);
			glLoadIdentity();
			gluPerspective(60.0, (float)w / (float)h, 1.0, 500.0);
			glMatrixMode(GL_MODELVIEW);
		}
	);
```

### DrawGame() function

Again, we can move needed area from the _main.cpp_ across. As you can see nothing has changed in the actual code. 

We cannot do a lambda function for this so we will have to use a static function. We will also need to make the _objects_ variable static in the header and declare it at the top of the .cpp file (because _DrawGame_ uses _objects).

```C++
void GameEngine::DrawGame(void)
{
	glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);

	glLoadIdentity();

	// Position the objects for viewing.
	gluLookAt(0.0, 0.0, -10.0, 0.0, 0.0, 0.0, 0.0, 1.0, 0.0);

	for (int i = 0; i < objects.size(); ++i)
	{
		objects[i]->Draw();
	}

	glutSwapBuffers();
}
```

<br/>
<details>
  <summary> Forgot how to do a static variable? </summary>

In the header file.
```C++	
	static std::vector<GameObject*> objects;
```

In the .cpp file at the top (just under the include).
```C++	
	std::vector<GameObject*> GameEngine::objects;
```
</details>
<br/>

### UpdateGame() function

This has to be a static function as well. Move the code from the _main.cpp_ over into this function. Make sure you sort out the errors. - It has to do with static variables, like before.

```C++
void GameEngine::UpdateGame(void)
{
	oldTimeSinceStart = newTimeSinceStart;
	newTimeSinceStart = glutGet(GLUT_ELAPSED_TIME);

	float deltaTime = (newTimeSinceStart - oldTimeSinceStart);
	deltaTime /= 1000.f;
	
	for (int i = 0; i < objects.size(); ++i)
	{
		objects[i]->Update(deltaTime);
	}

	glutPostRedisplay();
}
```

### StartEngine and CleanupEngine functions

These are very simple functions which are needed to start the game and then clean up after the game closes. For cleanup, this normally means any memory we request via the _new_ keyword is returned via the _delete_ keyword. 

In our case, we use the _new_ keyword when creating game objects so we need to do this. 

If you have not already, make sure in your _glutKeyboardFunc_ function that an "Escape" keypress calls the _CleanupEngine_ function so you actually delete the objects afterwards!

```C++
void GameEngine::StartEngine(void)
{
	std::cout << "Press escape to exit the game." << std::endl;
	glutMainLoop();
}

void GameEngine::CleanupEngine(void)
{
	for (int i = 0; i < objects.size(); ++i)
	{
		delete objects[i];
	}
}
```

### AddGameObject function

Finally for the _GameEngine_ class, we need a way to add game objects into the _objects_ vector. At the moment, this is very simple but this could have extra functionality added to it later.

Why is this needed if it is so basic? Firstly, the _objects_ variable is private so nothing outside the _GameEngine_ class can access it to add objects to it. 

Secondly, if someone else used your code, they should not have to know the inner workings of your engine code. By having a function like this, they tell the engine about an object they want to add and behind the scenes, your holder of objects could be a vector, map, list, or your own collection class. All they care about is that the engine now knows about the object. 

This is a good practice in code generally (called "information hiding" if you want to research more) as a back end change will not affect the front end code. For example, if you changed the vector type to your own type, you would change this function body but the use of the function would remain the same for the user.

```C++
void GameEngine::AddGameObject(GameObject* object)
{
	objects.push_back(object);
}
```

### Changing the main.cpp

Now that the _GameEngine_ class is in place and the code from _main.cpp_ has been moved over, the _main.cpp_ becomes very simple. We can remove all the code we have moved over and set up the new look of the _main.cpp_. 

By doing all this, we have compartmentalized our code and gave the user of the code (both yourself and anyone else using your code!) public functions to deal with the engine and private functions that do tasks but do not need to be actually called by the user.

Hopefully, you can see that we could use this game engine to make a number of different games by adding and changing game logic, object types etc.

The key steps are:

* Include the GameEngine header - This header will include all the other headers needed.
* Create some objects
* Create an instance of the game engine
* Initialize the engine
* Add the objects to the engine (so it actually knows about the objects!)
* Start the engine (which causes the game loop to occur with your functions - Drawing, Updating etc.)

> Using your classes/objects, add some to the game engine and make sure it works as before. 

This is what the _main.cpp_ should look like. 

```C++
#include "GameEngine.h"

GameObject* particle = new Particle(10.0f, glm::vec3(0, 0, 0));
GameObject* rigidBody = new RigidBody(10.f, glm::vec3(0, 0, 0));

GameEngine engine;

// Main routine.
int main(int argc, char** argv)
{
	engine.InitEngine(argc, argv, "GameEngine Window", 500, 500);

	engine.AddGameObject(particle);
	engine.AddGameObject(rigidBody);

	engine.StartEngine();
	
	return 0;
}
```

## Homework
* Change the window title to something else. It's your engine so give it a cool name!
* Add comments and documentation to your class so it is easier for others (and yourself) to understand what is going on.
* Make all the functions static in GameEngine so you do not have to make an instance of it in the main.cpp. This will allow you to call the functions like GameEngine::InitEngine( ... ).
* Instead of using global instances of the objects like the example above, create them inside the AddGameObject brackets. How does this work?
* Having a debug mode is useful for game development. You might want to see information like object variable values or a visual representation of the acceleration and velocity or collision boxes. Think about how you would implement this into the GameEngine class.
* How would you remove game objects? For example, if a collision removes the object hit? Hint: This is harder than it seems... Be careful about dangling pointers! (Dangling pointers are when you delete an object but something is still pointing at it. When it tries to access the deleted object, your program will crash (hopefully).)


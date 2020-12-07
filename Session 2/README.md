# Session 2 - Using Classes

#### Table of Contents
1. [Why use classes?](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%202/README.md#why-use-classes)
2. [Creating a class](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%202/README.md#creating-a-class)
3. [Header and C++ files](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%202/README.md#header-and-c-files)
4. [Making a basic class](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%202/README.md#making-a-basic-class)
5. [Using the new class](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%202/README.md#using-the-new-class)
6. [Creating multiple instances of the class](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%202/README.md#using-the-new-class)
7. [Documentation](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%202/README.md#documentation)
8. [Homework](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%202/README.md#homework)

Last session, we created a GitHub repository in the 217CR organization and added our base Visual Studio project to it.

> You haven't done all of those things? Go back to Session 1 and complete that tutorial!

For this week's session, we will be setting up a basic class for practice for your coursework and looking into a student's worst nightmare - documentation of their code.

## Why use classes?

Looking at our current code, we can see where objects are drawn in the _drawScene_ function.

```C++
	glPushMatrix();
	glColor3f(0, 1, 0);
	glBegin(GL_QUADS);
	glVertex3f(5, 0, 5);
	glVertex3f(-5, 0, 5);
	glVertex3f(-5, 0, -5);
	glVertex3f(5, 0, -5);
	glEnd();
	glPopMatrix();

	glPushMatrix();
	glColor3f(0.55, 0.27, 0.07);
	glRotatef(-45, 1, 0, 0);
	glutSolidCone(0.5, 0.75, 30, 30);
	glPopMatrix();

	glPushMatrix();
	glColor3f(-2, 0, 2);
	glTranslatef(-2, 0, 2);
	glRotatef(-90, 0, 1, 0);
	glutSolidCone(0.5, 0.75, 30, 30);
	glPushMatrix();
	glTranslatef(0, 0, -0.4);
	glColor3f(1, 1, 1);
	glutSolidCube(0.8);
	glPopMatrix();
	glPopMatrix();
```

At the moment, while it is not the best code, it is still readable and not too large an amount of code.

However, as our engine gets bigger, and more objects are added to the overall scene, this way of representing objects in code will become unsustainable.

Imagine wanting 100's of objects in a game scene... You would need to have 100's of objects represented this way in the _C++_ file. The file itself would be very large and it would be hard to find individual parts of the code when you need to change/edit/debug it. 

Another issue arises when you want copies of the same object. Yes, you can  copy and paste that object code around the file. But what if:

* You need to change the x position of a certain object? Was the change in number 15 in the _.C++_ file or 115?
* You figure out after weeks of programming that a certain object has a bug or error in it. Time to change every single instance of that copied object's code! Of course, you'll always forget to fix one instance of it that will end up breaking your game!

To deal with this growth of our code, we can use an Object Oriented approach to our code design, which means using classes to represent objects.

## Creating a class 

Within Visual Studio, there are a few ways we can create a class.

There are 3 ways you can create a class.

* Via the _"Class Wizard"_
* Via _"Add"_ -> _"Class"
* Via _"Add"_ -> _"New Item..."_

> All three of these ways work the same and it is more down to personal preference. I would suggest you try all three to see which one you like the most.

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%202/Readme%20Pictures/Class%20creation.png">
</p> 

For all 3 methods, you can either access them via _"Project"_ in the top menu or by right clicking _Source Files_ or _Header Files_ in the "Solution Explorer".

For the _"Add New Item..."_ method (which you will have already used last week for creating a _main.cpp_ file), you have the choice of making just a blank header file, a blank C++ file or a class. (This will open up the _"Add Class"_ way of making a class.) This can be helpful for simple classes that just require a header file.

For the _"Class Wizard"_, click on _"Add Class..."_. (This will open up the _"Add Class"_ way of making a class.)

For the _"Add Class"_ method, type in the _Class Name_ section and it will automatically fill out the _.h file_ and _.c++ file_ names. Click _"OK"_ when you have a sensible name.

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%202/Readme%20Pictures/Add%20Class.PNG">
</p> 

Whatever method you used, you should have a header file (_.h_) and a C++ file (_.cpp_) file now present in the "Solution Explorer". Normally, the header file automatically opens so you can work on it straight away.

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%202/Readme%20Pictures/Class%20Created.png">
</p> 

> What is _#pragma once_ ? That's part of this session's homework!

## Header and C++ files 

Most classes we will make will have both of these files. 

A header file contains the class signature. This is how the compiler knows about the class and also where a programmer can quickly look to see what variables and functions the class has. (It has less code than the _.cpp_ file so its easier to read by a human... most of the time.)

A C++ file contains the implementation of the things presented in the class signature, such as the function bodies of the class (aka where all the stuff happens).

## Making a basic class

Let's make a working class in our project to see an example.

This class will just draw an object in the scene for us. We will improve it as we go.

> We will call this class Cube. Create this now using your chosen method.

For classes, we will need:

* 1 or more constructors
* A destructor (Although a default one is created behind the scenes if you don't do one)
* Some variables that hold data related to the class
* Some functions that do something related to the class and/or it's data

A constructor gives an instance of the class (or created object of the class) default values and is called automatically whenever we create that object in our code. It is the name of the class with brackets - _Name();_

A destructor is called whenever an object is deleted or goes out of scope. This is used for clean up. - _~Name();_

A function has a return type, a name and 0+ arguments. 

Make sure your **header file** looks the same as mine below.

```C++
#pragma once
class Cube
{
public:
	Cube();
	~Cube();

	void Draw();
};
```

Hopefully, it's obvious that the Draw function will at some point draw the object! This is why good function naming is a good programming practice. (What does abc() do for example?)

Some of your code will now have a green squiggly underline. This means that you have defined the code in the header file but Visual Studio cannot find the rest of the definition for that function/constructor/destructor. 

Open your classes corresponding C++ file... Empty! (Other than the needed _#include_ so the C++ file knows about the header file.) Time to change that. 

Now, we could type the needed parts out manually... But, as programmers, we are lazy (in a good way!). So why not let technology do the work for us? (Also, manually typing might include typos!) 

Hover your mouse over each part of the code with a squiggle and click on the screwdriver. Choose _"Create definition of ... in Cube.cpp"_ for all three of the needed areas.

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%202/Readme%20Pictures/No%20definition.png">
</p> 

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%202/Readme%20Pictures/Added%20functions.PNG">
</p> 

Make sure your C++ file looks like this.

```C++
#include "Cube.h"

Cube::Cube()
{
}

Cube::~Cube()
{
}

void Cube::Draw()
{
}
```

Now we have this skeleton, we can add the needed internal code to make the class useful. In this example, it will be the OpenGL code for drawing a cube.

```C++
void Cube::Draw()
{
	glPushMatrix();
	glTranslatef(0, 0, 0);
	glColor3f(1.0, 0.0, 0.0);
	glutSolidCube(1.0f);
	glPopMatrix();
}
```
Once this is added, you might notice that this is underlined with red squiggly lines. Hovering your mouse over any of these lines should give you a pop up window saying that _X is not defined_. 

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%202/Readme%20Pictures/OpenGL%20red%20functions.PNG">
</p> 

If we try to now run our code, we will get a number of errors messages as well as pop up window saying **There were build errors.** Click the _"No"_ option to go back to Visual Studio.

The "Error List" at the bottom of Visual Studio contains all the errors that stopped the project from compiling and running. Warnings do not stop it from compiling and running but are shown to us as a "You might want to look at this" type of message.

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%202/Readme%20Pictures/Error%20List.PNG">
</p> 

> When reading errors, start at the top of the list and read that first. **It might be that all the errors exist only because of this one.** This means that by fixing the top one, it may also fix all the others too. 

> Of course, if you get an error, if you do not understand it (which can happen when the error message is quiet vague!), look it up online first to see what could cause it. If you still do not know, ask for help from the module staff.

All the errors we have got tell us that the OpenGL functions are undefined or not found. What is the difference between the OpenGL code in the _main/Source.cpp_ and the OpenGL in the C++ Cube file?

The answer is the includes. The Cube header file does not have these, so when they are compiled, they know nothing of the OpenGL libraries. To fix this, we need to add the includes into the Cube header.

```C++
#include <GL/glew.h>
#include <GL/freeglut.h>
#include <glm/glm.hpp>
```

Once these have been added to the Cube header file, the red lines should disappear.

> Why include the libraries in the header file when we only use the library functions in the C++ file? The C++ file imports the header file when it is compiled via the _#include_ line at the top, giving it access to the includes in the header file too.

Make sure your Cube header file looks like this before you continue.

```C++
#pragma once

#include <GL/glew.h>
#include <GL/freeglut.h>
#include <glm/glm.hpp>

class Cube
{
public:
	Cube();
	~Cube();

	void Draw();
};
```

## Using the new class

If we run the project now, it still shows the normal static scene as before... All of our hard work for nothing!

What we need to do is actually use our Cube class in the rest of the project and create some instances of it.

This can be done by including our own class headers the same way we include the library headers.  This is done by typing _#include "Cube.h"_ at the top of our _main/Source.cpp_ file (just under the OpenGL includes).

> Why #include " ... " and not #include < ... >? That's a homework task!

Once this is done, we can use the Cube class in the rest of that file.

We will create a global instance of a Cube at the top of the file. What happens if we run it now?

```C++
 ...
#pragma comment(lib, "glew32.lib") 

#include "Cube.h"

Cube cube;

// Drawing routine.
void drawScene()
 ...
```

Still nothing has changed in the scene! This is because while we have made an instance of a cube, we are not using the 
_Draw()_ function that contains the code to actually draw it. Where could we call the function to draw a cube?

Hopefully, it is obvious this happens in the _drawScene_ function, where the rest of the drawing code is. Add the line _cube.Draw()_ anywhere after the _gluLookAt_ line and before the _glutSwapBuffers_ line in the _drawScene_ function (and also not in the middle of any of the other draw code sections already there).

Running the project now will give us a red cube in the middle of the scene. Yay!

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%202/Readme%20Pictures/red%20cube.PNG">
</p> 

## Creating multiple instances of the class

We have one cube in the scene. Let's add more! We can do this by creating more instances and calling the _Draw_ function of each of them in the _drawScene_ function. 

> Create 2 new instances of cubes and call the _Draw_ function for each of them. I will call mine cube2 and cube3 (so that I have 3 instances of the Cube class in my scene). Check the example below when you have done this.

<br/>
<details>
	<summary> Example Code </summary>
	
```C++
Cube cube;
Cube cube2;
Cube cube3;

// Drawing routine.
void drawScene()
{
	...
	cube.Draw();
	cube2.Draw();
	cube3.Draw();
```

</details>
<br/>

Running the project shows us the same scene... But we have 3 cubes now and should see 3 cubes. Why is this the case?

Looking at the _Draw_ function they are all in the same position, so they are in the scene but are being drawn over each other.

To fix this, the Cube class will have to hold a position value so each instance of a Cube can be placed in another position. This will involve adding a new variable to the Cube class and changing the _Draw_ code. 

> A position is an x, y, and z value in space, given a coordinate system. We can use another library (the GLM library) to give us access to the _vec3_ class (Vector 3D) to save us having to write our own maths library.

We have already included the right library code (_#include <glm/glm.hpp>_) so we can go straight to adding a variable to the Cube class. Do this now and give it a sensible name. (Check my example below when you are happy.)

<br/>
<details>
	<summary> Example Code </summary>
	
```C++

class Cube
{
private:
	glm::vec3 position;

public:
	Cube();
	~Cube();

	void Draw();
};
```
</details>
<br/>

We need a way to give _position_ a value because at the moment it will be a default (0, 0, 0) value and is also not used anywhere.

> As the variable is private, we cannot change the value of position in the _main/Source.cpp_ like _cube.position = glm::vec3(1, 0, 0);_. Part of your homework is to find out why.

We can create a new constructor that, given a _vec3_ value, gives that instance of Cube that value as it's position. We will define the layout in the header file and then write the body in the C++ file. (You can use the automatic code generation we saw earlier to do the body for you!)

> Create a new constructor that takes in a _vec3_ value and then assigns that value to the _position_ variable. When you are happy, make sure your code does the same as the example below.

<br/>
<details>
	<summary> Example Code </summary>
	
```C++

#header file

public:
	Cube();
	Cube(glm::vec3 pos);
	
#C++ file
	
Cube::Cube(glm::vec3 pos)
{
	position = pos;
}
```

</details>
<br/>

The final code change to the Cube class we will do is in the _Draw_ function. (If you ran the project now, there would still be no change). We will change the _glTranslatef_ line so it uses the value of the _position_ variable instead of the current "hard coded" value of _0, 0, 0_. 

> Change the _glTranslatef_ line so it uses the position variable. Check this against the example below.

<br/>
<details>
	<summary> Example Code </summary>
	
```C++
void Cube::Draw()
{
	glPushMatrix();
	glTranslatef(position.x, position.y, position.z);
	glColor3f(1.0, 0.0, 0.0);
	glutSolidCube(1.0f);
	glPopMatrix();
}
```

</details>
<br/>

Now we just need to use the new constructor for the instances we create in the _main/Source.cpp_.

Give each cube the following positions.

  Object     |  Position Value
------------- | -------------
cube          | (1, 0, 0)
cube2 | (0, 2, 0)
cube3 | (3, 0, 1)

Once you have, run the project and check your scene to the example below.

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%202/Readme%20Pictures/3%20cubes.PNG">
</p> 

## Documentation

As a programmer, your comments allow others (and your future self!) to read your code and learn about any logic explanations, written explanations, assumptions or issues in the code. This combined with good naming practices (for classes, variables and functions) and clean code make sure your code is serviceable, readable and obvious. You will be surprised how much of your own code base you will have forgotten when you come back to it months later.

> Doing these things will put you above others when it comes to coursework and in industry. Any portfolio code will be looked at prior or after any games interview so make sure it is good code!

Adding comments that say very little or explain obvious stuff detract from that readability so comments should be for essential areas only. For bigger/team projects, documentation is needed to explain the structure and use of the code as you may be working on code that multiple people have written.

Doxygen is C++ documentation generation tool that takes comments (in a certain format) from your code and turn them into a set of web page documents that document all your code, including classes and functions.

Turning this:

```C++
/*! A test class */
 
class Afterdoc_Test
{
  public:
    /** An enum type. 
     *  The documentation block cannot be put after the enum! 
     */
    enum EnumType
    {
      int EVal1,     /**< enum value 1 */
      int EVal2      /**< enum value 2 */
    };
    void member();   //!< a member function.
    
  protected:
    int value;       /*!< an integer value */
};
```
into this:
http://www.doxygen.nl/manual/examples/afterdoc/html/class_afterdoc___test.html 

> Personally, I've used DoxyWizard which is a GUI for using doxygen. Find out more information at http://www.doxygen.nl/manual/doxywizard_usage.html . Let me know if you have any questions about how this works.

Creating neat, readable code is more like an art than programming skill and although I do not demand you to use documentation, spending time on this keeps everyone reading your code (including yourself) happy. Don't you want you to be happy?

## Homework

* Read into _#pragma once_, why it is needed and what a preprocessor directive is.
* Research the differences between public, private and protected in C++ classes. What is the default setting of a class?
* Why do we use #include " ... " and not #include < ... > for classes we create ourselves?
* When using the GLM library, we have to write glm:: before any vec3. This is called a namespace. What is a namespace and how can it be useful?
* Cubes should be able to be any colour! Create a new variable, constructor and alter the _Draw_ code for Cube to allow this. Use the new constructor on the 3 cubes to give them different colours.
* Download DoxyWizard on your own machines and try to create some documentation on this weeks work.

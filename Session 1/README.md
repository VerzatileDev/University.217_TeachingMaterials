# Session 1 - Setting up the project and GitHub

#### Table of Contents
1. [Setting up in GitHub](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%201/README.md#setting-up-in-github)
2. [Cloning the repository](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%201/README.md#cloning-the-repository)
3. [Creating a C++ Project using Visual Studio](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%201/README.md#creating-a-c-project-using-visual-studio)
4. [Using OpenGL with the project](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%201/README.md#using-opengl-with-the-project)
5. [Base Code](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/tree/master/Session%201#base-code)
6. [Pushing the changes to the repository](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%201/README.md#pushing-the-changes-to-the-repository)
7. [The power of the .gitignore file](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%201/README.md#the-power-of-the-gitignore-file)
8. [Homework](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%201/README.md#homework)


Welcome to this 217CR tutorial series! 

> By following these sessions, we will create the underlying areas needed for a physics engine. As always, if you have any questions (or spot any typos), feel free to talk to course staff or email me at ab8809@coventry.ac.uk.

For this week's session, we will be setting up GitHub and starting the base C++ project. This will get us ready for the later weeks and become a start for your coursework.

## Setting up in GitHub

As you will know by reading the assessment brief, your coursework has to be on the Coventry GitHub server and be in a certain organization. **(If it is not, it may receive a 0 mark!)**

Go onto the Coventry GitHub server via https://github.coventry.ac.uk and sign in via the top right. _This will be your normal username and password you use to log into university PCs._

> **If you cannot sign into the server, talk to me / email me ASAP as this needs to be sorted out.**

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%201/Readme%20Pictures/Sign%20in.PNG">
</p> 
 
Once you have logged into the server, go to the 217CR organization in 1 of 3 ways.

> **If you cannot access the organization, I have to manually add you to sort it. Talk to me ASAP.**

* On the main screen, clicking the dropdown list on the left side and selecting 217CR-2021 and then _"View organization"_.
* On the main screen, clicking your profile picture in the top right, then _"Your Profile"_, then selecting 217CR-2021 via the icon on the left side.
* Using the direct link - https://github.coventry.ac.uk/orgs/217CR-2021

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%201/Readme%20Pictures/Organizations.png">
</p>
 
 Once there, create a new repository via the green _"New button"_.
 
 * For the repository name, call it something obvious (and add your student ID) to the front.
 * Set it to private (or everyone in the university can see it!).
 * Initialize a README file.
 * Choose a VisualStudio .gitignore file.
 
 By doing all of this, it means that the markers will be able to see it, that other students cannot, that you can clone it straight away to your PC and you can commit changes without having to close Visual Studio.
 
<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%201/Readme%20Pictures/Create%20Repository.png">
</p>

 Finally, add the module staff to your repository so they have access to it. (Our information can be found on the 217CR Aula page under _Module Essentials_ -> _Module Guide_.)
 
 Go into your repository from the website, click "Settings", then "Collaborators", and then search for, and add, both members of module staff (Ian and Will) to the page. 
 
 <p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%201/Readme%20Pictures/Add%20Collabs.png">
</p>

  ## Cloning the repository
  
Once you have made your repository, it should take you to it's page. (If you have backed out of this, you can always find it by clicking on your profile picture and selecting _"Your repositories"_.)
  
The next step is to clone this repository to your PC so you have a local copy to do changes on.

> For these tutorials, I will be using the command line (_cmd_) to use Git. You are allowed to use programs like GitHub Desktop (which can be accessed via AppsAnywhere on University PCs) but if you come into problems with them, **staff might not be able to help**.

To clone a respository you will need the URL of it. Copy this from the address bar of your browser or use the green _"Clone or download"_ button and copy the URL from the pop up box.

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%201/Readme%20Pictures/Clone%20or%20Download.PNG">
</p>

  Open command line by typing cmd into Windows search. 
  
  Once there, you might have to navigate to your user area on the hard drive.
  
  The following cmd commands will be helpful to do this.
  
  Command     |  Explanation
------------- | -------------
C:            | Will change what hard drive disk is currently looked at.
cd "folder name" | Go into that directory / folder.
cd .. | Go back a directory / folder.
dir      | Print out what is in the directory.
mkdir "name for directory" | Create that directory / folder.
git clone "url" | Clones the repository into the current directory you are at.

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%201/Readme%20Pictures/cmd%20steps.PNG">
</p>

> _These are the steps I took to get to my Documents folder for example._

**Make sure you are at a sensible place directory wise before you clone your repository so you can find it later!**

Once at a good directory, git clone your repository. It might ask you to log in with your GitHub credentials. 

When you have submitted them, the repository (and it's files) should now be on your PC.

At the moment, it should contain only the the README and gitignore files. (And a hidden folder called .git)
  
<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%201/Readme%20Pictures/Cloned.PNG">
</p>

It is here that we will create a C++ project so it is in the GitHub repository, meaning we can push and pull changes to it.

## Creating a C++ Project using Visual Studio
 
> If you are not using Visual Studio 2019

* Open Visual Studio and Click _"File"_ -> _"New"_ -> _"Project.."_
* Select _"Empty Project"_.
* Make sure _"Create directory for solution"_ is unticked.
* Give it a sensible name.
* Make sure the location is the same as where the README file is stored.

> If you are using Visual Studio 2019, click "Continue without code".

* Click _"File"_ -> _"New"_ -> _"Project.."_
* Pick _"C++"_ as the Language to filter the list.
* Select _"Empty Project"_ and then _"Next"_.
* Give it a sensible name.
* Make sure _"Place solution and project in the same directory"_ is ticked.
* Make sure the location is the same as where the README file is stored.

Once this has been done, click _"Create"_.

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%201/Readme%20Pictures/Empty%20Project.PNG">
</p>

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%201/Readme%20Pictures/Creating%20Project.PNG">
</p>

We need to create the _.cpp_ file that will hold the _main()_ function. Remember, every C++ program needs a _main()_ function to run.

This can be done by right clicking "Source Files" on the right hand side (in the Solution Explorer) and clicking _"Add"_ -> _"New Item..."_.

Make sure C++ File (_.cpp_) is highlighted and either change the name (I always use _main.cpp_) or keep it as _Source.cpp_ and click _"Add"_ in the bottom right.

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%201/Readme%20Pictures/Add%20file.PNG">
</p>

Type in the following code to get us started and make sure it all works. Once you have typed it in, click _"Local Windows Debugger"_ (or hit _"F5"_) at the top middle of the screen to compile and run the code.

```C++
#include <iostream>

int main(int argc, char** argv)
{
	std::cout << "Hello world!" << std::endl;
	
	system("PAUSE");
	return 0;
}
```

That should pop up a console window with _"Hello world!"_ in it.

## Using OpenGL with the project

> The following section is aimed at Windows machines. If you use a Mac or Linux, this will be different for you.

Now the project is created, we will need to display something to the screen to see the physics in motion.

> For this, I will use a legacy version of OpenGL which will most likely differ from what you are taught in 212CR. The graphics implemented as part of this tutorial series are **not enough to pass in the 212CR module.**

The libraries we will be using are:

* GLEW
* FreeGLUT
* GLM

These are the same versions as the ones from 212CR so they should not cause any issues overall.

> You can download the library files I use through-out this tutorial series by downloading and extracting the _"Library Files"_ .zip file on the module Aula page.

These libraries are created for a certain build of project which can be a mix of Debug or Release and Win32 (x86) or Win64 (x64). For our case, these are for both Debug or Release and Win64 (x64).

We can change these in Visual Studio via the two dropdowns next to _"Local Windows Debugger"_. Make sure your project shows _"Debug"_ and _"x64"_.

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%201/Readme%20Pictures/Debug%20x64.PNG">
</p>

We now need to place these library files in a place where Visual Studio (and your code) can see and use them. Download (and extract) the files from the _"Library Files"_ .zip file if you have yet to.

 Files you should have after extraction   | 
------------- | 
include folder | 
freeglut.dll | 
freeglut.lib | 
glew32.dll |
glew32.lib | 


This is a 2-step process involving:

* Moving all the library files somewhere helpful
* Telling Visual Studio where these files are

For moving the library files, we shall place them where the project files are. This makes sure that the project always has the files (and a computer change / USB stick removal does not remove them!).

> Don't know where your project files are? Right click the project name in "Solution Explorer" and select _"Open Folder in File Explorer"_. 

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%201/Readme%20Pictures/open%20folder.PNG">
</p>

Your project files should now look something like this. (Of course, your project name will be different to mine!)

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%201/Readme%20Pictures/Added%20library%20files.PNG">
</p>

Moving the files is not enough. We now need to tell Visual Studio where these files are by 'linking' them.

We can do this via the "Property Page" of the project. You can access this in 1 of 3 ways:

* Click _"Project"_ at the top left of the screen, then _"Properties"_.
* Right click the project name in the "Solution Explorer" and select _"Properties"_.
* Click your project _(not the Solution)_ in the "Solution Explorer" to highlight and hit _"Alt + Enter"_ at the same time.

When the "Property Page" window has opened, make sure it says "Active(Debug)" and "Active(x64)". (If it does not show that, close the window, change it in the 2 dropdowns in Visual Studio and come back to the "Property Page" window.)

* Select "C/C++" and type include into the _"Additional Include Directories"_ textbox.
* Select "Linker" and type include into the _"Additional Library Directories"_ textbox.

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%201/Readme%20Pictures/Linking.png">
</p>

## Base Code

This week, we will just add some base OpenGL code. This is a static scene (so nothing moves) and is just to make sure everyone has got a working Visual Studio project that can run OpenGL content ready for later weeks.

Copy all the code from the _"BaseCode.cpp"_ which is on the Week 1 GitHub page (https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%201/BaseCode.cpp) and paste it over everything in your _"main.cpp"_ or _"Source.cpp"_.

Run the code to get the following scene.

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%201/Readme%20Pictures/Basic%20Scene.PNG">
</p>

> If you do not get the following scene, something is wrong. Look over the above tutorial again and see if you missed anything. The error messages shown in Visual Studio should give you a loose guide. If you get stuck, ask for help from the module staff.

## Pushing the changes to the repository

We return to using Git now to save our progress on the project. Once you have everything working without errors, we will push our changes to the project (and to the repository) to the GitHub server.

We do this via 3 commands (which you should be used to from ALL!):

Git commands  | 
------------- | 
git add . | 
git commit -m "some kind of message" | 
git push | 

Go into command line and navigate to where your repository is saved. (Make sure you are in the folder that contains the README and your project files! You can check with a _dir_ command like in my example below.

Once there, use the three above commands to push your changes to GitHub. You can check the website version to make sure the push has happened.

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%201/Readme%20Pictures/Push.png">
</p>

> Did something stop you using _git add ._? Either way, read the next section about _.gitignore_!

## The power of the .gitignore file

The reason we added a .gitignore file was to stop the following error when we try to use _git add ._ . This error occurs when the project (and Visual Studio) are still open at the time of the _add_ command and both Git tries to access the project file when Visual Studio already has it open.

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%201/Readme%20Pictures/Add%20issue.PNG">
</p>

> Without a .gitignore file, you would have to close Visual Studio fully and then use the 3 commands to push the changes to the repository. Of course, this gets annoying fast if you have to close and re-open Visual studio every time you want to push something!

A .gitignore file means that whatever file extensions / folders are written in it are not considered when pushing and pulling from the repository.

This allows us to remove the files that would be open when Visual Studio is, meaning we:

* Can push without the above error
* Stop unneeded files from being pulled/pushed saving on download time!

This should now allow you to add, commit and push future changes with Visual Studio open. It should also not bring in unneeded Build folders when you pull and clone your repository in the future saving on downloading MBs of extra data.

There is one issue with the current .gitignore file... If you want to add .obj files later for 212CR, the gitignore will cause them not to be pushed to the repository. To counter this, click the file when on the Coventry GitHub website and remove 2 lines from the .gitignore file and then commit these changes (as shown in the picture below).

<p align="center">
	
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%201/Readme%20Pictures/Edit%20gitignore.png">
</p>

## Homework

* Make sure you understand how GitHub works. Make sure to _git clone_ your work onto your home computer / laptop so you can work on the project outside of lecture / lab hours.

<br>This will make sure you understand how Git works.</br>

> Make sure to _git pull_ when you are on another machine when you do changes so you have the latest files as your local copy!

Although graphics will not be marked as part of 217CR, you will still need to understand how OpenGL works.

* Can you tell which part is responsible for drawing what? 
* Can you tell what each glut function is for? If you are unsure, look them up on OpenGL deocumentation or ask for help.
* The FreeGLUT functions use callbacks.  What is a callback?
* Why does idle() makes a call to glutPostRedisplay()?

* The callbacks currently use functions, such as glutIdleFunc(idle). <br/> Some of these functions are very small (~3 lines). Defining a separate function for this seems like a lot of effort and it can also confuse you as a programmer. It might be worthwhile to not have to specify a function. 

> Fortunately you can actually do this using lambda functions (since C++ 11). http://en.cppreference.com/w/cpp/language/lambda

* Try to re-write some of the glut function calls via lambdas. For example re-write glutKeyboardFunc(keyInput) to not use the _keyInput_ function, but still close when ESC is pressed.

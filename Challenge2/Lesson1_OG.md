---
title: "Lesson1_OG"
permalink: "/Lesson1_OG/"
layout: page
---

Official Learning Resource: [LearnOpenGL - Creating a Window](https://learnopengl.com/Getting-started/Creating-a-window)

## 1. Initialization of the project

In this lesson we will explain step by step how to create our first window using OpenGL.

## Creating the empty project with Visual Studio 2022

We will create this window using the Visual Studio 2022 IDE. The first thing we have to do is create a new project with Visual Studio, we will choose the C++ language and the Empty Project option

![1](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/2a90cf2e-23f2-4b13-82cf-fc238b93b789)

Give the project an appropriate name and save it where you see fit.

Remember to set the project to Debug and 64bits

![2](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/aba0210e-b485-47ee-a837-490d7c0e9455)


We will add a Game.cpp file.

Once we have the project created, the first thing we need to do before we start creating stunning graphics is:

 create an OpenGL context
an application window for drawing.

This couple of points are specific to the operating system we work on and, moreover, OpenGL tries to abstract itself from these operations. This means we have to create a window, define a context, and handle user input ourselves.

Fortunately, there are quite a few libraries that provide the functionality we're looking for, some specifically targeting OpenGL. These libraries save us all the OS-specific work and give us a window and an OpenGL context to render. Some of the most popular libraries are GLUT, SDL, SFML, and GLFW.

We will use GLFW. Feel free to use any of the other libraries, the setup for most is similar to GLFW.

## 1.2 Download and install GLFW:

GLFW is a specific library to be used in OpenGL, Basically it will allow us to create an OpenGL context, define the parameters of the window and manipulate the user's inputs.

First of all we have to go to the website:https://www.glfw.org/download.html, and download the 64-bit binaries for windows.

![3](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/5e29abf8-b981-4653-8061-c41e64117234)

We will go to our project folder and create 2 new folders:

(optional) 3rdParty: here we will save the original files of the external libraries used
Dependencies: This is where we will store the files required for our application

We will extract the contents of the glfw-3.3.8.bin.WIN64 file to the 3rdParty folder and in the Dependencies folder we will create a new folder called GLFW, and inside we will create two folders one called include and one lib:

![4](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/4a73c064-9b60-41ee-a697-41988f74b831)


We'll copy the contents of glfw-3.3.8.bin.WIN64\include into the Dependencies\GLFW\include folder so that it looks like this:

![5](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/7ed58fed-d0a0-4306-8983-7179f6643af9)


We will copy the contents of glfw-3.3.8.bin.WIN64\lib-vc2022 to Dependencies\GLFW\lib so that

![6](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/55a5b9a0-b4ee-47af-b0a1-53b0a67f7830)


## 1.3 Initialize GLFW in our project:

In order to use GLFW in our project, we need to include the files in the include folder and the lib.

Open the project properties window, from the Project->Properties menu or alt+F7. Make sure you have the Debug and x64 options selected
![7](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/bdc1f696-80b8-44d7-ae7e-0ff75a2b6faa)


To include the files in the include folder, select the C/C++ -> General section, and in Additional Include Directories add the line $(SolutionDir)Dependencies\GLFW\include\

![8](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/96528bff-09b1-47e4-af6f-7db39fcbacb5)


To include the files in the lib folder, select the Linker->General section, and in the Additonal Library Directories section add the line $(SolutionDir)Dependencies\GLFW\lib
![9](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/1dc083cf-ac4d-4b00-8ff1-1397243ab314)

It will also be necessary to add the glfw3.lib library to the Linker->Input section, to the Additional Dependencies section
![10](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/92c4f66a-bff3-43d0-b4f3-9928a0942610)


download it and install GLAD: Go to the page:[link](https://glad.dav1d.de/)

Select the C++ language, in Specification choose OpenGL, in API choose version 3.3, in the Profile section choose Core and finally check that Generate a loader is checked. Click the Generate button and download the generated .zip.
![11](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/5e3df07b-bc52-4ca5-a975-cf37ac05ba02)


The zip file contains two folders, we will add the folder called include to our project as we did earlier and add the glad.c file to your project.


We have everything done, in the Game.cpp file we add
```
#include <happy/happy.h>
#include <GLFW/glfw3.h>

int main()
{
	return 0;
}
```
Remember that first we must always include the glad include, since this includes the OpenGL header files (for example GL/gl.h) that are used by other libraries such as GLFW.
We should compile and check that it does it successfully, that no error appears, running this code doesn't produce a window or anything.
In the next lesson, we will create our first window in OpenGL.



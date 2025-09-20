---
title: "Lesson2"
permalink: "/Lesson2/"
layout: page
---

## .h and .cpp files

Before learning how to add/delete files from a project, we will explain why, when we program in C++, and especially if we do OOO programming, we highly recommend creating two types of files:
	The .h, .hpp or headers or interface files
	The .cpp, .cxx, .c o implementation  

For .h or header files, the idea is that the contents of these files contain the declarations of things like C++ types, programmer-declared types, function declarations, as well as structure or class declarations.

Remember that declaring anything in C++ only indicates that they exist, not how they are implemented. Anything declared in a header file can be used by other files.

In the cpp or implementation files is where we have the definitions or logic of everything we declared in the header file.

Why are we interested in having two files?
-	By encapsulation, we want to provide a class or structure with its functions, but we don't want the end user to see or have access to the implementation.
-	reuse: this way we can reuse the declaration of a function in different cpp.


## Creating/Adding files to our project

From a new VS project created, we will add a new file (.cpp), to carry out this action we will have to:

To create a new file in our solution or project, right-click on the "Solution Explorer" window, click Add -> New Item...

![1](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/bf779913-6e0d-4fb4-90e5-00aac87187f9)

A new window will open where we can choose the type of file we want to add, as well as its name and where we want to save it, this last point is very important, since by default it will be saved at the root of where find the solution

![2](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/4ecf8920-6959-43d3-80a4-ded69f5788e6)

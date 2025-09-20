---
title: "Lesson4"
permalink: "/Lesson4/"
layout: page
---

## Compiling/Building Pipeline

Before we start explaining how to debug, we will first explaining and justify how the pipeline works for:

1. Create one or several files in C++
2. Compile them
3. And finally run the resulting compilated binary file.

The basic workflow of writing a simple program begins by creating and writing text files with C++ syntax, these files will be passed through a compiler, which will compile them, and if the compilation is correct, it will create a binary file.

In this example we want as a result a binary file (exe), but keep in mind that we also can have as a resulting file a static library (.lib) or a dynamic library (.dll).

Imagine we have a little code in the project:

 ![1](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/01cc3597-1e88-4a08-8efa-dd193265b4e0)


(If you want a much more extensive explanation of the phases involved in the process of compiling a C++ file can be found here: [link](https://en.cppreference.com/w/cpp/language/translation_phases#Phase_4)
)
We don't need to go through all of them, but the most significant ones:

-	A very important phase that the compilation process goes through is that of preprocessing,

One of the first things a compiler does when it needs to compile a file is to preprocess all the preprocessor statements that exist in that file. In addition, any line thatstart for a hash (#),and a preprocessor statement. Some examples can be:
	#include
	#define
	#pragma


For more information on preprocessor declaration types go here: [link](https://en.cppreference.com/w/cpp/preprocessor)


In our example we find #include,what is a preprocessor statement, here the compiler will look for this file (the iostream file) and add/copy all its contents to this file.

Then, once all the preprocessor statements have been evaluated, the compiler will convert all the C++ code in the file or files into machine code.

Use:
Returning to our code, we see that we find the main, which in C++ is called the entry point, every program in C++ must have an entry point where our program begins to execute, once the program begins execution at the entry point, the following lines will be executed in order, unless there is acommand of change of execution order

The compiler can create different types of executables, more or less optimized, with debugging information included or not, etc... how does the compiler determine the type of compilation to do? Well, Visual Studio is based on these twomenus selectable:

 ![2](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/b54ddf79-08b3-4f73-8c58-ec5491f51302)


The first drop-down list is related to the solution configuration, and indicates only a group of rules that apply when a project is compiled, and the second drop-down list is related to which resulting platform we are building our executable against.

By default VS defaults to Debug and x86 ( Win32 ) options. VS has by default these two build options (Debug and Release) and for the platform we can find x86 and x64 by default.
 
  ![3](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/56cf421f-d374-42ed-bf43-96aeeb8ce6b5)![4](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/2927c971-e2c9-4efc-ae44-097a622f5949)



So for example, if we have Debug and x86 settings, that means our executable will contain informationdebuggable and will be generating a 32-bit application for Windows.

Returning to the compilation phase, it is important to mention that only the cpp files will be compiled, the .h files are never compiled, since the contents of these are added in the cpp files that include them with the #include, there it is where it will be compiled.

So in this way we will have many cpp files compiled individually, generating each of them an .obj file. Once the compiler has created all these .obj files from all the cpp files in our solution, we need to combine them all to create a single binary file (.exe), this will be the function of the Linker.

The linker basically takes all these .obj files, and matches them in such a way that we get a single file.

In VisualStudio we can configure the Linker in the "Linker" tab. Highlight:
-	In the "General" section: we can define external directories to access static or dynamic libraries, in case our solution needs them.

 ![5](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/b0923688-2018-4c07-815d-9b05493b4f38)


## COMPILE

Now that we understand the phases of building our project, let's see how we can build in VS.

1.	To compile a single cpp file we can do thisclicking ctrl+F7, or from the VS toolbar, this option is not found by default, to add it we will have to:

		i. By clicking the right mouse button on the toolbar, we add the "Build" section
 
![6](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/3b66e3d7-a62f-44b3-ba53-79218bf18c7e)

		ii. Click the drop-down list of thissection i anar a “Add or Remove Buttons’-> Customize ”:
  
 ![7](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/56e21dd8-ec34-4ac2-a764-0a45638f0fbe)

In the new window, click on "Add command":

 ![8](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/777bf7d4-b330-422b-bbef-e0e15a7fe9ae)


Select the “Build” section and scroll down to select the compile button

 ![9](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/ee318753-c48d-442c-b8c9-f2deac62b877)



2.	 Click the button to compile project or solution in itmenu superior 
 
![10](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/b554727b-ccff-4fd3-be18-7b4b2d2be6ab)


## DEBUG
It is important that you distinguish between a compilation error and a linking error, because their resolution is different.

Compilation errors are basically due to an error in the syntax of the code (missing a semicolon, undeclared variable, etc...)

In VS, compilation errors appear as follows in the Error list window:
 ![11](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/68260609-966c-4ffa-a764-57668d345e75)

 
And in the output window:

 ![12](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/9e137c06-4ecc-4812-9d4b-29b0c14957d5)


We recommend using the ouput window to consult/solve errors as it provides more information than the Error List, which can be used for an overview but not as a viable source of information.


Linking errors appear like this:

 ![13](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/4a1bbbe9-8ce9-4f1c-971f-11c38f4d5390)


I to the finestra d'output:

 ![14](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/ffe85b2b-313e-4279-a978-aa310bcdbe45)


Mostly Linking errors occur because a cpp file cannot find the definition of a function.










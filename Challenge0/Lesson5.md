---
title: "Lesson5"
permalink: "/Lesson5/"
layout: page
---
## Debugging using breakpoints

In this document we learn how to add breakpoints, which will help us interrupt the execution of our program and check the values of the variables at that moment.

A breakpoint is a point in our program where the debugger will pause the execution in the line where we have placed this breakpoint, and this allows us to see the state of our program, or in other words, see which values, in memory, contain the variables and structures of our program.

As we will see in the next examples, knowing the state of the memory is incredibly useful for diagnosing the problem in our code, since we can check at any time what the values of the variables are and diagnose whether this value is the correct one or not.

## How to add a breakpoint in VStudio 2022

A useful tip is to try to debug in "Debug" mode, since the other modes change a little how the code is interpreted, and it is possible that some breakpoints do not correspond to the selected line or it simply does not stop.


A breakpoint can be added, just by clicking F9, in this way a red dot will appear in the selected line of code, or simply by clicking on the left bar with the left mouse button.

 ![1](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/78337268-6075-4739-a36d-4a20473805d9)


If we then click on the "Local Windows Debugger", which ensures that we run our program with the debugger attached to our program
 

We will see that the execution of our program stops where the red dot is located, where a yellow arrow now appears, which indicates where the execution point of our program is located. The yellow arrow indicates the line that you want to debug, even though this line has not yet been executed.

 

At the top of the menu we will find tools that will make it easier for us to handle the execution, for example:

 

-	The continue button will allow us to continue the execution as if nothing had happened. or F5
-	Button "Step into" or F11, "Step Over" or F9 and "Step Out" or Shift+F11, these 3 buttons will allow us to control the execution step by step:


"Step into" or F11 will allow us to "enter" the function where the execution is at that moment, in our case it will be the Log function.
 
"Step over" will allow us to pass over or pass the debug point to the next line of code, in our case std::cin...

"Step out" or Shift+F11, will allow us to exit the execution of the function where the debug point is located, and go to the line of code that called this function, in our case, it is main.
 

When we're debugging we can hover over the variables and examine their contents, for example:

 	

In this case we can examine the value of time_span, which has just been calculated on this exact line. Here what we are doing is reading the value that this variable has in memory.

We also have three windows that will help us investigate the value of the memory at that time.
-	Auto Window and Local Window: local variables of the function you are currently in will be displayed here.
 


-	Watch window: this window allows us to monitor the variables that we consider. In the example we are interested in knowing the value of time_span at any time, to achieve this we just have to write the name of the variable in the "Name" column.
  

## Visualization of the memory status

In VS we have a window, very similar to the ones explained above, which allows us to see the state of all the memory. To open this window we will have to go to Debug->Windows->Memory (Important: This option only appears when we are running our code)

 
A window like this will appear showing us all the memory of our program. 


On the left we can see the memory addresses, in the center we can see the values saved, represented in hexadecimal, in these addresses, and on the right we can see the ASCII interpretation of these values.

If we want to find the memory position occupied by a specific variable in our code, we will need to know its memory address, and to achieve this basically what we have to do is at the top write the name of our variable with a " &" in front.
 

If we click enter, we will see how the value of the memory address corresponding to this variable appears

 


And as we can see, when we put a breakpoint in the initialization of this variable we can see how the memory content is updated:

 

Example: We will add a new variable (int) initialized with the value 8.
 
We see that since the variable "a" is an int, 4 bytes have been modified and the value 8 saved. (Remember that 2 digits represent a byte)

 


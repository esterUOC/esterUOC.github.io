---
title: "Lesson2_OG"
permalink: "/Lesson2_OG/"
layout: page
---
## 2. First window

In this lesson we will explain step by step how to create our first window using OpenGL.

## 2.1 Using the GLFW library
Let's initialize a GLFW window. Inside main we are going to write:
```
int main()
{
	glfwInit();
	glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
	glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
	glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
	//glfwWindowHint(GLFW_OPENGL_FORWARD_COMPAT, GL_TRUE);
	return 0;
}
```

glfwInit initializes GLFW, and then we can initialize a window using the glfwWindowHint command. This function is characterized by the fact that as the first argument we indicate which option we want to initialize, and as the second argument we indicate the value. You can find all the possible configurable options in the GLFW documentation.

In our case we indicate that we want to use OpenGL version 3.3, and the core mode, as we explained in lesson 0.

Next we will add the following code to create the window:
```
	// Creating the window
	GLFWwindow* window = glfwCreateWindow(800, 600, "LearnOpenGL", NULL, NULL);
	if (window == NULL)
	{
		std::cout << "Failed to create GLFW window" << std::endl;
		glfwTerminate();
		return -1;
	}
	glfwMakeContextCurrent(window);
```
The function glfwCreateWindow, to create the window, needs the following parameters, the width, the length, the name we give to the window, the two following parameters can be ignored.
If the window object is created successfully, then we create the context for our window with flfwMakeContextCurrent.


## 2.2 GLAD Library Usage
Right after the GLFW code we will add the code that initializes GLAD, remember that it is necessary to run this code before calling any OpenGL functions.
```
	// Initialize GLAD
	if (!gladLoadGLLoader((GLADloadproc)glfwGetProcAddress))
	{
		std::cout << "Failed to initialize GLAD" << std::endl;
		return -1;
	}
```
	What this piece of code does is: glfwGetProcAddress is a function that returns the address of the specified OpenGL main function or extension, if supported by the current context. And we pass this address to GLAD which will load the OpenGL function pointers specific to the operating system the code is running on.

Compilation should be correct and code execution should work as well even though nothing can be seen.


## 2.3 Definition of the viewport
In order to be able to render something, we need to tell OpenGL the size of the window where we want to render our polygons and meshes, or we'll do that with the glViewport function.

The first two parameters define the bottom point on the left of the window, the third parameter defines the width and finally the fourth defines the height. In this case the values ​​match the window previously defined with GLFW, although they may have different sizes.
```
glViewport(0, 0, 800, 600);
```
OpenGL uses the data specified via glViewport to transform the 2D coordinates it rendered into on-screen coordinates. For example, a processed location point (-0.5, 0.5) will be mapped (as a final transform) to (200,450) in screen coordinates. Note that coordinates rendered in OpenGL are between -1 and 1, so we effectively map from the range (-1 to 1) to (0, 800) and (0, 600).

## 2.3.1 Resize from window

We will have to add callback functions that will be called when the user changes the size of the window or viewport.
We'll add the function definition and declaration above the main function:
```
void framebuffer_size_callback(GLFWwindow* window, int width, int height);
void framebuffer_size_callback(GLFWwindow* window, int width, int height)
{
	glViewport(0, 0, width, height);
}
```
Now we will register this callback right after the viewport is created.
```
 	// callback de resize
	glfwSetFramebufferSizeCallback(window, framebuffer_size_callback);
```

## 2.4 Definition of the game loop

We almost have it, now we will add the main loop of the game, which will run indefinitely until we tell it otherwise:
```
	// Game Loop
	while (!glfwWindowShouldClose(window))
	{
		glfwSwapBuffers(window);
		glfwPollEvents();
	}
```
With this loop what we achieve is to keep painting images and handling user input until the user explicitly closes the window.

The glgwWindowShouldClose function checks if GLFW has been instructed to close.
The glfwPollEvents function checks for input, such as keyboard input or mouse movement.
The glfwSwapBuffers will swap the color buffer used to render during this rendering iteration and display it as output to the screen.


## 2.5 Close resources

When we exit the game loop because we want to close the window, we will have to close and release all the resources in an appropriate way, so we will add the following code after the main loop:
```
	// Release resources
	glfwTerminate();
```
At this point if we compile and run the code, a black window should appear with the name we indicated earlier.

![1](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/063e09e0-18f7-4a19-90f1-8b6428dea83e)


## 2.6 Handling the Input

We will use the GLFW function glfwGetKey to handle the input, more specifically to know if a key has been pressed or not.

Visit this page to access all the features offered by GLFW:[link](https://www.glfw.org/docs/3.3/group__input.html)

We will create our own function that checks whether a key has been pressed or not, in the event that it has been pressed, we will perform an action as appropriate. We add the declaration and definition of this function outside the main.

```
void processInput(GLFWwindow* window)
{
	if (glfwGetKey(window, GLFW_KEY_ESCAPE) == GLFW_PRESS)
		glfwSetWindowShouldClose(window, true);
}
```
And we will proceed to call this function within the game loop, in this way, we will check in each frame if any key is pressed.

If we compile and run we can check how we can close the window by pressing the ESC key


## 2.7 Reset viewport content

We will locate all the rendering commands inside the game loop, right after handling the input.
The first thing to do before painting a new frame is to clean the window with a color of your choice, otherwise the result of the previous frame could interfere with the new image we are about to calculate.

We will use the glClear function to clear the viewport. We specify the color to clear the screen with glClearColor. Whenever we call glClear and clear the color buffer, the entire color buffer will be filled with the color specified by glClearColor. This will result in a bluish dark green color.

The glClearColor function is a function that sets the state of OpenGL, and glClear is a function that uses the defined state, as it uses the current state to retrieve the clear color.

If we compile and run we should get the following screen
![2](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/b9659107-4747-431f-bcbb-654e77b34943)



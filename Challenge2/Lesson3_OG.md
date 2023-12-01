---
title: "First triangle"
permalink: "/Lesson3_OG/"
layout: page
---

Official Learning Resource: [LearnOpenGL - Hello Triangle](https://learnopengl.com/Getting-started/Hello-Triangle)

In this lesson we will explain step by step how to create our first window using OpenGL.

## 3.1 Graphics pipeline

Simplifying a lot we could say that the work of OpenGL consists of transforming all the 3D coordinates of our meshes and polygons into 2D pixels that fit the screen defined by the user.

The transformation process from 3D coordinates to 2D pixels is managed by the "graphics pipeline" of OpenGL. This can be divided into two main parts:
the first transforms your 3D coordinates into 2D coordinates
and the second part transforms the 2D coordinates into actual color pixels.

The graphics pipeline can be divided into several steps where each step requires the output of the previous step as input.

All these steps are highly specialized and can easily be executed in parallel. Today's graphics cards have thousands of tiny processing cores to quickly process our data within the pipeline. Processing cores run small programs on the GPU for each step of the pipeline. These little programs are called shaders. Some shaders can be programmed by us and replace the existing ones by default.

Below you will find an abstract representation of all the stages of the graphics pipeline. Note that the blue sections represent sections where we can inject our own shaders, notice there are three of them, although we'll only focus on two of them, the vertex shader and the fragment shader.
![1](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/10f35a1e-e882-4ce5-8320-19bc4fbcdaa7)


As you can see, in this example, the input data to the graphics pipeline is a list of 3D points that form a triangle, this input data, which is usually encapsulated in an array is called Vertex Data.

	In the future in this array we will save all those properties of the vertices that we want to use in some shader such as position, color, texture coord, etc.

- The Vertex Shader phase takes as input the vertices defined in our program, and its task is, if necessary, to apply some operation to obtain different 3D coordinates.
- The "primitive assembly" stage takes as input all the vertices of the previous phase and assembles all the points in the given primitive form; in this case a triangle.
- The "geometry shader" takes as input a collection of vertices that form a primitive and has the ability to generate other shapes by emitting new vertices to form new (or other) primitives.
- The output of the “geometry shader” is passed to the rasterization stage, where the resulting primitives are mapped that will eventually be translated into the corresponding pixels of the final screen, giving rise to fragments for the “fragment shader”. Before applying this shader, the "clipping" is performed before the next stage, which discards all the fragments that are outside our view, or viewport..

An OpenGL fragment is all the data needed for OpenGL to render a single pixel.

The main purpose of the "shader fragment" is to calculate the final color of a pixel, usually at this stage all OpenGL effects (lights, shadows, light color, etc.) occur.
- After all the corresponding color values ​​have been determined, the final object will go through one more stage that we call "alpha test" and "blending stage". This stage checks the corresponding depth value (z value) of the fragment and uses it to check whether the resulting fragment is in front of or behind other objects and should be discarded accordingly.

The stage also checks the alpha values ​​(alpha values ​​define the opacity of an object) and blends the objects accordingly. So even if a pixel output color is calculated in the fragment shader, the final pixel color could be something completely different when rendering multiple triangles.

Once the graphics pipeline is explained, let's draw a triangle in our application. We will continue using the solution from the previous lesson, and to start we will define the input vertices.

## 3.2 Input vertices
Remember that OpenGL is a 3D graphics library, so all the coordinates we specify in OpenGL are in 3D (x, y, and z coordinates).
Also remember that OpenGL only processes 3D coordinates when they are in a specific range between -1.0 and 1.0 on all 3 axes (x, y, and z). All coordinates within this so-called normalized device coordinate range will end up visible on the screen (and all coordinates outside this region will not).

Normally the vertices of the modeled objects are not within this range and must be bounded, but to simplify our exercise, we will create an array with three vertices defining a triangle, all of which are within the correct OpenGL display range .

Let's add this array of floats just before the game loop:
```
	float vertices[] = {
		-0.5f, -0.5f, 0.0f,
		 0.5f, -0.5f, 0.0f,
		 0.0f, 0.5f, 0.0f
			};
```
Here we are defining a 2D triangle, since its z-coordinate is initialized to 0.0f. As seen in the image:

![2](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/cd409796-edc8-4bf6-b2f8-11b1c0d72d0e)


With the array of vertices defined, the goal is to send them as input to the first process of the graphics pipeline: the vertex shader. This is done:
reserving a space in the GPU memory where they store the vertex data,
configure how OpenGL should interpret this patch of memory
 and specify how to send the data to the graphics card.

Once we have all that, the vertex shader will process the vertices as we tell it to.



reserving a space in the GPU memory where they store the vertex data,

This memory space in the GU will be accessible by us from the buffer offered by OpenGL: VBO (vertex buffer object). Sending data to the graphics card from the CPU is relatively slow, so whenever we can, we try to send as much data as possible at once. Once the data is in the graphics card's memory, the vertex shader has near-instant access to the vertices, making it extremely fast to access

We will create the VBO object as follows, right after the vertices declaration.
```
	unsigned int VBO;
	glGenBuffers(1, &VBO);
	glBindBuffer(GL_ARRAY_BUFFER, VBO);
	glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
```
What the first line of code does is declare an integer (VBO) that will be initialized with the start address of the buffer when we call the glGenBuffers function. The glGenBuffers function creates a buffer, indicated by the first parameter, and the address of the start of this buffer, second parameter.

In the third line, glBindBuffer, we indicate whether the created buffer, which we access from the VBO variable, its content should be interpreted as vertices (GL_ARRAY_BUFFER) or indexes (GL_ELEMENT_ARRAY_BUFFER).

The fourth line contains the glBufferData function, which creates and initializes the buffer created with the explicit vertices. In our case, to indicate in the 1st parameter that the target isGL_ARRAY_BUFFER what we do is access VBO, the second argument specifies the size of data we want to transfer to the buffer, remember that vertices is the array initialized by us previously, the third parameter indicates the array directly.

The fourth parameter specifies how we want the GPU to process the data:
- GL_STREAM_DRAW: Data is set only once and used by the GPU a few times at most.
- GL_STATIC_DRAW: Data is only set once and used many times.
- GL_DYNAMIC_DRAW: Data is changed a lot and used many times.

In our case, being a static triangle for now, we will use theGL_STATIC_DRAW. 

If we compile and run the code, it should run correctly, although nothing will appear on the screen yet.


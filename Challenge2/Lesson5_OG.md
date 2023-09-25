---
title: "Vertex Attributes"
permalink: "/Lesson5_OG/"
layout: page
---
OpenGL still does not know how to interpret the vertices stored in memory and how to connect the information of these vertices with the attributes of the vertex shader. In this lesson we will explain step by step how to link attribute vertices.
 
The vertex shader allows us to specify any input we want in the form of vertex attributes, and while this allows for a lot of flexibility, it means that we have to manually specify what part of our input data goes to which attribute of vertex to vertex shader.

This means that we need to specify how OpenGL should interpret the vertex data before manipulating it.

Our vertex buffer data has the following format:
![1](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/b7d778d4-2baa-44e7-bae7-007505752467)


- Position data is stored as 32-bit (4-byte) floating-point values.
- Each position is composed of 3 of these values.
- There is no space (or other values) between each set of 3 values. Values ​​are tight
packed in the matrix.
- The first value of the data is at the beginning of the buffer.

With this knowledge we can tell OpenGL how to interpret the vertex data (per vertex attribute) using glVertexAttribPointer, we'll add this code after creating the shaders:
```
	glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
	glEnableVertexAttribArray(0);
```

- The first parameter specifies which vertex attribute we want to set. In this case we indicate the value 0, which corresponds to the position of the vertex. Remember that we specified the location of the position vertex attribute in the vertex shader with (location = 0). This sets the location of the vertex attribute to 0, and since we want to pass data to this vertex attribute, we pass it to 0.
- The next argument specifies the size of the vertex attribute. The vertex attribute is a vec3, so it consists of 3 values.
- The third argument specifies the data type which is GL_FLOAT (a vec* in GLSL consists of floating point values).
- The fourth argument specifies whether we want the data to be normalized. This means that the data must be within the range [0,1]. In this example it's not relevant to us since the data is already in that range, so we'll leave it at GL_FALSE.
- The fifth argument is known as the "stride" and tells us the space between consecutive vertex attributes in memory. Since the next set of position data is exactly 3 times the size of a float, we indicate this value.
- The last parameter is of type void* and thus requires a strange cast. This indicates where the position data starts in the buffer. Since the position data is at the start, this value is just 0.

Now that we've specified how OpenGL should interpret vertex data, we should also enable this technique via code with the glEnableVertexAttribArray function, since vertex attributes are disabled by default. We will pass the location of the vertex attribute as an argument.

From this moment we have everything set up:

We initialized the vertex data in a buffer using a vertex buffer object,
We set up a vertex and fragment shader
and we've told OpenGL how to bind the vertex data to the vertex attributes and the vertex shader.

We will add the following lines to the existing code
```
	...
	
	// Read and load the shaders into memory
	Shader ourShader("res/vertexshader.vs", "res/fragmentshader.fs");

	// Lesson 5 configure the vertex attributes
	glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
	glEnableVertexAttribArray(0);
```

We will have to repeat this process every time we want to draw an object, this process can become a very tedious task, for example in the case of having 100 objects, so there is an object (VAO) "vertex array object" that stores the calls to previously configured vertex attributes, so this has the advantage that when the vertex attribute pointers are set, these settings only need to be done once, and whenever we want to draw the object, we only need to bind the VAO corresponding

To use a VAO all we need to do is link the VAO using the glBindVertexArray function. From this point we should bind/set the corresponding VBOs and attribute pointers, and then unbind the VAO for later use. As soon as we want to draw an object, we simply bind the VAO to the preferred configuration before drawing the object and that's it.

So, first of all, just before creating the VBO, we will create and link the VAO.
```
	unsigned int NEW;
	glGenVertexArrays(1, &NEW);
	glBindVertexArray(NEW);

	// Create and initialize the VBO
	unsigned int VBO;
	glGenBuffers(1, &VBO);
...
```
And with that there we have it, a VAO that stores our vertex attribute settings and which VBO to use.
Typically, all VAOs (and thus the necessary VBOs and attribute pointers) are first generated/configured and stored for later use. The moment we want to draw one of our objects, we take the corresponding VAO, bind it, then draw the object and unbind the VAO again.

Finally, to draw objects, OpenGL provides us with the glDrawArrays function that draws primitives using the active shader, the previously defined vertex attribute settings, and the vertex data from the VBO (linked indirectly via the VAO).

Inside the game loop we will write:
```
	// Game Loop
	while (!glfwWindowShouldClose(window))
	{
		...
		glClear(GL_COLOR_BUFFER_BIT);

		ourShader.use();
		glBindVertexArray(NEW);

		glDrawArrays(GL_TRIANGLES, 0, 3);
		...		
	
		}
```

![2](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/2d6aa4fb-d621-44cf-8a0f-097035cd20c9)



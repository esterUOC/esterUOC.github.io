---
title: "Shader Parameters"
permalink: "/Lesson7_OG/"
layout: page
---

Official Learning Resource: [LearnOpenGL - Shaders](https://learnopengl.com/Getting-started/Shaders)

Shaders are written in the language very similar to C. GLSL is designed for use with graphics and contains useful functions specifically aimed at manipulating vectors and matrices.

A shader typically has the following structure:

![1](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/d193bab2-e175-4099-815d-2e9db37c681a)


When talking specifically about the vertex shader, each input variable is also known as a vertex attribute. There is a hardware-limited maximum number of vertex attributes we can declare. OpenGL guarantees that there are always at least 16 4-component vertex attributes available,

## 7.1 Types of GLSL

GLSL has, like any other programming language, data types to specify what kind of variable we want to work with. GLSL has most of the basic default types we know from languages ​​like C: int, float, double, uint i bool. GLSL also includes two types of containers that we will be using a lot, namelyvectors and matrices.

A vector in GLSL is a container of 1, 2, 3, or 4 components for any of the basic types just mentioned.
The components of a vector can be accessed using vec.x where x is the first component of the vector. You can use .x, .y, .z and .w to access its first, second, third and fourth component respectively. GLSL also allows you to use rgba for colors or stpq for texture coordinates, accessing the same components.

For example:

![2](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/968ac4f2-61df-4940-994a-0a4a57b087be)

## 7.2 In and outs
Each shader can specify input (in) and output (out) parameters using these keywords and whenever an output variable matches an input variable of the next shader stage it is passed to them.

Specifically the vertex shader is special in its input, since it receives data directly from the vertex data or in other words from the CPU. Therefore, the vertex shader requires an additional layout specification for its inputs.

The other exception is that the fragment shader requires a vec4 color output variable, since fragment shaders need to generate a final output color. If you don't specify an output color in your fragment shader, the color buffer output for those fragments will be undefined.

Apart from these two exceptions, if we want to send data from one shader to another, we will have to declare an output in the sending shader and a similar input in the receiving shader. When the types and names are the same on both sides, OpenGL will bind these variables and then it is possible to send data between shaders.

Let's try modifying our beginning triangle with this code:

VertexShader:
```
#version 330 core
layout (location = 0) in vec3 aPos;

out vec4 vertexColor; // specify a color output to the fragment shader

void main()
{
gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);
	vertexColor = vec4(0.5, 0.0, 0.0, 1.0);
}
```
```
Fragment Shader:

#version 330 core
out vec4 FragColor;

in vec4 vertexColor;

void main()
{
FragColor = vertexColor;
}
```

If we compile and run, we will see that we now have a red triangle as output

![3](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/4ace1111-320d-410b-8b16-bca09b9e0ae7)

## 7.3 Uniforms

Uniforms are another way to pass data from our application on the CPU to the shaders on the GPU.
First, uniforms are global. Which means that a uniform variable is unique per shader object and can be accessed from any shader at any stage of the shader program.
Second, whatever the value of the uniform is, they will retain their values ​​until they are reset or upgraded.

To declare a uniform in GLSL, we simply add the uniform keyword to a shader with a type and a name. From this point on we can use the newly declared uniform in the shadow.


For example, let's modify our fragment shader, adding a uniform:
```
#version 330 core
out vec4 FragColor;

uniform vec4 ourColor; // we set this variable in the OpenGL code.

void main()
{
	FragColor = ourColor;
}
```
This uniform is currently empty; We haven't added any data yet. First we need to find the index/location of the uniform attribute in our shader. Once we have the index/location of the uniform, we can update its values.


First we'll add this function to our Shader.h

```
unsigned int getShaderProgramID(){return ID; }
```

We will modify the Game Loop as follows:
```
		glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
		glClear(GL_COLOR_BUFFER_BIT);

		ourShader.use();

		// Llicço 7, Uniforms
		float timeValue = glfwGetTime();
		float greenValue = (sin(timeValue) / 2.0f) + 0.5f;
		int vertexColorLocation = glGetUniformLocation(ourShader.getShaderProgramID(), "ourColor");

		glUniform4f(vertexColorLocation, 0.0f, greenValue, 0.0f, 1.0f);

		// render the triangle
		glBindVertexArray(NEW);
		glDrawArrays(GL_TRIANGLES, 0, 3);
```

First, we retrieve the execution time in seconds using glfwGetTime(). We then vary the color in the range 0.0 to 1.0 using the sin function and store the result in greenValue.

Next, we check if ourColor uniform exists using glGetUniformLocation, supplying the shader and uniform name as parameters. If glGetUniformLocation returns -1, the location could not be found.
Finally, we can set the uniform value using the glUniform4f function.

If we compile and run, we will see a triangle appear that gradually changes color from green to black.


![4](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/bf4fa36d-246e-4323-b2a9-2bfb4c79a37a)![5](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/10e7641b-eb43-4211-ba1e-3fe3887d44d9)


As you can see, uniforms are a useful tool for setting attributes that can change each frame, or for exchanging data between your application and our shaders, but what if we want to set a color for each vertex?

In this case we should declare as many uniform variables as we have vertices. A better solution would be to include more data in the vertex attributes, which is what we will do now.

## 7.4 More attributes

We saw in the previous chapter how we can populate a VBO, set vertex attribute pointers, and store everything in a VAO. This time we additionally want to add color data to the vertex data. We will add color data as 3 floats to the vertex array.

We assign a red, green and blue color to each of the corners of our triangle respectively, our code defines a triangle like this:
```
	float vertices[] = {
		// positions        // colors
		 0.5f, -0.5f, 0.0f, 1.0f, 0.0f, 0.0f,// bottom right
		-0.5f, -0.5f, 0.0f, 0.0f, 1.0f, 0.0f,// bottom left
		 0.0f, 0.5f, 0.0f, 0.0f, 0.0f, 1.0f// top
	};
```
Since we now have more data to send to the vertex shader, we need to adjust it to also receive our color value as a vertex attribute input. Note that we set the location of the aColor attribute to 1 with the layout specifier:

Vertex Shader.vs
```
#version 330 core
layout (location = 0) in vec3 aPos;		// position has attribute position 0
layout (location = 1) in vec3 aColor;	// color has attribute position 1

out vec3 ourColor;						// output a color to the fragment shader

void main()
{
	gl_Position = vec4(aPos, 1.0);
	ourColor = aColor;
}
```
Fragment Shader
```
#version 330 core
out vec4 FragColor;

in vec3 ourColor; // we set this variable in the OpenGL code.

void main()
{
	FragColor = vec4(ourColor, 1.0);
}
```
Now the new VBO will contain the following structure:

![6](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/0fc2d440-35c8-40b1-aa1e-f954656f66b9)


And updating the Game.cpp part of the set up of the attributes:
```
	float vertices[] = {
		// positions // colors
		0.5f, -0.5f, 0.0f, 1.0f, 0.0f, 0.0f,// bottom right
		-0.5f, -0.5f, 0.0f, 0.0f, 1.0f, 0.0f,// bottom left
		0.0f, 0.5f, 0.0f, 0.0f, 0.0f, 1.0f// top
	};

	unsigned int VBO, VAO;
	glGenVertexArrays(1, &NEW);
	glGenBuffers(1, &VBO);
	
	glBindVertexArray(NEW);

	// Create and initialize the VBO
	glBindBuffer(GL_ARRAY_BUFFER, VBO);
	glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);


	// Lesson 5 configure the vertex attributes
	glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 6 * sizeof(float), (void*)0);
	glEnableVertexAttribArray(0);
	// LLiço 7 color attribute
	glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, 6 * sizeof(float), (void*)(3 * sizeof(float)));
	glEnableVertexAttribArray(1);

	ourShader.use();


	// Game Loop
	while (!glfwWindowShouldClose(window))
	{

		// inputs
		processInput(window);

		// rendering Commands
		glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
		glClear(GL_COLOR_BUFFER_BIT);

		glBindVertexArray(NEW);
		ourShader.use();

		// render the triangle
		glDrawArrays(GL_TRIANGLES, 0, 3);

		glfwSwapBuffers(window);
glfwPollEvents();
}
```
If we compile and run, we should see a triangle like this:

![7](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/4222f01d-fb89-4878-821d-a14f2ef64ae8)




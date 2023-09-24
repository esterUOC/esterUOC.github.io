  ---
title: "First Shaders"
permalink: "/Lesson4_OG/"
layout: page
---
In this lesson we will explain step by step how to create our first shaders.

Modern OpenGL requires us to at least set up a vertex and fragment shader if we want to do some rendering, so we'll create a new class and create our first shaders. This way we can finally visualize our triangle.

Next we will create a new file and call it vertexshader.vs and fragmentshader.fs, we invent both the name and the extensions of the files, this way they will be more intuitive.


## 4.1 Shade vertex creation
The vertex shader is one of our programmable shaders. The language used is GLSL (OpenGL Shading Language).

Below is the source code for a very basic vertex shader in GLSL, copy this code into a new file in your project and name it vertexshader.vs.
```
#version 330 core
layout (location = 0) in vec3 aPos;

void main()
{
	gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);
}
```
The shader starts by declaring the version of OpenGL and that we are using the core functionality.
Next we declare all the input attributes (where we use the keyword in), which in our case will be the position.
We also specifically set the location of the input variable using layout (location = 0), we'll see later what we need it for.

To set the vertex shader output we need to assign the position data to the predefined gl_Position variable, which is a vec4 behind the scenes. At the end of the main function, whatever we set gl_Position to will be used as the output of the vertex shader.


## 4.2 Creating the Shader fragment

The fragment shader is the second shader we need to create to render a triangle. The shader fragment consists of calculating the color output of our pixels.

To simplify things, in this example we will make the color of the triangle always orange.

We'll create a new file called fragmentshader.fs and copy this content:
```
#version 330 core
out vec4 FragColor;

void main()
{
		FragColor = vec4(1.0f, 0.5f, 0.2f, 1.0f);
}
```
The fragment shader requires only one output variable and is a vector of size 4 that defines the final color output. We can declare output values ​​with the out keyword, which we have called FragColor here.

 Then we simply assign a vec4 to the color output as an orange color with an alpha value of 1.0 (1.0 is completely opaque).


## 4.3 Class Shader creation

Next we will create a shader class that will read some files where we will create our shaders.

First of all we will create a .h file called Shader.h with this content:
```
#pragma once

#ifndef SHADER_H
#define SHADER_H
#include <happy/happy.h> // include glad to get the required OpenGL headers
#include <string>
#include <fstream>
#include <sstream>
#include <iostream>
class Shader
{
public:
	// the program ID
	unsigned int ID;
	// constructor reads and builds the shader
	Shader(const char* vertexPath, const char* fragmentPath);
	// use/activate the shader
	void use();
	// utility uniform functions
	void setBool(const std::string& name, bool value) const;
	void setInt(const std::string& name, int value) const;
	void setFloat(const std::string& name, float value) const;
};
#endif
```

We'll start by looking at the constructor code:
```
Shader::Shader(const char* vertexPath, const char* fragmentPath)
{
	// 1. retrieve the vertex/fragment source code from filePath
	std::string vertexCode;
	std::string fragmentCode;
	std::ifstream vShaderFile;
	std::ifstream fShaderFile;
	// ensure ifstream objects can throw exceptions:
	vShaderFile.exceptions(std::ifstream::failbit | std::ifstream::badbit);
	fShaderFile.exceptions(std::ifstream::failbit | std::ifstream::badbit);
	try
	{
		// open files
		vShaderFile.open(vertexPath);
		fShaderFile.open(fragmentPath);
		std::stringstream vShaderStream, fShaderStream;
		// read file’s buffer contents into streams
		vShaderStream << vShaderFile.rdbuf();
		fShaderStream << fShaderFile.rdbuf();
		// close file handlers
		vShaderFile.close();
		fShaderFile.close();
		// convert stream into string
		vertexCode = vShaderStream.str();
		fragmentCode = fShaderStream.str();
	}
	catch (std::ifstream::failure It is)
	{
		std::cout << "ERROR::SHADER::FILE_NOT_SUCCESFULLY_READ" << std::endl;
	}
	const char* vShaderCode = vertexCode.c_str();
	const char* fShaderCode = fragmentCode.c_str();

	// 2. compile shaders
	unsigned int vertex, fragment;
	int success;
	char infoLog[512];
	
	// vertex Shader
	vertex = glCreateShader(GL_VERTEX_SHADER);
	glShaderSource(vertex, 1, &vShaderCode, NULL);
	glCompileShader(vertex);
	// print compile errors if any
	glGetShaderiv(vertex, GL_COMPILE_STATUS, &success);
	if (!success)
	{
		glGetShaderInfoLog(vertex, 512, NULL, infoLog);
		std::cout << "ERROR::SHADER::VERTEX::COMPILATION_FAILED\n" <<
			infoLog<< std::endl;
	};

	// fragment Shader
	fragment = glCreateShader(GL_FRAGMENT_SHADER);
	glShaderSource(fragment, 1, &fShaderCode, NULL);
	glCompileShader(fragment);
	// print compile errors if any
	glGetShaderiv(fragment, GL_COMPILE_STATUS, &success);
	if (!success)
	{
		glGetShaderInfoLog(fragment, 512, NULL, infoLog);
		std::cout << "ERROR::SHADER::FRAGMENT::COMPILATION_FAILED\n" <<
			infoLog<< std::endl;
	};

	// 3. shader Program
	ID = glCreateProgram();
	glAttachShader(ID, vertex);
	glAttachShader(ID, fragment);
	glLinkProgram(ID);
	// print linking errors if any
	glGetProgramiv(ID, GL_LINK_STATUS, &success);
	if (!success)
	{
		glGetProgramInfoLog(ID, 512, NULL, infoLog);
		std::cout << "ERROR::SHADER::PROGRAM::LINKING_FAILED\n" <<
			infoLog<< std::endl;
	}
	// 4. delete shaders; they’re linked into our program and no longer necessary
	glDeleteShader(vertex);
	glDeleteShader(fragment);

}
```
The above code can be summarized in 4 important steps:
1. Read the source code of the filePath vertex/fragment, here the goal is to read the two files, both the vertex and the fragment, and save their contents in two string variables.
2. Compile shaders: In order for OpenGL to use the shader, it must dynamically compile it at runtime from its source code. That is why for both the vertex shader and the fragment shader we will create a shader object, referenced by an ID using the glCreateShader function.
Next, we attach the shader source code to the shader object and compile the shader with the glCompileShader(…) function.
Finally, if no errors were detected during the compilation of the shaders, they have been compiled and we can send them both to the shader program that we will use to render.
3. A shader program object is responsible for using the previously compiled shaders and using them to render in the corresponding phases of the graphic pipeline.
The glCreateProgram function creates a program and returns the ID reference to the newly created program object. Next we need to attach the precompiled shaders to the program object and then link them with glLinkProgram:
The result is an object that we can activate by calling glUseProgram with the newly created program object as an argument.
We will do this with the use() function
4. Finally, delete the shader objects once we've linked the program object to them, we don't need them anymore.

To finish defining the Shader class we will add the last definitions of the missing functions:
```
void Shader::use()
{
	glUseProgram(ID);
}

void Shader::setBool(const std::string& name, bool value) const
{
	glUniform1i(glGetUniformLocation(ID, name.c_str()), (int)value);
}
void Shader::setInt(const std::string& name, int value) const
{
	glUniform1i(glGetUniformLocation(ID, name.c_str()),value);
}
void Shader::setFloat(const std::string& name, float value) const
{
	glUniform1f(glGetUniformLocation(ID, name.c_str()),value);
}
```
Finally, in the Game.cpp we will add the use of these defined functions

```	
...
// Create and initialize the VBO
	unsigned int VBO;
	glGenBuffers(1, &VBO);
	glBindBuffer(GL_ARRAY_BUFFER, VBO);
	glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);


	Shader ourShader("res/vertexshader.vs", "res/fragmentshader.fs");


	// Game Loop
	while (!glfwWindowShouldClose(window))
	{
...
		glClear(GL_COLOR_BUFFER_BIT);

		ourShader.use();

...
	}

	// Release resources
	glfwTerminate();
	return 0;
}
```
We have now sent the input vertex data to the GPU and told the GPU how it should process the vertex data within a vertex and fragment shader. We're almost there, but not yet. If we compile and run we are still not able to see the triangle, we are missing one last step

OpenGL still doesn't know how to interpret vertex data in memory and how to connect vertex data to vertex shader attributes.





---
title: "GLM, Matrices and Vectors"
permalink: "/Lesson9_OG/"
layout: page
---
So far all our objects are static. For morels we will do it by transforming them using (multiple) matrices.

Consult topic 8 of the pdf to learn about vectors, matrices and the mathematical operations we can do, as well as the vector product and the dot product.

OpenGL does not have any definition of matrices or vectors and their operations, fortunately there is a library called GLM that will allow us to operate with these structures.


## 9.1 GLM

GLM stands for OpenGL Mathematics and is a library with only headers (.h) files, which means we just need to include the header files in our project and we're done; no need to link or compile.

We will go to the page:[link to GLM page](https://glm.g-truc.net/0.9.8/index.html) and we will click on the Download link in the left menu, and we will download its latest download

![1](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/1ab144cb-c767-45fa-895f-96fb0a5e2a5f)

We will extract the file and copy the contents of the glm folder into our project, I recommend saving this folder in the same place where we keep the GLAD and GLFW libraries

![2](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/74914e56-8656-4372-85d2-675d91c4d055)


We will now include this directory in the Include section of our project:

![3](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/4e2e520d-d4c9-430e-8fe2-c45bc19a9aa2)


If we add these includes to our Game.cpp we should be able to compile without problems....

We are going to do interesting things like scale and rotate our triangle, inside the game loop we are going to add these codes:
```
  glBindVertexArray(NEW);

	glm::mat4 trans = glm::mat4(1.0f);
	trans = glm::rotate(trans, glm::radians(90.0f), glm::thing 3(0.0, 0.0, 1.0));
  trans = glm::scale(trans, glm::thing 3(0.5, 0.5, 0.5));
```

We also pass the transformation matrix to the shader:
```
	unsigned int transformLoc = glGetUniformLocation(ourShader.ID, "transform");
	glUniformMatrix4fv(transformLoc, 1, GL_FALSE, glm::value_ptr(trans));

	ourShader.use();
```
Because we pass the matrix to each of the GLM functions, GLM automatically multiplies the matrices together, resulting in a transformation matrix that combines all transformations.

Next we will modify the shader code to accept arrays.
```
#version 330 core
layout (location = 0) in vec3 aPos;		// position has attribute position 0
layout (location = 1) in vec3 aColor;	// color has attribute position 1
layout (location = 2) in vec2 aTexCoord;

out vec3 ourColor;						// output a color to the fragment shader
out vec2 TexCoord;

uniform mat4 transform;

void main()
{
	gl_Position = transform * vec4(aPos, 1.0f);
	ourColor = aColor;
	TexCoord = aTexCoord;
}
```

If we compile AND run, we should get this result:

![4](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/3df76b5a-3a0d-4623-9823-372379122026)

If we add these matrix transformations, instead of the previous ones, we get a displaced triangle that rotates
```
trans = glm::translate(trans, glm::thing 3(0.5f, -0.5f, 0.0f));
trans = glm::rotate(trans, (float)glfwGetTime(), glm::thing 3(0.0f, 0.0f, 1.0f));
```
If we compile and run we will see how the triangle is shifted and moved.


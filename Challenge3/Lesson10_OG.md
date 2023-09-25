---
title: "Coordinate Systems"
permalink: "/Lesson10_OG/"
layout: page
---

As we discussed in previous lessons, OpenGL expects all vertices, which we want to be visible, to be in normalized device coordinates after each execution of the vertex shader.
This means that all the x, y and z coordinates of each vertex should be between -1.0 and 1.0; coordinates outside this range will not be visible.
Normally the coordinates of the 3D meshes of the world, the characters, are usually in non-normalized coordinates (-1.0 and 1.0), and what is usually done is, in the vertex shader we will transform them so that they are within this range.

These normalized coordinates (NDCs) are then fed to the rasterizer to transform them into 2D coordinates/pixels on the screen.

The transformation of coordinates to NDC is generally achieved by transforming the vertices of an object through various coordinate systems before finally transforming them to NDC.
The advantage of transforming to various intermediate coordinate systems is that some operations/calculations are easier in certain coordinate systems, as will soon become apparent. There are a total of 5 different coordinate systems that are important to us:
  * Local space (or Object space)
  * World space
  * View space (or Eye space)
  * Clip space
  * Screen space

These are all different states that our vertices will transform into before finally ending up as fragments or pixels.

The vertices pass from one state to another by multiplying them by different matrices:

  * Model Matrix
  * View Matrix
  * Projection Matrix

Below we show an image that shows the transformation process that the vertices undergo. For more information read the theory of topic 9 of the book.

![1](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/ffe83abb-8936-4e75-afb4-91a68a2bac3f)

## 10.1 Exercise

From now on we will work with a 3D cube to better visualize the perspective projection.

To begin, let's create a cube:
```
	//LESSON 10 cub
	float vertices[] = {
	-0.5f, -0.5f, -0.5f, 0.0f, 0.0f,
	0.5f, -0.5f, -0.5f, 1.0f, 0.0f,
	0.5f, 0.5f, -0.5f, 1.0f, 1.0f,
	0.5f, 0.5f, -0.5f, 1.0f, 1.0f,
	-0.5f, 0.5f, -0.5f, 0.0f, 1.0f,
	-0.5f, -0.5f, -0.5f, 0.0f, 0.0f,
	-0.5f, -0.5f, 0.5f, 0.0f, 0.0f,
	0.5f, -0.5f, 0.5f, 1.0f, 0.0f,
	0.5f, 0.5f, 0.5f, 1.0f, 1.0f,
	0.5f, 0.5f, 0.5f, 1.0f, 1.0f,
	-0.5f, 0.5f, 0.5f, 0.0f, 1.0f,
	-0.5f, -0.5f, 0.5f, 0.0f, 0.0f,
	-0.5f, 0.5f, 0.5f, 1.0f, 0.0f,
	-0.5f, 0.5f, -0.5f, 1.0f, 1.0f,
	-0.5f, -0.5f, -0.5f, 0.0f, 1.0f,
	-0.5f, -0.5f, -0.5f, 0.0f, 1.0f,
	-0.5f, -0.5f, 0.5f, 0.0f, 0.0f,
	-0.5f, 0.5f, 0.5f, 1.0f, 0.0f,
	0.5f, 0.5f, 0.5f, 1.0f, 0.0f,
	0.5f, 0.5f, -0.5f, 1.0f, 1.0f,
	0.5f, -0.5f, -0.5f, 0.0f, 1.0f,
	0.5f, -0.5f, -0.5f, 0.0f, 1.0f,
	0.5f, -0.5f, 0.5f, 0.0f, 0.0f,
	0.5f, 0.5f, 0.5f, 1.0f, 0.0f,
	-0.5f, -0.5f, -0.5f, 0.0f, 1.0f,
	0.5f, -0.5f, -0.5f, 1.0f, 1.0f,
	0.5f, -0.5f, 0.5f, 1.0f, 0.0f,
	0.5f, -0.5f, 0.5f, 1.0f, 0.0f,
	-0.5f, -0.5f, 0.5f, 0.0f, 0.0f,
	-0.5f, -0.5f, -0.5f, 0.0f, 1.0f,
	-0.5f, 0.5f, -0.5f, 0.0f, 1.0f,
	0.5f, 0.5f, -0.5f, 1.0f, 1.0f,
	0.5f, 0.5f, 0.5f, 1.0f, 0.0f,
	0.5f, 0.5f, 0.5f, 1.0f, 0.0f,
	-0.5f, 0.5f, 0.5f, 0.0f, 0.0f,
	-0.5f, 0.5f, -0.5f, 0.0f, 1.0f
		};
```
Now what we will do is create the different matrices, we will start with the first one, the model matrix, the matrix model consists of translations, scale and/or rotations that we would like to apply to transform all the vertices of the object in the global space of world

In our example we will rotate it a little on the x-axis:
```
	glm::mat4 model = glm::mat4(1.0f);
	model = glm::rotate(model, glm::radians(-55.0f), glm::thing 3(1.0f, 0.0f, 0.0f));
```
By multiplying the vertex coordinates with this model matrix, we are transforming the vertex coordinates into world coordinates.

Next, we need to create a view matrix:
```
	glm::mat4 view = glm::mat4(1.0f);
	// note that we’re translating the scene in the reverse direction
	view = glm::translate(view, glm::thing 3(0.0f, 0.0f, -3.0f));
```
.To finish we will define the projection matrix:
```
	glm::mat4 projection;
	projection = glm::perspective(glm::radians(45.0f), 800.0f / 600.0f, 0.1f, 100.0f);
```
Now we need to send these arrays to our vertex shader:
```
		glm::mat4 model = glm::mat4(1.0f);
		model = glm::rotate(model, glm::radians(-55.0f), glm::thing 3(1.0f, 0.0f, 0.0f));

		glm::mat4 view = glm::mat4(1.0f);
		// note that we’re translating the scene in the reverse direction
		view = glm::translate(view, glm::thing 3(0.0f, 0.0f, -3.0f));

		glm::mat4 projection;
		projection = glm::perspective(glm::radians(45.0f), 800.0f / 600.0f, 0.1f, 100.0f);

		int modelLoc = glGetUniformLocation(ourShader.ID, "model");
		glUniformMatrix4fv(modelLoc, 1, GL_FALSE, glm::value_ptr(model));

		int viewlLoc = glGetUniformLocation(ourShader.ID, "view");
		glUniformMatrix4fv(viewlLoc, 1, GL_FALSE, glm::value_ptr(view));

		int projectionLoc = glGetUniformLocation(ourShader.ID, "projection");
		glUniformMatrix4fv(projectionLoc, 1, GL_FALSE, glm::value_ptr(projection));
```

Now that we've created the transformation arrays, we should pass them to our shaders. First, we declare the transformation matrices as uniform in the vertex shader and multiply them by the vertex coordinates:
```
#version 330 core
layout (location = 0) in vec3 aPos;		// position has attribute position 0
layout (location = 1) in vec2 aTexCoord;

out vec2 TexCoord;

uniform mat4 model;
uniform mat4 view;
uniform mat4 projection;

void main()
{
		gl_Position = projection * view * model * vec4(aPos, 1.0f);
		exCoord = vec2(aTexCoord.x, aTexCoord.y);;
}
```
Update the command that paints the cube:
```
glDrawArrays(GL_TRIANGLES, 0, 36);
```
If we compile and run the code, we will get a cube like this:
![2](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/a565c9f4-7fe1-45bb-9031-ff23a9e3b4f6)

As you can see, some sides of the cube are being drawn on top of other sides of the cube, this happens because OpenGL makes no guarantees about the order in which the triangles are painted.

To solve this we will use the Z-buffer to configure OpenGL to do depth tests so that it will not overpaint any side that may be above in the final scene.

## 10.2 Z-Buffer

To learn about the theory, read section 9.8.1 of the book
To activate the Z-buffer, which is disabled by default, we will have to activate it (just after initializing the viewport):
```
	glEnable(GL_DEPTH_TEST);
```
Since we are using a Z-buffer, we also need to clear the depth buffer before each rendering iteration, so inside the main loop we need to update the function glClear(...)
```
glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
```
If we compile and run we should be able to see how the cube is now rendered correctly:

![3](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/c10d630a-986e-4dc9-93f2-a9e26a9deb08)



---
title: "Element Buffer Objects"
permalink: "/Lesson6_OG/"
layout: page
---

Official Learning Resource: [LearnOpenGL - Hello Triangle](https://learnopengl.com/Getting-started/Hello-Triangle)

In this chapter we will explain the use of the element buffer object (EBO). An EBO is a buffer, which holds the indices that OpenGL uses to decide which vertices to draw.

Let's say for example that we want to draw any mesh (a rock for example) which is made up of thousands of vertices, instead of keeping the list of vertices that form the triangles of the mesh, consequently duplicating many of them and using a lot of space in memory, what we want is to have all the vertices stored in a list and use the indices of these to form the triangles.

For example we are going to create a rectangle formed by two triangles, the vector of vertices and indices will be:
```
	float vertices[] = {
	0.5f, 0.5f, 0.0f,// top right
	0.5f, -0.5f, 0.0f,// bottom right
	-0.5f, -0.5f, 0.0f,// bottom left
	-0.5f, 0.5f, 0.0f// top left
	};
	unsigned int indices[] = { // note that we start from 0!
	0, 1, 3, // first triangle
	1, 2, 3 // second triangle
	};
```
Next we create the EBO object:
```
	unsigned int EBO;
	glGenBuffers(1, &EBO);
```
Then we link the EBO and copy the indexes to the buffer with glBufferData. Also, just like with the VBO, we'll link this object, although this time we specify GL_ELEMENT_ARRAY_BUFFER to specify the buffer type.
```
	glBindBuffer(GL_ARRAY_BUFFER, VBO);
	glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

	glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO);
	glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(lists), lists,GL_STATIC_DRAW);

	// Read and load the shaders into memory
	Shader ourShader("res/vertexshader.vs", "res/fragmentshader.fs");
```

Finally in the game loop, the last thing left to do is replace the glDrawArrays call with glDrawElements to indicate that we want to render the triangles from an index buffer. When we use glDrawElements we will draw using indices provided in the element buffer object.

Finally, to indicate whether we want to paint in fill or wireframe mode, we use the function glPolygonMode, where the first parameter will always beGL_FRONT_AND_BACK, but the second parameter can beGL_FILL o GL_LINE.
```
	while (!glfwWindowShouldClose(window))
	{
		// inputs
		processInput(window);

		// rendering Commands
		glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
		glClear(GL_COLOR_BUFFER_BIT);

		ourShader.use();
		//glDrawArrays(GL_TRIANGLES, 0, 3);

		glBindVertexArray(NEW);
		glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO);
		glPolygonMode(GL_FRONT_AND_BACK, GL_LINE);
		glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0);

		glfwSwapBuffers(window);
		glfwPollEvents();
	}
```
If we compile and run the code, we should be able to see a rectangle. Below is an example using the Fill mode, and the Line model.

![1](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/d3d854ec-958a-4597-9af6-6aa4a9fd0ffc)




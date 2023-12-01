---
title: "Cameras"
permalink: "/Lesson11_OG/"
layout: page
---

Official Learning Resource: [LearnOpenGL - Camera](https://learnopengl.com/Getting-started/Camera)

In this lesson we will learn how to initialize a camera in OpenGL. We'll create a freestyle camera that allows us to freely move around a 3D scene based on keyboard and mouse input.

The goal is to simulate seeing all the vertices of the world from the perspective of a camera in a certain position. To define the camera we need its position in world space, the direction it is facing, a vector pointing to the right, and a vector pointing up from the camera.

![1](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/907a1db7-e22c-4aa0-a313-c7aff41e1f9b)

Simplifying a lot, what we have to do is calculate a matrix, called "LookAt", which defines a coordinate space right at the point where we want to place our camera.
Using this LookAt array effectively transforms all world coordinates into camera space. The LookAt array then does exactly what it says: it creates an array that looks at a given target.

GLM provides us with a function that does all this work for us, glm::LookAt(...). We just need to specify a camera position, the target position, and a vector representing the up vector in world space. GLM then creates the LookAt array that we can use as the view array.
```
	glm::mat4 view;
	view = glm::lookAt(glm::thing 3(0.0f, 0.0f, 3.0f),			
  glm::thing 3(0.0f, 0.0f, 0.0f), glm::thing 3(0.0f, 1.0f, 0.0f));
```

If we replace the view matrix definition that we have previously with this new definition, and in addition we add that the camera position changes over time:
```
		glm::mat4 view = glm::mat4(1.0f);// make sure to initialize matrix to identity matrix first
		float radius = 10.0f;
		float camX = static_cast<float>(sin(glfwGetTime()) * radius);
		float camZ =static_cast<float>(cos(glfwGetTime()) * radius);
		view = glm::lookAt(glm::thing 3(camX, 0.0f, camZ), glm::thing 3(0.0f, 0.0f, 0.0f), glm::thing 3(0.0f, 1.0f, 0.0f));
```
If we compile and run, we can see how the cube is rotating all the time, what is really happening is that we are moving the camera around the cube.


## 10.1 Exercise

Follow the code to make our own camera.

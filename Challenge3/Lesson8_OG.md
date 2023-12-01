---
title: "Textures"
permalink: "/Lesson8_OG/"
layout: page
---

Official Learning Resource: [LearnOpenGL - Textures](https://learnopengl.com/Getting-started/Textures)

In this lesson we will learn to how to use textures. As you know, a texture is a 2D image (1D and 3D textures even exist) that is used to add detail and realism to an object.

In our example we would like map the texture wall.jpeg above the triangle of our application.

In order to map a texture to the triangle we must tell each vertex of this triangle which part of the texture corresponds to being mapped. Therefore, each vertex should have an associated texture coordinate, which specifies what part of the texture image should be displayed. Fragment interpolation does the rest for the other fragments of our triangle.

	Texture coordinates range from 0 to 1 in the x and y axes. Obtaining the color of the texture using the texture coordinates is called sampling. Texture coordinates start at (0,0) for the bottom left corner of a texture image to (1,1) for the top right corner of a texture image.

The image below shows how we map the texture coordinates to the triangle:

![1](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/de31dcd2-b1a3-461d-9a9d-700d11f13c0c)

We specify 3 texture coordinate points for the triangle:
- we want the bottom left side of the triangle to correspond to the bottom left side of the texture, so we use the texture coordinate (0,0) for the bottom left vertex of the triangle.
- in the lower right with a texture coordinate (1,0).
- The top of the triangle should correspond to the top center of the texture image, so we take (0.5,1.0) as the texture coordinate.
We only need to pass 3 texture coordinates to the vertex shader, which then passes them to the fragment shader which perfectly interpolates all the texture coordinates of each fragment.

Our job is to tell OpenGL how to sample the textures

## 8.1 Texture Wrapping

Texture coordinates usually range from (0,0) to (1,1), outside of this range the default behavior of OpenGL is to repeat texture images (GL_REPEAT) but there are more options that OpenGL offers (see chapter 8 of the book):

![2](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/02e6db91-83f6-460f-ad83-5991573309ae)

We will use the function glTexParameteri, to define the behavior of both the s and t coordinates (equivalent to x and y).

![3](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/4e87232c-1b0f-4420-8737-4f8bfe2b5308)

The first argument specifies what type of texture it is, in our case GL_TEXTURE_2D. The second argument forces us to say which option we want to set and for which texture axis. The last argument forces us to pass the texture sample we would like: GL_MIRRORED_REPEAT.

## 8.2 Texture Filtering

Texture coordinates are resolution-independent, but can have any floating-point value, so OpenGL must figure out which texture pixel (or texel) the texture coordinate should be mapped to. For example if we have a very large object and a low resolution texture.

OpenGL has several options for this texture filtering. But for now, we will talk about the most important options: GL_NEAREST and GL_LINEAR.

- GL_NEAREST is OpenGL's default texture filtering method. Here, OpenGL selects the texel whose center is closest to the texture coordinate.
- GL_LINEAR takes an interpolated value from neighboring texels of the texture coordinate, approximating a color between the texels.

 ![4](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/14eb0fcc-7bc7-461d-a9bb-57ea0491ee14)![5](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/8c666a4c-e9a1-46d9-aee2-d7dc618fc2bb)


The resulting effect is as follows:

![6](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/0851a5b1-0a85-4212-a4f4-5bc6fb8dbde2)

 We will also use the function glTexParameteri, to define the behavior:
![7](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/c91a0424-81a6-4925-8656-845cef4433f0)


## 8.3 Mipmaps

The concept called mipmaps is basically a collection of texture images where each subsequent texture is twice as small as the previous one. OpenGL will use a different mipmap texture that better suits the distance between the observer and the object. The further away the object is, the smaller mipmaps will be used. In this way, OpenGL is able to display the correct texels, avoid visual artifacts and lower memory usage.

![8](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/79d05dc2-dd7a-4c2d-8637-0255936e4a2b)


OpenGL has a function that automatically creates them for us, glGenerateMipmaps.
When switching between levels of mipmaps, OpenGL can displaysome visual artifacts such as sharp edges visible between the two mipmap layers. Just as textures have some filtering, it is also possible to filter between mipmap levels using NEAREST and LINEAR filtering. We will do this using the glTexParameteri function

![9](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/816cbdb6-8f2d-4d48-91db-03bb4aa73e2a)


## 8.4 Reading and loading textures in our code
We'll use an image upload library that supports several popular formats and does all the hard work for us. The library (which is basically a file) stb_image.h,

Simply download the single header file (https://github.com/nothings/stb/blob/master/stb_image.h), add it to your project as stb_image.h, and create an additional C++ file with the following code in our Game.cpp:
```
#define STB_IMAGE_IMPLEMENTATION
#include "stb_image.h"
```
If we compile we should have 0 errors. Now let's read and load our texture into memory, usually this code goes right before the VBO and VAO definitions:

As always we will start by creating an object:
```
	unsigned int texture;
	glGenTextures(1, &texture);
```
The function glGenTextures takes as the first parameter how many textures we want to generate and stores them in an unsigned int passed as the second argument.

Like other objects, we need to bind it so that any subsequent texture commands will set the currently bound texture:
```
glBindTexture(GL_TEXTURE_2D, texture);
```
We will also initialize the wrapping and filtering options
```
	glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_REPEAT);
	glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_REPEAT);
	glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
	glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
```
We will read the texture:
```
 int width, height, nrChannels;
 unsigned char* data = stbi_load("textures/wall.jpg", &width, &height, &nrChannels, 0);
```

Now that the texture is linked, we can start generating a texture using the image data loaded earlier. The textures are generated with glTexImage2D: refer to chapter 7 of the book to consult the parameters of this function
```
	if (data)
	{
		glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB, width, height, 0, GL_RGB, GL_UNSIGNED_BYTE, data);
		glGenerateMipmap(GL_TEXTURE_2D);
	}
	else
	{
		std::cout << "Failed to load texture" << std::endl;
	}
```

Once glTexImage2D is called, the linked texture object has the texture image attached to it. We will call glGenerateMipmap after generating the texture. This will automatically generate all necessary mipmaps for the currently bound texture.

Once we've finished generating the texture and its corresponding mipmaps, it's a good practice to release the image memory:
stbi_image_free(data);

If you compile and run, if you put a breakpoint in the data variable, we can see if it has read data or not.

Next we're going to add the texture coordinates to our data array
```
	float vertices[] = {
		// positions        // colors         // tex coords
		 0.5f, -0.5f, 0.0f, 1.0f, 0.0f, 0.0f, 0.0f, 0.0f,// bottom right
		-0.5f, -0.5f, 0.0f, 0.0f, 1.0f, 0.0f, 1.0f, 0.0f,// bottom left
		 0.0f, 0.5f, 0.0f, 0.0f, 0.0f, 1.0f, 0.5f, 1.0f,// top
	};
```

Since we've added an extra vertex attribute, we need to notify OpenGL again of the new vertex format:
![10](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/b843b7fb-6eb0-40bb-9cbd-075ffbfb794b)

The vertex attributes will look like this:
```
	// Lesson 5 configure the vertex attributes
	glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 8 * sizeof(float), (void*)0);
	glEnableVertexAttribArray(0);
	// LLiço 7 color attribute
	glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, 8 * sizeof(float), (void*)(3 * sizeof(float)));
	glEnableVertexAttribArray(1);
	// LLiço 8 color attribute
	glVertexAttribPointer(2, 2, GL_FLOAT, GL_FALSE, 8 * sizeof(float), (void*)(6 * sizeof(float)));
	glEnableVertexAttribArray(2);
```

Now we'll modify our vertex shader to read this new variable:
```
#version 330 core
layout (location = 0) in vec3 aPos;		// position has attribute position 0
layout (location = 1) in vec3 aColor;	// color has attribute position 1
layout (location = 2) in vec2 aTexCoord;

out vec3 ourColor;						// output a color to the fragment shader
out vec2 TexCoord;

void main()
{
	gl_Position = vec4(aPos, 1.0);
	ourColor = aColor;
	TexCoord = aTexCoord;
}
```

And the fragment shader will be:
```
#version 330 core
out vec4 FragColor;

in vec3 ourColor; // we set this variable in the OpenGL code.
in vec2 TexCoord;
uniform sampler2D ourTexture;

void main()
{
	FragColor = texture(ourTexture, TexCoord);
}
```

Now we only need to link the texture before calling the function that paints the glDrawElements triangle, and we will have the following image:

![11](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/88b43d95-ecf7-4712-8468-7d19928848ab)

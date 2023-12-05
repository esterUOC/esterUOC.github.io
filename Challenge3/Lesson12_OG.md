---
title: "Text Rendering"
permalink: "/Lesson12_OG/"
layout: page
---

Official Learning Resource: [LearnOpenGL - Text Rendering](https://learnopengl.com/In-Practice/Text-Rendering)

In this lesson we will learn how to render text in OpenGL, but we must point out that there is no support for rendering text within OpenGL, then it will be up to us to define a system to represent the text on the screen.
Some examples of techniques are: drawing letter shapes using GL_LINES, creating 3D meshes of letters or rendering character textures to 2D quads in a 3D environment, there are also those who render characters to quads.

In this lesson we will learn to render text using a technique used by the FreeType library.

Anyone who wants to learn or read other existing techniques access points 48.1 (bitmap fonts)



## 12.1 FreeType

FreeType is a software development library that is capable of loading fonts, rendering them to bitmaps, and providing support for various font-related operations.
What makes this library particularly attractive is that it is able to load TrueType fonts.

A TrueType font is a collection of character glyphs not defined by pixels or any other non-scalable solution, but by mathematical equations, similar to vector images. Using this font, we can easily render character glyphs of different sizes without any quality loss.

We can download the .lib file from the page:[FreeType link](https://freetype.org/)

Installing the library (watch this [tutorial](https://www.youtube.com/watch?v=qW_8Dyq2asc) to get an idea of ​​what we'll do next):


Once we have configured the library in our project, we are going to include the headers:
```
#include <ft2build.h>
#include FT_FREETYPE_H
```
Now we will add the Truetype font arial.ttf which can be found on our pc in windows/fonts, we will add to our solution a folder called fonts, we will copy this arial.ttf and copy this code:
```
	FT_Library ft;
	if (FT_Init_FreeType(&ft))
		std::cout << "ERROR::FREETYPE: Could not init FreeType Library" << std::endl;

	FT_Face face;
	if (FT_New_Face(ft, "fonts/arial.ttf", 0, &face))
		std::cout << "ERROR::FREETYPE: Failed to load font" << std::endl;
```

To define the size of the font in pixels we will use the function FT_Set_Pixel_Sizes(…), which sets the width and height parameters of the font. Setting the width to 0 allows the face to dynamically calculate the width based on the given height.
```
FT_Set_Pixel_Sizes(face, 0, 48);
```
Next we will generate all the textures of the characters and save them in a map:
```
	struct Character {
		unsigned int TextureID; // ID handle of the glyph texture
		glm::ivec2 Size; // Size of glyph
		glm::ivec2 Bearing; // Offset from baseline to left/top of glyph
		unsigned int Advance; // Offset to advance to next glyph
	};
	std::map<char, Character> Characters;
```

Next, we will create a texture for the 128 characters (ASCII) and save them in a map:
```
	glPixelStorei(GL_UNPACK_ALIGNMENT, 1); // no byte-alignment restriction
	for (unsigned char c = 0; c < 128; c++)
	{
		// load character glyph
		if (FT_Load_Char(face, c, FT_LOAD_RENDER))
		{
			std::cout << "ERROR::FREETYTPE: Failed to load Glyph" << std::endl;
			continue;
		}
		// generate texture
		unsigned int texture;
		glGenTextures(1, &texture);
		glBindTexture(GL_TEXTURE_2D, texture);
		glTexImage2D(
			GL_TEXTURE_2D,
			0,
			GL_RED,
			face->glyph->bitmap.width,
			face->glyph->bitmap.rows,
			0,
			GL_RED,
			GL_UNSIGNED_BYTE,
			face->glyph->bitmap.buffer
		);
		// set texture options
		glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_CLAMP_TO_EDGE);
		glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_CLAMP_TO_EDGE);
		glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
		glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
		// now store character for later use
		Character character = {
		texture,
		glm::ivec2(face->glyph->bitmap.width, face->glyph->bitmap.rows),
		glm::ivec2(face->glyph->bitmap_left, face->glyph->bitmap_top),
		face->glyph->advance.x
		};
		Characters.insert(std::pair<char, Character>(c, character));
	}
	FT_Done_Face(face);
	FT_Done_FreeType(ft);
```
## 12.2 Shaders
To render the glyphs we'll be using the following vertex shader:
```
#version 330 core
layout (location = 0) in vec4 vertex; // <vec2 pos, vec2 tex>
out vec2 TexCoords;

uniform mat4 projection;

void main()
{
    gl_Position = projection * vec4(vertex.xy, 0.0, 1.0);
    TexCoords = vertex.zw;
}
```
And the fragment shader:
```

#version 330 core
in vec2 TexCoords;
out vec4 color;

uniform sampler2D text;
uniform vec3 textColor;

void main()
{    
    vec4 sampled = vec4(1.0, 1.0, 1.0, texture(text, TexCoords).r);
    color = vec4(textColor, 1.0) * sampled;
} 
```

We come back to the Game.cpp file and in its main loop we will insert the enabling of the blending:
```
glEnable(GL_BLEND);
glBlendFunc(GL_SRC_ALPHA, GL_ONE_MINUS_SRC_ALPHA); 
```
For the projection matrix we'll be using an orthographic projection matrix.
```
glm::mat4 projection = glm::ortho(0.0f, 800.0f, 0.0f, 600.0f);
```
Last up is creating a VBO and VAO for rendering the quads. For now we reserve enough memory when initiating the VBO so that we can later update the VBO's memory when rendering characters:
```
unsigned int VAO, VBO;
glGenVertexArrays(1, &VAO);
glGenBuffers(1, &VBO);
glBindVertexArray(VAO);
glBindBuffer(GL_ARRAY_BUFFER, VBO);
glBufferData(GL_ARRAY_BUFFER, sizeof(float) * 6 * 4, NULL, GL_DYNAMIC_DRAW);
glEnableVertexAttribArray(0);
glVertexAttribPointer(0, 4, GL_FLOAT, GL_FALSE, 4 * sizeof(float), 0);
glBindBuffer(GL_ARRAY_BUFFER, 0);
glBindVertexArray(0); 
```
## 12.3 Render Text

To render the text, we create a function that renders a string of characters:
```
void RenderText(Shader &s, std::string text, float x, float y, float scale, glm::vec3 color)
{
    // activate corresponding render state	
    s.Use();
    glUniform3f(glGetUniformLocation(s.Program, "textColor"), color.x, color.y, color.z);
    glActiveTexture(GL_TEXTURE0);
    glBindVertexArray(VAO);

    // iterate through all characters
    std::string::const_iterator c;
    for (c = text.begin(); c != text.end(); c++)
    {
        Character ch = Characters[*c];

        float xpos = x + ch.Bearing.x * scale;
        float ypos = y - (ch.Size.y - ch.Bearing.y) * scale;

        float w = ch.Size.x * scale;
        float h = ch.Size.y * scale;
        // update VBO for each character
        float vertices[6][4] = {
            { xpos,     ypos + h,   0.0f, 0.0f },            
            { xpos,     ypos,       0.0f, 1.0f },
            { xpos + w, ypos,       1.0f, 1.0f },

            { xpos,     ypos + h,   0.0f, 0.0f },
            { xpos + w, ypos,       1.0f, 1.0f },
            { xpos + w, ypos + h,   1.0f, 0.0f }           
        };
        // render glyph texture over quad
        glBindTexture(GL_TEXTURE_2D, ch.textureID);
        // update content of VBO memory
        glBindBuffer(GL_ARRAY_BUFFER, VBO);
        glBufferSubData(GL_ARRAY_BUFFER, 0, sizeof(vertices), vertices); 
        glBindBuffer(GL_ARRAY_BUFFER, 0);
        // render quad
        glDrawArrays(GL_TRIANGLES, 0, 6);
        // now advance cursors for next glyph (note that advance is number of 1/64 pixels)
        x += (ch.Advance >> 6) * scale; // bitshift by 6 to get value in pixels (2^6 = 64)
    }
    glBindVertexArray(0);
    glBindTexture(GL_TEXTURE_2D, 0);
}
```
and again coming back to the Game.cpp file, in the main loop, we shuld be able to render strings of text:
```
RenderText(shader, "This is sample text", 25.0f, 25.0f, 1.0f, glm::vec3(0.5, 0.8f, 0.2f));
RenderText(shader, "(C) LearnOpenGL.com", 540.0f, 570.0f, 0.5f, glm::vec3(0.3, 0.7f, 0.9f));
```
This should then look similar to the following image:

![LearnOpenGL_2](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/03b5ceda-9557-460c-b0b4-479f3c1349b7)

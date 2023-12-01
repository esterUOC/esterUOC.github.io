---
title: "Lesson0_OG"
permalink: "/Lesson0_OG/"
layout: page
---

Official Learning Resource: [LearnOpenGL  - Getting started](https://learnopengl.com/Getting-started/OpenGL)

## What is OpenGL?
In this new PAC we will leave behind the use of the Raylib framework and start using OpenGL, considered as an API that provides a large number of functions that we can use to manipulate graphics and images.

Well, although OpenGL is not exactly an API, it can be considered a specification that indicates what result calls to the functions provided by OpenGL should give. Then, it is the developers responsible for implementing these specifications who “create” the specific code. It is usually the companies that create GPUs that are responsible for implementing these functions.

This is why the vast majority of GPUs already support OpenGL, since the companies themselves have been responsible for creating the implementation of these functions. And that is why it is advisable to update the drivers of our graphics cards, just to update some implementations of functions.

Early on, OpenGL was developed in named modeimmediate (often known as
fixed function pipeline) which was basically a method that made it very easy to draw graphics, since most of OpenGL's functionality was hidden from developers and they didn't have much control over how OpenGL did its calculations. Immediate mode is very easy to use and understand, but it's also extremely inefficient because it didn't give developers much flexibility.

For this reason, the immediate specification began to be deprecated from version 3.2 and from this version onwards developers were encouraged to develop in profile-basic mode (core-profile) of OpenGL, which is a split of the OpenGL specification that removes all old deprecated functionality or forces us to use new versions of the features.

This new version of OpenGL is more flexible and efficient, but at the same time more complex to learn, as it requires the developer to really understand how OpenGL works internally.

We will learn OpenGL 3.3, although the library is currently already in version 4.8. This is not a problem, since all versions since 3.3 add useful additional features for OpenGL without changing the core mechanics of OpenGL; newer versions just introduce slightly more efficient or more useful ways to accomplish the same tasks.

Links of interest: [OpenGL official page](https://www.opengl.org)  

## State machine
OpenGL is itself a big state machine: a collection of variables that define how it should work at any given time.
An OpenGL state is commonly defined as an OpenGL context. When using OpenGL, we often change its state by setting some options, manipulating some buffers, and finally rendering the current context.
For example: if we tell OpenGL that we want to draw lines instead of triangles, we will change OpenGL's state by changing some context variable that defines how OpenGL should paint.
When we work in OpenGL we will find:
 - functions that change the state of the OpenGL context
 - functions that use the defined state to perform operations based on the current context.


---
title: "Lesson3"
permalink: "/Lesson3/"
layout: page
---
To modify the VisualStudio compilation rules we will have to select a project from our solution and click with the right button.

 ![1](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/36c0cb9b-7e21-4ad8-b44e-74b5390796d9)


Next, the window will appear where we can change the properties of our project

 ![2](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/95b5f7c4-2e12-49ac-b1cc-6c01db1e95ea)



In this window we will determine the specifications when building a project for a specific platform.

 We will start by talking about the upper section, where the Configuration and the Platform are indicated.

 ![3](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/2c8ff9d3-fcf8-4131-81b4-e83d211c4f3e)


The Configuration part contains a list with the existing configuration types by default (Debug and Release), we can select all and/or create new ones. Above all, it is important to make sure that we have selected the settings that we want to modify. Otherwise, the changes will be applied to the configuration that is selected and not to the one we want, this is an error that happens very frequently.

 ![4](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/be82b3b9-3030-4d44-8d74-edd5280e357a)


For the platform we default to these options: remember x86 is the same as Win32.

 ![5](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/7fb745b2-e4b9-4b42-85e9-48deeb4ee524)


In the "General" section we can find information such as:
-	The version of the SDK being used
-	The output directory, or where the compiled program will be saved
-	The intermediate directory, or where the temporary files will be generated and saved
-	The type of configuration, here we specify which type of binary we want to generate, .exe, .dll, .lib, among others
-	etc…

 
![6](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/4d2d44d6-690d-4e14-90c9-b8eaa873ed27)



The compiler settings are in the “C/C++” tab, here we can find:
-	In the "General" section, the folder of the external files we want to include
-	In the "Optimization" section, we can configure what kind of binary code we want to create, whether highly optimized or not.
-	In the "Preprocessor" section, we define the preprocessed symbols we want or need.

  ![7](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/aa26d5ae-fa93-489e-91a7-5313b56941be)![8](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/c79eb95c-9850-498c-9e86-6d6da2625a61)



Example of the different configurations available in the optimization section in the case of Debug and Release.

In the "Linker" section, we will find:
-	In the "General" section, the folder of the external libraries we want to include
-	In the "Input" section where we will add the additional files needed for linking

 ![9](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/de79f717-2089-4f43-96c3-4b902ec9d699)

 ![10](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/41b7b822-acda-4b89-bb36-3dc8c3a71670)






---
title: "Lesson1_OG"
permalink: "/Lesson1_OG/"
layout: page
---
## 1. Inicialització del projecte

En aquesta lliçó explicarem pas a pas com crear la nostra primera finestra utilitzant OpenGL. 

##1.1	Creació del projecte buit amb Visual Studio 2022


Crearem aquesta finestra utilitzant l’IDE Visual Studio 2022. Lo primer que hem de fer és crear un nou projecte amb Visual Studio, escollirem el llenguatge C++ i la opció d’Empty Project

 ![1](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/ce08a59f-fb0f-4870-9148-19c320c7528b)

Poseu un nom adequat al projecte i guardeu-lo on vosaltres cregueu convenient.

Recordeu de configurar el projecte en Debug i 64bits
![2](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/59216f99-d590-4590-8af6-a0d59dc31684)

Afegirem un fitxer Game.cpp .

Una vegada tenim el projecte creat, el primer que hem de fer abans de començar a crear gràfics impressionants és:

1.	 crear un context OpenGL 
2.	una finestra d'aplicació per dibuixar. 

Aquest parell de punts són específiques del sistema operatiu en que treballem i a més a més OpenGL intenta abstraure's d'aquestes operacions. Això vol dir que hem de crear una finestra, definir un context i gestionar l'entrada de l'usuari per nosaltres mateixos.

Afortunadament, hi ha força biblioteques que ofereixen la funcionalitat que busquem, algunes especialment dirigides a OpenGL. Aquestes biblioteques ens estalvien tot el treball específic del sistema operatiu i ens donen una finestra i un context OpenGL per renderitzar-nos. Algunes de les biblioteques més populars són GLUT, SDL, SFML i GLFW. 

Noslatres farem servir GLFW. No dubteu a utilitzar qualsevol de les altres biblioteques, la configuració de la majoria és similar a la de GLFW.

## 1.2	Baixar-nos i instalar GLFW: 

GLFW és una llibreria específica per ser utilitzada en OpenGL, Bàsicament ens permetrà crear un context d’OpenGL, definir els paràmetres de la finestra i manipular els inputs de l’usuari.

Primer de tot hem d’anar a la pàgina web: https://www.glfw.org/download.html, i baixar-nos els binaris en 64bits per a windows.
![3](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/06f0b150-b986-45bc-8b46-e8eea2750742)

 
Anirem a la nostra carpeta de projecte i crearem 2 carpetes noves:

-	(opcional) 3rdParty : aqui guardarem els fitxers originals de les llibreries externes utilitzades
-	Dependencies: aquí guardarem els fixters necessaris per a la nostra aplicació

Extraurem el contingut del fitxer glfw-3.3.8.bin.WIN64 a la carpeta 3rdParty i a la carpeta Dependencies crearem una carpeta nova anomenada GLFW, i a dins crearem dos carpetes una anomenada include i una lib:

 ![4](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/c11e4561-8fd4-4ea3-8c6b-bf0363ec7288)

Copiarem el contingut de glfw-3.3.8.bin.WIN64\include a la carpeta Dependencies\GLFW\include de tal manera que quedi:

 ![5](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/6c21b288-e7b6-4796-b7d8-b520de00c5dd)


Copiarem el contingut de glfw-3.3.8.bin.WIN64\lib-vc2022 a Dependencies\GLFW\lib de tal manera que quedi
![6](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/1788706d-f271-46d3-b1d9-b5fe03f3f9b7)


## 1.3	Inicialitzar GLFW al nostre projecte: 

Per poder utilitzar GLFW en el nostre projecte, hem d-incloure a la nostra solució els fitxer de la carpeta include i la lib.

Obre la finestra de propietats del projecte, a partir del menu Project->Properties o alt+F7. Assegureu-vos de tenir seleccionat les opcions de Debug i x64
 ![7](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/e1ec8b61-5441-4be0-84ef-6873f3935a79)

Per incloure els fitxers de la carpeta include, seleccionarem l’apartat C/C++ -> General, i a Additional Include Directories afegirem la linea $(SolutionDir)Dependencies\GLFW\include\ 

![8](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/92d0b32f-0c72-464c-b5b1-68266e3f3dc4)

Per incloure els fitxers de la carpeta lib, seleccionarem l’apartat Linker->General, i a l’apartat Additonal Library Directories afegirem la linea $(SolutionDir)Dependencies\GLFW\lib
 ![9](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/21b4311c-b1ca-4ff8-bb90-1ac57235b1f6)


També será necessari afegir a l’apartat Linker->Input, a l’apartat Additional Dependencies la llibreria glfw3.lib

 ![10](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/c1f1b5af-0121-4753-81f6-9d9895d54fd0)



-	baixar-nos i instalar GLAD:  Aneu a la pàgina: [link GLAD](https://glad.dav1d.de/)

Seleccioneu el llenguate C++, en Specification escolliu OpenGL, en API escolliu la versió 3.3, en la secció Profile escolliu Core i finalment comproveu que Generate a loader está check. Cliqueu el botó Generate i baixeu-vos el .zip generat. 
![11](https://github.com/esterUOC/esterUOC.github.io/assets/128288660/d32fdedc-cfd6-4003-8e3e-1813da0a4a66)


El fitxer zip conté dues carpetes, afegirem la capeta anomenada include al nostre projecte, tal i com hem fet anteriorment i afegirem el fitxer glad.c al vostre projecte.


Ja ho tenim tot fet, al fitxer Game.cpp afegim 

#include <glad/glad.h>
#include <GLFW/glfw3.h>

int main()
{
	return 0;
}

Recordar que primer sempre hem d’incloure l’include de glad, ja que aquest incloueix els fitxers headers d’OpenGL (per exemple GL/gl.h) que son utilitzats per altres llibreries com ara GLFW.
Hauriem de compilar i comprovar que ho fa amb exit, que no apareix cap error, la execució d’aquest codi no produeix cap finestra ni res.
En la següent lliçó, crearem la nostra primera finestra en OpenGL.

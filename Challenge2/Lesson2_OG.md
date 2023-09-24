---
title: "Lesson2_OG"
permalink: "/Lesson2_OG/"
layout: page
---

## 2. Primera finestra

En aquesta lliçó explicarem pas a pas com crear la nostra primera finestra utilitzant OpenGL. 

## 2.1	Ús de la biblioteca GLFW
Anem a inicialitzar una finestra de GLFW. Dins del main anem a escriure:
```
int main()
{
	glfwInit();
	glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
	glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
	glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
	//glfwWindowHint(GLFW_OPENGL_FORWARD_COMPAT, GL_TRUE);
	return 0;
}
```

glfwInit inicialitza GLFW, i seguidament podem inicialitzar una finestra utilitzant la comanda glfwWindowHint. Aquesta funció es caracteritza perquè com a primer argument indiquem quina opció volem inicialitzar, i com a segon argument indiquem el valor. A la documentació de GLFW podreu trobar totes les possibles opcions configurables.

En el nostre cas indiquem que volem volem usar la versió d’OpenGL 3.3, i el mode core, tal com hem explicat en la lliçó 0.

A continuació afegirem el següent codi per a crear la finestra:
```
	// Creació de la finestra
	GLFWwindow* window = glfwCreateWindow(800, 600, "LearnOpenGL", NULL, NULL);
	if (window == NULL)
	{
		std::cout << "Failed to create GLFW window" << std::endl;
		glfwTerminate();
		return -1;
	}
	glfwMakeContextCurrent(window);
```

La funció glfwCreateWindow, per crear la finestra, necessita els següents parameters, l’amplada, la llargada, el nom que li donem a la finestra, els dos següents paràmeters els podem ignorar.
Si l’objecte window es crea de forma satisfactoria, llavors crearem el context per a la nostra finestra amb flfwMakeContextCurrent.


## 2.2	Ús de la biblioteca GLAD
Just després del codi de GLFW afegirem el codi que inicialitza GLAD, recordeu que és necessari executar aquest codi abans de cridar cap funció d’OpenGL.
```
	// Inicialització de GLAD
	if (!gladLoadGLLoader((GLADloadproc)glfwGetProcAddress))
	{
		std::cout << "Failed to initialize GLAD" << std::endl;
		return -1;
	}
```
	Aquest tros de codi el que fa és: glfwGetProcAddress  és una funció que retorna l'adreça de la funció principal o extensió d'OpenGL especificada, si és compatible amb el context actual. I aquesta adreça la passem a GLAD que carregarà els punters de les funcions d’OpenGL específica del sistema operatiu en el que s’està executant el codi.

La compilació ha de ser correcte i l’execució del codi ha de funcionar també tot i que no es pot veure res.


## 2.3	Definició del viewport
Per poder renderitzar alguna cosa, necessitem dir-li a OpenGL el tamany de la finestra on volem renderitzar els nostres poligons i malles, aixó o farem amb la funció glViewport.

Els dos primers paràmeters defineixen el punt baix a l’esquerra de la finestra, el tercer paràmetre defineix l’ample i finalment el quart defineix l’alçada. En aquest cas els valors coincideixen amb la finestra definida anteriorment amb GLFW, tot i que poden tenir diferent tamany.
```
glViewport(0, 0, 800, 600);
```
OpenGL utilitza les dades especificades mitjançant glViewport per transformar les coordenades 2D que va processar en coordenades a la pantalla. Per exemple, un punt d'ubicació processat (-0,5, 0,5) s'assignaria (com a transformació final) a (200.450) en coordenades de pantalla. Tingueu en compte que les coordenades processades a OpenGL estan entre -1 i 1, de manera que mapem efectivament des de l'interval (-1 a 1) fins a (0, 800) i (0, 600).

## 2.3.1	Resize de la finestra

Haurem d’afegir funcions callback que es cridaran quan l’usuari canvia la mida de la finestra o viewport. 
Afegirem la definició i la declaració de la funció a sobre de la funció main:
```
void framebuffer_size_callback(GLFWwindow* window, int width, int height);
void framebuffer_size_callback(GLFWwindow* window, int width, int height)
{
	glViewport(0, 0, width, height);
}
```
Ara registrarem aquest callback just després de la creació del viewport .
```
 // callback de resize
	glfwSetFramebufferSizeCallback(window, framebuffer_size_callback);
```

## 2.4	Definició del game loop

Quasi ja ho tenim, ara afegirem el loop principal del joc, el qual s’anirà executant indefinidiament fins que no li diguem lo contrari:
```
	// Game Loop
	while (!glfwWindowShouldClose(window))
	{
		glfwSwapBuffers(window);
		glfwPollEvents();
	}
```
Amb aquest loop el que conseguirem és seguir pintant imatges i manejant l’input de l’usuari fins que l’usuari explicitament tanca la finestra.

La funció glgwWindowShouldClose comprova  si GLFW ha estat instruit a tancar.
La funció glfwPollEvents comprova si hi ha inputs, com l'entrada del teclat o el moviment del ratolí.
El glfwSwapBuffers canviarà el color buffer que s'utilitza per renderitzar durant aquesta iteració de renderització i mostrar-la com a sortida a la pantalla.


2.5	Tancar recursos

Quan sortim del game loop perquè volem tancar la finestra haurem de tancar i lliberar tots els recursos de manera adequada, per això afegirem el següent codi després del main loop:

	// Lliberar recursos
	glfwTerminate();

En aquest punt si compilem i executem el codi, ens hauria d’aparèixer una finestra negre amb el nom que li hem indicat anteriorment.

 


2.6	Manejant l’Input

Utiitzarem la funció de GLFW glfwGetKey per manejar l’input, més concretament per saber si una tecla ha estat premuda o no.

Visiteu aquesta pàgina per accedir a totes les funcions oferides per GLFW: https://www.glfw.org/docs/3.3/group__input.html

Crearem una funció pròpia que controla si una tecla ha estat premuda o no, en el cas que si, farem una acció si escau. Afegim la declaració i definició d’aquesta funció fora del main.


void processInput(GLFWwindow* window)
{
	if (glfwGetKey(window, GLFW_KEY_ESCAPE) == GLFW_PRESS)
		glfwSetWindowShouldClose(window, true);
}

I procedirem a cridar aquesta funció dins el game loop, d’aquesta manera, comprovarem en cada frame si hi ha alguna tecla premuda.

Si compilem i executem podrem comprovar com podem tancar la finestra prement la tecla ESC


2.7	Reset del contingut del viewport

Localitzarem totes les comandes de rendering dins del game loop, just després del manejament de l’input.  
Lo primer que s’ha de fer abans de pintar un nou frame, és natejar la finestra amb un color de la nostra conveniència, sinò ho fessim així el resultat del frame anterior podria interferir en la imatge nova que estem a punt de calcular.

Utilitzarem la funció glClear per natejar el viewport. Especifiquem el color per netejar la pantalla amb glClearColor. Sempre que anomenem glClear i esborrem el buffer de color, tot el buffer de color s'omplirà amb el color indicat per glClearColor. Això donarà com a resultat un color verd fosc blavós.

La funció glClearColor és una funció que configura l’estat d’OpenGL i glClear és una funció que utilitza l'estat definit, ja que fa servir l'estat actual per recuperar el color de netejar.

Si compilem i executem hauriem d’aconseguir la següent pantalla


 









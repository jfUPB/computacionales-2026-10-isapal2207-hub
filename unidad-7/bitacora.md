# Unidad 7

## Bitácora de proceso de aprendizaje
### ACTIVIDAD 1
3 PREGUNTAS

¿Por qué se usa layout(location = 0) en el vertex shader? 
¿Cómo sabe el programa cuándo dibujar el triángulo y cuándo actualizar la pantalla? 
¿Qué función cumplen exactamente VAO y VBO en relación con los datos del triángulo?

HIPOTESIS:
Creo que el programa necesita enviar los datos del triángulo a la GPU mediante buffers (VBO/VAO) y luego usar un programa de shaders activo para interpretar esos datos, de modo que la GPU pueda procesarlos y finalmente mostrarlos en la ventana creada. 

### ACTIVIDAD 2
Para que un programa con OpenGL funcione en Windows no es solo escribir el código, sino que se necesitan varias herramientas que trabajan juntas.
Primero está GLFW, que es el que se encarga de abrir la ventana y manejar cosas como el teclado y el mouse. OpenGL por sí solo no puede hacer eso, entonces GLFW es necesario para poder ver lo que se dibuja.
También está opengl32.lib, que ya viene con Windows. Esta librería sirve para conectar el programa con OpenGL, pero solo con funciones básicas, así que por sí sola no es suficiente para usar lo más moderno.
Por eso se usa GLAD, que lo que hace es cargar las funciones más avanzadas de OpenGL. Esas funciones no están en Windows, sino en los drivers de la tarjeta gráfica, entonces GLAD las obtiene desde ahí para que el programa las pueda usar.
Los drivers de la GPU son los que realmente hacen el trabajo de dibujar en pantalla. O sea, todo lo que ves lo procesa la tarjeta gráfica a través de esos drivers.
Y por último está GLM, que no es obligatorio, pero ayuda mucho con las matemáticas, como trabajar con vectores y matrices, lo cual es muy útil en gráficos.


### ACTIVIDAD 3
¿Qué es el contexto OpenGL?

El contexto OpenGL es el entorno donde se ejecutan todas las funciones de OpenGL. Es como un espacio de trabajo que contiene toda la información necesaria para renderizar gráficos, como configuraciones, estados y recursos. Sin este contexto, OpenGL no puede funcionar porque no tendría dónde ejecutar las operaciones gráficas.

¿Cuál es el rol de la biblioteca GLFW y qué ventaja tiene usarla?

GLFW se encarga de crear la ventana, manejar el teclado y el mouse, y además crear el contexto OpenGL. La ventaja de usar GLFW es que simplifica mucho el proceso, ya que permite trabajar de forma multiplataforma sin tener que lidiar directamente con el sistema operativo.

¿Por qué OpenGL necesita un contexto?

OpenGL necesita un contexto porque requiere un lugar donde almacenar y ejecutar todas sus configuraciones y operaciones. Siguiendo la analogía del taller de arte, el contexto sería el taller donde están las herramientas, los materiales y el espacio para trabajar. Sin ese taller, no se podría crear nada.

¿Qué es el framebuffer y a qué te recuerda?

El framebuffer es una estructura donde se almacenan los píxeles que se van a mostrar en pantalla. Es como una imagen en memoria que luego se presenta al usuario. Esto recuerda a los conceptos de imágenes digitales vistos anteriormente, donde cada píxel tiene un color y juntos forman una imagen completa.

¿Qué relación hay entre el viewport y el framebuffer?

El viewport define qué parte de la ventana (o del framebuffer) se va a usar para dibujar. Es como una ventana dentro de otra ventana. El framebuffer contiene todos los píxeles, y el viewport indica en qué región se renderiza la imagen.

¿Qué rol juegan los drivers de la GPU y la GPU?

Los drivers de la GPU son los encargados de proporcionar las funciones modernas de OpenGL y de comunicar el programa con la tarjeta gráfica. La GPU es la que realmente realiza los cálculos y procesa los gráficos. En conjunto, son los responsables de transformar los datos en imágenes visibles.

¿Por qué es necesario activar el VSync?

El VSync sincroniza la velocidad de renderizado con la tasa de refresco del monitor. Si no se activa y la imagen es dinámica, pueden aparecer efectos como el “tearing”, donde la imagen se ve cortada. Si la imagen es estática, puede que no se note tanto, pero igual se pueden generar inconsistencias visuales o uso innecesario de recursos.

¿Qué es OpenGL Legacy y qué diferencia tiene con OpenGL moderno?

OpenGL Legacy es la versión antigua de OpenGL donde muchas operaciones se hacían de forma automática y fija (por ejemplo, el pipeline fijo). En cambio, OpenGL moderno usa shaders y permite mayor control sobre el proceso de renderizado. La diferencia principal es que el moderno es más flexible y eficiente, pero también más complejo.

¿Qué es el shader program y por qué es importante?

El shader program es un conjunto de shaders (principalmente vertex y fragment shaders) que se ejecutan en la GPU. Es importante porque en OpenGL moderno todo el procesamiento gráfico depende de estos programas, ya que no existe un pipeline fijo como antes.

¿Qué hace setupTriangle()? ¿Qué son VAO y VBO?

La función setupTriangle() probablemente se encarga de configurar los datos del triángulo para que puedan ser usados por la GPU. El VBO almacena los datos de los vértices, mientras que el VAO organiza cómo se deben interpretar esos datos (por ejemplo, qué atributo corresponde a cada vértice).

¿Es necesario usar el shader program y el VAO en cada loop?

En teoría, si no cambian, no sería necesario configurarlos en cada iteración. Sin embargo, en la práctica se hace en cada loop porque el estado de OpenGL puede cambiar. Esto es útil cuando se trabajan múltiples objetos o diferentes shaders, ya que se necesita asegurar que se está usando el correcto en cada momento.

¿Por qué es importante glfwSwapBuffers()?

Esta función intercambia el buffer que se está mostrando con el que se acaba de renderizar. Esto es importante porque permite mostrar la imagen completa de forma fluida. Si no se llama, la imagen no se actualiza correctamente y puede que no se vea nada o que aparezcan errores visuales, ya que el buffer visible nunca cambia.

### ACTIVIDAD 4
<img width="1059" height="673" alt="image" src="https://github.com/user-attachments/assets/37e8943d-6b6e-4138-aae5-7df7aaf45410" />
En esta actividad se modificó el programa del triángulo para hacerlo interactivo usando la posición del mouse. Se implementó un callback que captura la posición del cursor y actualiza el triángulo en tiempo real.

El cambio principal fue en el vertex shader, donde se agregó una variable uniforme llamada offset, que permite mover el triángulo según la posición del mouse. Este valor se actualiza constantemente en el ciclo de renderizado.

Normalización de las coordenadas del mouse

Las coordenadas del mouse están en píxeles, con origen en la esquina superior izquierda. Como OpenGL usa un sistema diferente, estas coordenadas deben convertirse al rango de -1 a 1:

offsetX = (xpos / width) * 2.0 - 1.0
offsetY = -((ypos / height) * 2.0 - 1.0)

Esto permite que el centro de la ventana sea (0,0) y los bordes estén entre -1 y 1.

Relación con OpenGL (NDC)

OpenGL utiliza coordenadas NDC, donde todo lo visible debe estar entre -1 y 1 en X y Y. Por eso es necesario normalizar las coordenadas del mouse, para que el triángulo se pueda mover correctamente en pantalla.

Normalización a NDC

Este proceso consiste en convertir coordenadas de pantalla a un rango estándar (-1 a 1). Esto permite que el programa funcione correctamente sin importar el tamaño de la ventana.

Explicación del código

El programa crea una ventana con GLFW y carga OpenGL con GLAD. Luego define los vértices del triángulo y los envía a la GPU usando VBO y VAO.

Se crean los shaders, donde el vertex shader usa la variable offset para mover el triángulo. El fragment shader define el color.

El callback del mouse actualiza la posición, y en el loop principal se envían estos valores al shader y se dibuja el triángulo continuamente.

Evidencia

Se incluye una captura donde se observa el triángulo moviéndose según el mouse.

Conclusión

La interacción se logra al convertir las coordenadas del mouse al sistema de OpenGL. La normalización a NDC es clave para que el triángulo se posicione correctamente, y los shaders permiten aplicar el movimiento en tiempo real.

### ACTIVIDAD 5


Triangle.cpp

	#include <iostream>
	#include <glad/glad.h>
	#include <GLFW/glfw3.h>
	
	// Input
	void processInput(GLFWwindow* window) {
	    if (glfwGetKey(window, GLFW_KEY_ESCAPE) == GLFW_PRESS)
	        glfwSetWindowShouldClose(window, true);
	}
	
	// Shaders
	const char* vertexShaderSrc = R"glsl(
	#version 460 core
	layout (location = 0) in vec3 aPos;
	void main() {
	    gl_Position = vec4(aPos, 1.0);
	}
	)glsl";
	
	const char* fragmentShaderSrc = R"glsl(
	#version 460 core
	out vec4 FragColor;
	uniform float time;
	void main() {
	    float green = (sin(time) / 2.0) + 0.5;
	    FragColor = vec4(0.0, green, 0.0, 1.0);
	}
	)glsl";
	
	// Crear shaders
	unsigned int buildShaderProgram() {
	    unsigned int vs = glCreateShader(GL_VERTEX_SHADER);
	    glShaderSource(vs, 1, &vertexShaderSrc, NULL);
	    glCompileShader(vs);
	
	    unsigned int fs = glCreateShader(GL_FRAGMENT_SHADER);
	    glShaderSource(fs, 1, &fragmentShaderSrc, NULL);
	    glCompileShader(fs);
	
	    unsigned int prog = glCreateProgram();
	    glAttachShader(prog, vs);
	    glAttachShader(prog, fs);
	    glLinkProgram(prog);
	
	    glDeleteShader(vs);
	    glDeleteShader(fs);
	
	    return prog;
	}
	
	int main() {
	
	    glfwInit();
	    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 4);
	    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 6);
	
	    GLFWwindow* window = glfwCreateWindow(400, 400, "Animacion OpenGL", NULL, NULL);
	    glfwMakeContextCurrent(window);
	
	    gladLoadGLLoader((GLADloadproc)glfwGetProcAddress);
	
	    float vertices[] = {
	        -0.5f, -0.5f, 0.0f,
	         0.5f, -0.5f, 0.0f,
	         0.0f,  0.5f, 0.0f
	    };
	
	    unsigned int VAO, VBO;
	    glGenVertexArrays(1, &VAO);
	    glGenBuffers(1, &VBO);
	
	    glBindVertexArray(VAO);
	
	    glBindBuffer(GL_ARRAY_BUFFER, VBO);
	    glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
	
	    glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
	    glEnableVertexAttribArray(0);
	
	    unsigned int shaderProgram = buildShaderProgram();
	
	    while (!glfwWindowShouldClose(window)) {
	
	        processInput(window);
	
	        glClearColor(0.1f, 0.1f, 0.1f, 1.0f);
	        glClear(GL_COLOR_BUFFER_BIT);
	
	        glUseProgram(shaderProgram);
	
	        float timeValue = glfwGetTime();
	        int timeLoc = glGetUniformLocation(shaderProgram, "time");
	        glUniform1f(timeLoc, timeValue);
	
	        glBindVertexArray(VAO);
	        glDrawArrays(GL_TRIANGLES, 0, 3);
	
	        glfwSwapBuffers(window);
	        glfwPollEvents();
	    }
	
	    glfwTerminate();
	}


<img width="752" height="441" alt="image" src="https://github.com/user-attachments/assets/e6629b7f-0afe-4012-8d20-1befdb791ccb" />
<img width="751" height="507" alt="image" src="https://github.com/user-attachments/assets/dd886155-2066-46a0-9ae2-7638ff267711" />



En este programa se mantiene la estructura básica de OpenGL: creación de ventana, inicialización de GLAD, definición de vértices y uso de buffers (VAO y VBO).

La diferencia principal está en el fragment shader, donde se utiliza una variable uniforme llamada time. Esta variable recibe el tiempo actual del programa mediante la función glfwGetTime().

Dentro del shader, se utiliza la función seno (sin) para generar un valor que cambia continuamente entre 0 y 1. Este valor se usa para modificar el canal verde del color, lo que produce un efecto de animación en el triángulo.

En cada iteración del ciclo de renderizado, el programa envía el valor actualizado del tiempo al shader, lo que permite que el color cambie de forma continua.

## Bitácora de aplicación 

### ACTIVIDAD 6
#### FASE 1
Triangle.cpp

	#include <iostream>
	#include <glad/glad.h>
	#include <GLFW/glfw3.h>
	
	// Input
	void processInput(GLFWwindow* window) {
	    if (glfwGetKey(window, GLFW_KEY_ESCAPE) == GLFW_PRESS)
	        glfwSetWindowShouldClose(window, true);
	}
	
	// Vertex Shader
	const char* vertexShaderSrc = R"glsl(
	#version 460 core
	layout (location = 0) in vec3 aPos;
	uniform vec2 offset;
	void main() {
	    gl_Position = vec4(aPos.x + offset.x, aPos.y + offset.y, aPos.z, 1.0);
	}
	)glsl";
	
	// Fragment Shader
	const char* fragmentShaderSrc = R"glsl(
	#version 460 core
	out vec4 FragColor;
	uniform float time;
	void main() {
	    float green = (sin(time) / 2.0) + 0.5;
	    FragColor = vec4(0.2, green, 0.8, 1.0);
	}
	)glsl";
	
	// Crear shaders
	unsigned int buildShaderProgram() {
	    unsigned int vs = glCreateShader(GL_VERTEX_SHADER);
	    glShaderSource(vs, 1, &vertexShaderSrc, NULL);
	    glCompileShader(vs);
	
	    unsigned int fs = glCreateShader(GL_FRAGMENT_SHADER);
	    glShaderSource(fs, 1, &fragmentShaderSrc, NULL);
	    glCompileShader(fs);
	
	    unsigned int prog = glCreateProgram();
	    glAttachShader(prog, vs);
	    glAttachShader(prog, fs);
	    glLinkProgram(prog);
	
	    glDeleteShader(vs);
	    glDeleteShader(fs);
	
	    return prog;
	}
	
	int main() {
	
	    glfwInit();
	    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 4);
	    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 6);
	
	    GLFWwindow* window = glfwCreateWindow(400, 400, "Actividad 05", NULL, NULL);
	    glfwMakeContextCurrent(window);
	
	    gladLoadGLLoader((GLADloadproc)glfwGetProcAddress);
	
	    float vertices[] = {
	        -0.2f, -0.2f, 0.0f,
	         0.2f, -0.2f, 0.0f,
	         0.0f,  0.2f, 0.0f
	    };
	
	    unsigned int VAO, VBO;
	    glGenVertexArrays(1, &VAO);
	    glGenBuffers(1, &VBO);
	
	    glBindVertexArray(VAO);
	
	    glBindBuffer(GL_ARRAY_BUFFER, VBO);
	    glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
	
	    glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3*sizeof(float), (void*)0);
	    glEnableVertexAttribArray(0);
	
	    unsigned int shaderProgram = buildShaderProgram();
	
	    while (!glfwWindowShouldClose(window)) {
	
	        processInput(window);
	
	        glClearColor(0.1f, 0.1f, 0.1f, 1.0f);
	        glClear(GL_COLOR_BUFFER_BIT);
	
	        glUseProgram(shaderProgram);
	
	        float timeValue = glfwGetTime();
	        float offsetX = sin(timeValue) * 0.5f;
	
	        int timeLoc = glGetUniformLocation(shaderProgram, "time");
	        glUniform1f(timeLoc, timeValue);
	
	        int offsetLoc = glGetUniformLocation(shaderProgram, "offset");
	        glUniform2f(offsetLoc, offsetX, 0.0f);
	
	        glBindVertexArray(VAO);
	        glDrawArrays(GL_TRIANGLES, 0, 3);
	
	        glfwSwapBuffers(window);
	        glfwPollEvents();
	    }
	
	    glfwTerminate();
	}

#### FASE 2

##### EVIDENCIA 1
<img width="710" height="535" alt="image" src="https://github.com/user-attachments/assets/fd15d9b1-677c-4b7d-ac6e-b88fd16bafba" />

Explicación:
GLFW se usa primero porque crea la ventana y el contexto OpenGL. Sin ese contexto, OpenGL no puede funcionar. GLAD depende de ese contexto para cargar las funciones modernas de OpenGL desde los drivers.

Justificación:
GLAD necesita saber qué funciones están disponibles en el contexto activo. Si se ejecuta antes de GLFW, no habría contexto y fallaría.

##### EVIDENCIA 2
<img width="1214" height="390" alt="image" src="https://github.com/user-attachments/assets/b0149ce5-6044-483c-8213-bd1b8ecc265c" />

Explicación:
El arreglo de vértices se guarda en el VBO con glBufferData. Luego, glVertexAttribPointer conecta esos datos con el atributo aPos del vertex shader (location = 0).

Justificación:
El VAO almacena cómo interpretar los datos del VBO. Sin esta conexión, el shader no recibiría los vértices.

##### EVIDENCIA 3
<img width="407" height="423" alt="image" src="https://github.com/user-attachments/assets/15c9fe5f-ffbf-4dcf-b42d-2a21fe138473" />
<img width="439" height="434" alt="image" src="https://github.com/user-attachments/assets/f2aca3d8-61f6-4e34-a390-b67c549b82df" />

Explicación:
El uniform time cambia el color dinámicamente y offset cambia la posición del triángulo. El VBO no cambia, solo los valores enviados al shader.

Justificación:
Esto es posible porque los uniforms modifican el comportamiento del shader sin alterar los datos originales en memoria.

##### EVIDENCIA 4
<img width="1061" height="519" alt="image" src="https://github.com/user-attachments/assets/2659035e-cbc6-46b4-8957-1f1978e13284" />
Se cambio: 'float offsetX = sin(timeValue) * 0.5f;' por: 'float offsetX = 2.0f;' 

Esperado:
El triángulo debería salirse de la pantalla.

Resultado:
El triángulo desaparece.

Conclusión:
Los valores fuera del rango NDC (-1 a 1) no se renderizan en pantalla.

##### EVIDENCIA 5
<img width="1249" height="690" alt="image" src="https://github.com/user-attachments/assets/99a2497b-6744-4e41-8b49-479cae312eaf" />
Explicación

El offset se envía como una variable uniform al shader mediante la función glUniform2f. Este valor modifica la posición del triángulo en el vertex shader sin alterar los datos originales de los vértices.

Justificación

Se utiliza un uniform porque el offset es un valor global que se aplica a todos los vértices. Los atributos son para datos individuales por vértice, mientras que los uniforms permiten aplicar un mismo valor a todo el shader, lo que hace el proceso más eficiente y sencillo.

## Bitacora de reflexion

¿Qué parte del pipeline entiendes mejor ahora?

La parte que entiendo mejor es la relación entre los datos de los vértices (VBO/VAO) y el vertex shader. Ahora comprendo cómo los datos definidos en el CPU se envían a la GPU y cómo el shader los procesa para posicionar los objetos en pantalla.

¿Qué parte aún sientes más opaca o abstracta?

La parte más abstracta sigue siendo el funcionamiento interno del fragment shader y cómo la GPU procesa cada fragmento de forma paralela. Aunque entiendo su propósito, aún es difícil visualizar exactamente cómo se ejecuta internamente.

¿Qué error técnico te ayudó más a aprender en esta unidad?

El error que más me ayudó fue usar un offset fuera del rango permitido, lo que hizo que el triángulo desapareciera. Esto me permitió entender mejor el sistema de coordenadas NDC y cómo OpenGL solo renderiza objetos dentro del rango de -1 a 1.
<img width="469" height="864" alt="image" src="https://github.com/user-attachments/assets/17a876bc-fcb1-425d-880a-827e3740a35a" />
<img width="929" height="552" alt="image" src="https://github.com/user-attachments/assets/2b45f076-c0db-48ff-821f-0c638375ff9d" />



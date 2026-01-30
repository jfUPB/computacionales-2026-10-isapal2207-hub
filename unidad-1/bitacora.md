# Unidad 1

## Bitácora de proceso de aprendizaje
# ACTIVIDAD 1
Observaciones experimento 1

Observaciones experimento 2

¿Qué diferencia hay entre los datos almacenados en la memoria ROM y en la RAM?
La RAM es una memoria volátil, mucho más rápida que la ROM, que almacena información temporal de los programas y procesos en uso. Esta última se pierde cuando se apaga el equipo. La ROM, por su parte, es una memoria no volátil que guarda datos permanentes como instrucciones básicas para iniciar el dispositivo y firmware; su contenido rara vez cambia y no se pierde al apagar el equipo.

# ACTIVIDAD 2
-Identifica una instrucción que use la ALU y explica qué hace.
 Por ejemplo: D=D-A. Esta orden emplea la ALU para hacer una resta entre el valor del registro D y el contenido que apunta A, guardando el resultado en D. La ALU se usa para hacer este tipo de operaciones logicas.

-¿Para qué sirve el registro PC?
El contador de programa (PC) almacena la dirección de la instrucción que el procesador tiene que llevar a cabo. Por lo general, después de cada instrucción se aumenta automáticamente o cambia su valor cuando se produce un salto (por ejemplo, JMP, JGE)

-¿Cuál es la diferencia entre @i y @READKEYBOARD?
@READKEYBOARD se refiere a una etiqueta, osea, a una dirección de memoria que indica la ubicación de una instrucción del programa para poder saltar a esta. Por el contrario, @i es una variable almacenada en la memoria que puede modificarse mientras el programa se ejecuta.

-Describe qué se necesita para leer el teclado y mostrar información en la pantalla.
Para determinar si una tecla está siendo presionada, es necesario leer el valor de KBD; para cambiar los píxeles de la pantalla, se deben escribir valores en SCREEN. Tambien, se utilizan registros (A, D) y variables como "i" para navegar a través de las direcciones de memoria y determinar qué mostrar con base en la entrada del teclado.

-Identifica un bucle en el programa y explica su funcionamiento.
Se inicia el bucle principal en la etiqueta READKEYBOARD y finaliza con la instrucción @READKEYBOARD 0;JMP. Este bucle examina continuamente el teclado, cambia el valor de i y actualiza la pantalla en función de si se presiona o no una tecla.

-Identifica una condición en el programa y explica su funcionamiento.
La instrucción D;JNE es una condición luego de leer KBD: si el valor obtenido del teclado no es cero (hay una tecla oprimida), se salta a la etiqueta (KEYPRESSED); en caso contrario, sigue ejecutando las instrucciones que vienen.

# ACTIVIDAD 3
Escribe tu mismo el programa.
Simula paso a paso. Recuerda la metodología: predice, ejecuta, observa y reflexiona.


## Bitácora de aplicación 
# ACTIVIDAD 4 


## Bitácora de reflexión
# ACTIVIDAD 5
-Describe con tus palabras las tres fases del ciclo Fetch-Decode-Execute. ¿Qué rol juega el Program Counter (PC) en este ciclo?
El ciclo Fetch–Decode–Execute consiste en que primero la CPU busca (fetch) en memoria la instrucción indicada por el Program Counter (PC), luego decodifica (decode) qué tipo de instrucción es y qué debe hacer, y finalmente la ejecuta (execute) realizando cálculos, asignaciones o saltos. El PC guarda la dirección de la próxima instrucción a ejecutar y normalmente se incrementa en uno, excepto cuando ocurre un salto, en cuyo caso se carga con otra dirección.

-¿Cuál es la diferencia fundamental entre una instrucción-A (que empieza con @) y una instrucción-C (que involucra D, M, A, etc.) en el lenguaje ensamblador de Hack? Da un ejemplo de cada una.
La diferencia fundamental entre una instrucción A y una instrucción C en Hack es que la instrucción A (que empieza con @) solo sirve para cargar un valor o dirección en el registro A, mientras que la instrucción C se usa para realizar cálculos, mover datos entre registros y memoria, o ejecutar saltos. Un ejemplo de instrucción A es @10, y un ejemplo de instrucción C es D=M+1.
-Explica la función de los siguientes componentes del computador Hack: el registro D, el registro A y la ALU.

-¿Cómo se implementa un salto condicional en Hack? Describe un ejemplo (p. ej., saltar si el valor de D es mayor que cero).
El registro D se utiliza para almacenar datos temporales y resultados de cálculos, el registro A puede almacenar tanto valores como direcciones de memoria (y cuando se usa M, se accede a la memoria apuntada por A), y la ALU es la unidad que realiza todas las operaciones aritméticas y lógicas del computador Hack.

-¿Cómo se implementa un loop en el computador Hack? Describe un ejemplo (p. ej., un loop que decremente un valor hasta que llegue a cero).
Un salto condicional en Hack se implementa usando una instrucción C con un campo de salto (JGT, JEQ, JLT, etc.), que depende del resultado de la ALU. Por ejemplo, para saltar si el valor del registro D es mayor que cero se usa @ETIQUETA seguido de D;JGT.

-¿Cuál es la diferencia entre la instrucción D=M y la instrucción M=D?
La diferencia entre D=M y M=D es la dirección del flujo de datos: D=M copia el contenido de la memoria RAM ubicada en la dirección almacenada en A hacia el registro D, mientras que M=D copia el contenido del registro D hacia la posición de memoria RAM indicada por A.

-Describe brevemente qué se necesita para leer un valor del teclado (KBD) y para “pintar” un pixel en la pantalla (SCREEN).
Para leer un valor del teclado se accede a la dirección de memoria mapeada como KBD, cuyo contenido representa la tecla presionada, mientras que para pintar un píxel en la pantalla se escribe un valor en la región de memoria SCREEN, donde cada palabra controla 16 píxeles y escribir bits en 1 permite encenderlos.

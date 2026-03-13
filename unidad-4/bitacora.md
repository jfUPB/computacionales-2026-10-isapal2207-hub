# Unidad 4

## Bitácora de proceso de aprendizaje


## Bitácora de aplicación 
## ACTIVIDAD 3

## EVIDENCIA 1: inserción del primer nodo en una cola vacía (enqueue)
<img width="1215" height="511" alt="image" src="https://github.com/user-attachments/assets/c204e2ad-2a88-4001-abc1-5357778c2655" />
El breakpoint se estableció aquí porque queremos observar la condición del programa antes de cambiar la estructura de datos, o sea, ver cómo está la cola antes de agregar el primer nodo.


<img width="1216" height="515" alt="image" src="https://github.com/user-attachments/assets/23901e72-5f67-46ea-b431-9c60f666cf89" />
"front", "rear" y "size" son las variables que tienen mayor importancia aquí. Que poseen los valores que se indican a continuación:

front = NULL
rear = NULL
size = 0

Esto evidencia que la cola está vacía antes de insertarla, pues si el front y el rear están en NULL, no hay ningún nodo y size=0 muestra que la cola no tiene nodos en este momento.

<img width="1219" height="650" alt="image" src="https://github.com/user-attachments/assets/76488afa-bfa3-43cc-a4bf-6e45103fc585" />
Al inicializar 

	Node * newNode = new Node(x, y, radius, color, opacity);

Se genera un nodo "newNode" nuevo,se puede observar la dirección de memoria, lo que indica que se guardó correctamente en la memoria. Al expandirlo, también podemos ver sus atributos.
<img width="1711" height="135" alt="image" src="https://github.com/user-attachments/assets/e5df6a3b-a91c-4101-af32-ead44f762b01" />

<img width="1350" height="643" alt="image" src="https://github.com/user-attachments/assets/eb7c4b86-c54b-4da0-bd9d-46098e899713" />
Se puede ver en esta captura de pantalla que el front y el rear tienen la misma dirección de memoria, lo cual significa que los dos se dirigen hacia el mismo nodo.

## EVIDENCIA 2: mantenimiento del orden FIFO al insertar más nodos (enqueue)
<img width="1213" height="682" alt="image" src="https://github.com/user-attachments/assets/7931f4c4-11c6-49bb-a3fb-86b6755d79d6" />
se pone breakpoint en el mismo lugar 


Lo que hacemos es entrar en enqueue, pues ya estamos en la línea "void BrushQueue::enqueue(float x, float y, float radius, ofColor color, float opacity) {". Utilizamos la tecla F10 para avanzar por el código hasta que se ejecute la línea "front = rear = newNode;". Después, presionamos nuevamente F5 para acceder de nuevo a la función y ejecutamos la línea "Node* newNode = new Node(...)".
<img width="1715" height="95" alt="image" src="https://github.com/user-attachments/assets/2bf196d8-2943-4e59-8664-ca2adc219b68" />
Aquí podemos ver la dirección de memoria de front y rear, que señalan el nodo y el tamaño con valor 1, correspondiente al nodo presente en ese momento.

Después, presionamos F10 hasta ejecutar la línea "if (rear == nullptr)". Como ya existe un nodo y rear no es NULL, el código se irá a "else" y podremos observar lo siguiente:
<img width="1209" height="658" alt="image" src="https://github.com/user-attachments/assets/dabdf148-a182-4722-bff5-0b5e4aa66a72" />
Como se puede ver, el rear ya tiene direccion de memoria, por ende apunta a un nodo, y mas a vajo se puede ver que next es NULL, o sea, que el nodo que hay es el unico en la cola

Ahora para conectar los nodos, presionamos F10 sobre la linea "rear->next = newNode;" luego otra vez F10 sonbre "rear = newNode;" para actualizar el rear para que ahora el nuevo nodo sea el ultimo de la cola
<img width="1218" height="615" alt="image" src="https://github.com/user-attachments/assets/4af4e458-d857-4e55-a795-ad3114552f4a" />
Al observar front y rear, podemos ver que tienen direcciones de memoria distintas, lo cual indica que no apuntan al mismo lugar. Además, newNode tiene la misma dirección que rear, lo que significa que el nuevo nodo ya se encuentra al final de la cola.


## EVIDENCIA 3: comportamiento de eliminación y prevención de fugas (dequeue) 
<img width="1213" height="650" alt="image" src="https://github.com/user-attachments/assets/67a3266b-70b6-4f9c-947a-cb04e68b6deb" />
Se deja el breakpoint ahi para que el programa me permita generar nodos en la pantalla, asi esa linea identifique los nodos que va a eliminar
<img width="1203" height="671" alt="image" src="https://github.com/user-attachments/assets/5787c216-99bb-4320-8fec-55b1db118b2a" />
En la captura de pantalla podemos observar que las variables net, radius, x e y se encuentran en el front. Esto indica que allí está el nodo más antiguo. Además, observamos que el tamaño es 51, que es el maxSize de la cola; por lo tanto, estamos en condiciones de eliminar un nodo.
Ahora presionamos F10 nuevamente para ejecutar la línea "Node* temp = front;", con el fin de guardar en temp la dirección del nodo que vamos a eliminar.
<img width="1215" height="671" alt="image" src="https://github.com/user-attachments/assets/ef27ee40-4850-4c1a-9337-ce1023bb80af" />

Como se puede observar, ahora front y temp apuntan en la misma dirección; esto prueba que el nodo que va a eliminarse fue almacenado en temp.

Ahora llevamos a cabo la línea "front = front->next;". Esto se hace con el fin de que el valor "front" cambie por el próximo nodo al que se eliminará.
<img width="1209" height="654" alt="image" src="https://github.com/user-attachments/assets/46a75a59-7e9d-4f18-abee-6dd6a186b3c9" />
En esta captura de pantalla, observamos que front y front->next tienen direcciones distintas; eso indica que el nodo viejo está en front y el siguiente nodo se encuentra en front->next. Finalmente, observamos que size=51 y maxSize=50, lo que indica que la cola ha excedido su tamaño máximo y se ejecutó dequeue().

Con el fin de evitar memory leak y liberar la memoria del nodo eliminado, ahora ejecutamos la línea "delete temp;". Después, llevamos a cabo la instrucción "size--;" para que el tamaño disminuya y el size regrese a 50.
<img width="1207" height="683" alt="image" src="https://github.com/user-attachments/assets/df519eab-5098-45a4-afc0-4c782c4a980a" />
Este pantallazo muestra que el tamaño volvió a ser 50 después de realizar la línea "size--;", lo cual indica que el nodo fue eliminado correctamente.

## EVIDENCIA 4: control del tamaño máximo de la cola (maxSize) 
<img width="1210" height="645" alt="image" src="https://github.com/user-attachments/assets/b2cd8497-c91a-47c6-9d3a-5b74cbf6f943" />
Establecemos el siguiente punto de interrupción en ofApp.h, específicamente en la línea "if (size > maxSize)", dado que es aquí donde se comprueba si la cola ha sobrepasado el límite de su tamaño.
La primera evidencia también puede observarse en la misma captura de pantalla. En el Autos, se pueden observar dos variables: maxSize y size. La primera tiene un valor de 50 y la segunda uno de 51, lo que nos señala que la cola ha sobrepasado el máximo permitido.
<img width="1211" height="641" alt="image" src="https://github.com/user-attachments/assets/07231a31-7a59-4d9e-9886-91d3b9edc247" />
Después, pulsamos F10 para ubicar el cursor sobre la línea "dequeue();" y, a continuación, presionamos F11 para acceder a la función dequeue();. Lo que nos demuestra que cuando se excede el límite de la cola, se invoca automáticamente a la función de eliminar los nodos, que es dequeue().

## EVIDENCIA 5: recorrido de la cola sin destruirla (draw)
<img width="1203" height="653" alt="image" src="https://github.com/user-attachments/assets/9fc97a7d-73e7-4f3f-b8fe-73e5d3351e35" />
Se establece un punto de interrupción en esa línea porque es el lugar donde comienza la cola en el draw(). En este lugar, el sistema toma el puntero front que apunta al primer nodo y lo asigna a un puntero temporal llamado current. Este último será empleado para atravesar todos los nodos de la cola mediante un ciclo: ```cpp while(current != nullptr)```
<img width="1197" height="657" alt="image" src="https://github.com/user-attachments/assets/0e027326-17c3-4765-8b68-f6d40aa46845" />

Se puso el breakpoint en es alinea para observar la situación de la cola antes de comenzar el recorrido. La captura de pantalla evidencia que el recorrido comienza en el nodo inicial, debido a que front tiene una dirección de memoria válida. Además, contamos con el tamaño de valor 50, lo que verifica la existencia de nodos almacenados.
<img width="1213" height="632" alt="image" src="https://github.com/user-attachments/assets/90b898e6-1006-4c8f-a51d-63a0f09e1428" />

El pantallazo indica que draw() itera a través de la cola, nodo por nodo, mediante el puntero temporal current. Como puede observarse, current y front tienen la misma dirección de memoria, lo que indica que el recorrido empieza con el primer nodo. Y con el ciclo "while(current != nullptr)", podemos atravesar cada nodo de la cola.
<img width="1202" height="629" alt="image" src="https://github.com/user-attachments/assets/713659ad-39a6-4122-b8ba-135d0c5f2a1c" />
En esta captura de pantalla, hasta la línea "current = current->next;" se ejecuta y todavía estamos dentro del ciclo while, lo que indica que el programa está atravesando cada nodo de la cola. En el panel de Autos, se puede ver que current apunta a un nodo y que current->next apunta al siguiente; esto señala que el recorrido es efectivo.

Además, se puede observar que los punteros front y rear permanecen inalterables, con las mismas direcciones. Esto evidencia que el recorrido opera sin eliminar o destruir ningún nodo.

## EVIDENCIA 6: limpieza total de la memoria (clear)
Establecemos un breakpoint en la línea "while (front != nullptr) {" para que el programa se detenga en ese lugar cuando tratemos de eliminar la cola con c y creamos la cola en pantalla.
<img width="1211" height="362" alt="image" src="https://github.com/user-attachments/assets/9ef2a95a-56b4-425e-83cb-65c8f8fe292c" />
En la captura de pantalla se pueden observar las direcciones de memoria válidas de front y rear, así como un valor válido para size; esto evidencia que existen nodos en la cola antes de ser eliminados.

Después, presionamos F10 para posicionarnos sobre la línea "dequeue()", y luego presionamos F11 para ingresar a la función que contiene toda la lógica de eliminación. Finalmente, volvemos a presionar F10.
<img width="1212" height="604" alt="image" src="https://github.com/user-attachments/assets/e20061ff-a605-4081-8fea-da8ada30ee0e" />


Ya podemos ver que tanto front como rear tienen una dirección de memoria válida y que size es igual a 50, lo cual indica que se ha creado un trazo.
Después, presionamos hasta que se ejecuten las dos líneas siguientes:
	
	delete temp;
	size--;

Estas lineas eliminan el nodo y reducen el tamaño de la cola 
<img width="1213" height="634" alt="image" src="https://github.com/user-attachments/assets/4ff52d8a-9bf7-47f7-b5f6-696c21ab2564" />

Se puede observar que la dirección de front es distinta y que el size se redujo a 49, lo que indica que se eliminó el nodo y la memoria fue liberada.
## Bitácora de reflexión

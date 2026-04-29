# Unidad 3

## Bitácora de proceso de aprendizaje


## Bitácora de aplicación 
## CUAL FUE EL ERROR


ERROR 1: ambos personajes comparten la misma memoria
Se hace un mal duplicado, la línea: El puntero de la dirección de estadísticas es replicado tal cual en lugar del valor de cada estadística. Esto ocurre porque el personaje copiaHeroe = heroe; realiza una duplicación de la información que contiene. Sucede porque en la pila permanecen dos objetos diferentes: "heroe" y "copiaHeroe", pero no se generan nuevas estadísticas en el montón, sino que ambos hacen referencia al mismo bloque. Si alteras las estadísticas en uno, se modifican en el otro. Además, si procuras corregir la fuga añadiendo un destructor con delete[], los dos objetos intentarían liberar el mismo arreglo.

ERROR 2: Se produce una fuga de memoria, se reserva la memoria pero no se libera nunca. La clase del constructor ejecuta: estadisticas = new int[3]; sin embargo, no se libera cuando el héroe muere. Esto sucede porque el héroe es un objeto local que reside en el stack y las estadísticas son un arreglo que se encuentra en el heap. Al salir de simularEncuentro, el héroe queda fuera del stack, pero como nadie liberó el arreglo del heap, este permanece activo. Como resultado, el programa utiliza cada vez más RAM. Lo que ocurre en un juego es que, tras muchas sesiones, se vuelve lento o incluso se bloquea debido a la falta de memoria.

2. DEPURADOR

Error 1
<img width="1480" height="764" alt="image" src="https://github.com/user-attachments/assets/3a0a4ef5-99c5-4f8b-8e15-725717772e57" />



Error 2
<img width="1277" height="801" alt="image" src="https://github.com/user-attachments/assets/e3e58dea-5860-4c19-aac3-14c8097a07f3" />

3. INDUCE FALLOS
Error 2: poniendo esta modificacion
<img width="626" height="108" alt="image" src="https://github.com/user-attachments/assets/500f5965-db62-414b-bdd6-61a75cb3a7cc" />

ANTES
<img width="748" height="598" alt="image" src="https://github.com/user-attachments/assets/3a6274f5-e4a2-4397-979c-8fc0de99bcba" />

DESPUES (30 seg)
<img width="834" height="597" alt="image" src="https://github.com/user-attachments/assets/b85b8973-f324-4829-9256-388c8a4c63a9" />

Error 1: poniendo esto
<img width="690" height="171" alt="image" src="https://github.com/user-attachments/assets/047f3f0a-d6b0-4eba-866a-97dc11ea414b" />

demostracion 
<img width="663" height="274" alt="image" src="https://github.com/user-attachments/assets/12277652-6c61-4190-89d2-2d016b0ff1cd" />




4. Solución y refactorización (síntesis y creación):

ERROR 2: Reemplazar 'int* estadisticas' por 'std::array<int, 3> estadisticas;' Asi se elimina la gestion manuel y no hay que corregir la clase. Asi, se almacenan las estadisticas directamente dentro del objeto, ya nonse usa el 'new' y 'delete', permite que la copia del objeto sea segura

modificacion error 2
	
		#include <iostream>
		#include <string>
		
	class Personaje {
	public:
	    std::string nombre;
	    std::array<int, 3> estadisticas;
	
	    Personaje(std::string n, int vida, int ataque, int defensa) {
	        nombre = n;
	        estadisticas = new int[3];
	        estadisticas[0] = vida;
	        estadisticas[1] = ataque;
	        estadisticas[2] = defensa;
	        std::cout << "Constructor: nace " << nombre << std::endl;
	    }
	
	    void imprimir() {
	        std::cout << "Personaje " << nombre
	            << " [Vida: " << estadisticas[0]
	            << ", ATK: " << estadisticas[1]
	            << ", DEF: " << estadisticas[2]
	            << "]" << std::endl;
	    }
	};
	
	void simularEncuentro() {
	    std::cout << "\n--- Iniciando encuentro ---" << std::endl;
	    Personaje heroe("Aragorn", 100, 20, 15);
	
	    Personaje copiaHeroe = heroe;
	    copiaHeroe.nombre = "Copia de Aragorn";
	    copiaHeroe.estadisticas[0] = 561532; // vida
	    heroe.imprimir();
	    copiaHeroe.imprimir();
	
	    std::cout << "Saliendo del encuentro..." << std::endl;
	}
	
	int main() {
	    for (int i = 0; i < 1000000; i++) simularEncuentro();
	    std::cout << "\nSimulación terminada." << std::endl;
	    return 0;
	}

ERROR 1: Se quita el puntero dinamico para poner un contenedor seguro: 'std::array<int, 3> estadisticas;'
ahora cuando se hace la copia del heroe, se copia el nombre, los 3 enteros de estadisticas y cada objeto tiene su propio almacenamiento independiente

CODIGO
	
		#include <iostream>
		#include <string>
		#include <array>
		
	class Personaje {
	public:
	    std::string nombre;
	    std::array<int, 3> estadisticas; // [vida, ataque, defensa]
	
	    // Constructor (sin new, sin delete)
	    Personaje(const std::string& n, int vida, int ataque, int defensa)
	        : nombre(n), estadisticas{ vida, ataque, defensa } {
	        std::cout << "Constructor: nace " << nombre << std::endl;
	    }
	
	    // (No hace falta destructor: no hay memoria dinámica manual)
	    // (No hace falta constructor de copia ni operador de asignación:
	    //  std::string y std::array copian correctamente)
	
	    void imprimir() const {
	        std::cout << "Personaje " << nombre
	            << " [Vida: " << estadisticas[0]
	            << ", ATK: " << estadisticas[1]
	            << ", DEF: " << estadisticas[2]
	            << "]" << std::endl;
	    }
	};
	
	void simularEncuentro() {
	    std::cout << "\n--- Iniciando encuentro ---" << std::endl;
	
	    Personaje heroe("Aragorn", 100, 20, 15);
	    heroe.imprimir();
	
	    // Ahora la copia es segura (copia real de estadisticas)
	    Personaje copiaHeroe = heroe;
	    copiaHeroe.nombre = "Copia de Aragorn";
	
	    copiaHeroe.imprimir();
	
	    std::cout << "Saliendo del encuentro..." << std::endl;
	}
	
	int main() {
	    simularEncuentro();
	    std::cout << "\nSimulación terminada." << std::endl;
	    return 0;
	}

5. Justificación de la Solución
   Cambio 1: Reemplazar int* estadisticas por std::array<int, 3> estadisticas
   En int*, el arreglo se generaba en el heap mediante new, pero al no liberarse, el bloque seguía ocupado incluso tras destruirse el objeto. Con std::array, las estadísticas no se almacenan en el heap, sino que se guardan directamente en el objeto. Por ende, no se asigna memoria dinámica y no hay nada para liberar manualmente. Resultado: no se producen pérdidas de memoria, aun cuando se generan miles de personajes. ¿Por qué esto soluciona la copia superficial? Cuando las estadísticas eran un puntero, al copiar un objeto solamente se copiaba la dirección, no los datos. Cuando se copia un Personaje con std::array, se replica el contenido real del arreglo. Resultado: héroe y copiaHéroe tienen estadísticas separadas, lo que evita errores en los que uno cambia mientras el otro también lo hace.

	 Cambio 2:Eliminar new y delete de la clase
 Los manuales new/delete suelen ser una fuente común de errores, como fugas, liberación doble y punteros colgantes. Si se eliminan, el ciclo de vida de la memoria es gestionado automáticamente por objetos seguros de la STL (std::string, std::array). Esto previene errores inesperados debido a la corrupción del heap.

## Bitácora de reflexión

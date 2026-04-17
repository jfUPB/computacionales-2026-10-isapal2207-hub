# Unidad 6

## Bitácora de proceso de aprendizaje


## Bitácora de aplicación 

## CODIGOS

ofApp.h:
	
	#pragma once
	
	#include "ofMain.h"
	#include <string>
	#include <vector>
	
	class Observer {
	public:
		virtual ~Observer() = default;
		virtual void onNotify(const std::string & event) = 0;
	};
	
	class Subject {
	public:
		void addObserver(Observer * observer);
		void removeObserver(Observer * observer);
	
	protected:
		void notify(const std::string & event);
	
	private:
		std::vector<Observer *> observers;
	};
	
	class Particle;
	
	class State {
	public:
		virtual ~State() = default;
		virtual void update(Particle * particle) = 0;
		virtual void onEnter(Particle * particle) { }
		virtual void onExit(Particle * particle) { }
	};
	
	class Particle : public Observer {
	public:
		Particle();
		~Particle() override;
	
		Particle(const Particle &) = delete;
		Particle & operator=(const Particle &) = delete;
	
		void update();
		void draw();
		void onNotify(const std::string & event) override;
	
		void setState(State * newState);
	
		ofVec2f position;
		ofVec2f velocity;
		float size;
		ofColor color;
	
	private:
		void keepInsideWindow();
		State * state;
	};
	
	class NormalState : public State {
	public:
		void update(Particle * particle) override;
		void onEnter(Particle * particle) override;
	};
	
	class AttractState : public State {
	public:
		void update(Particle * particle) override;
	};
	
	class RepelState : public State {
	public:
		void update(Particle * particle) override;
	};
	
	class StopState : public State {
	public:
		void update(Particle * particle) override;
	};
	
	// NUEVO ESTADO
	class CrazyState : public State {
	public:
		void update(Particle * particle) override;
		void onEnter(Particle * particle) override;
	};
	
	class ParticleFactory {
	public:
		static Particle * createParticle(const std::string & type);
	};
	
	class ofApp : public ofBaseApp, public Subject {
	public:
		~ofApp() override;
		void setup() override;
		void update() override;
		void draw() override;
		void keyPressed(int key) override;
	
	private:
		std::vector<Particle *> particles;
	};


ofApp.cpp:

	#include "ofApp.h"
	#include <algorithm>
	
	void Subject::addObserver(Observer * observer) {
		if (!observer) return;
		if (std::find(observers.begin(), observers.end(), observer) == observers.end()) {
			observers.push_back(observer);
		}
	}
	
	void Subject::removeObserver(Observer * observer) {
		if (!observer) return;
		observers.erase(std::remove(observers.begin(), observers.end(), observer), observers.end());
	}
	
	void Subject::notify(const std::string & event) {
		for (Observer * observer : observers) {
			observer->onNotify(event);
		}
	}
	
	Particle::Particle()
		: state(nullptr) {
		position = ofVec2f(ofRandomWidth(), ofRandomHeight());
		velocity = ofVec2f(ofRandom(-0.5f, 0.5f), ofRandom(-0.5f, 0.5f));
		size = ofRandom(2.0f, 5.0f);
		color = ofColor(255);
	
		state = new NormalState();
		state->onEnter(this);
	}
	
	Particle::~Particle() {
		if (state) {
			state->onExit(this);
			delete state;
			state = nullptr;
		}
	}
	
	void Particle::setState(State * newState) {
		if (state) {
			state->onExit(this);
			delete state;
		}
		state = newState;
		if (state) {
			state->onEnter(this);
		}
	}
	
	void Particle::update() {
		if (state) {
			state->update(this);
		}
		keepInsideWindow();
	}
	
	void Particle::draw() {
		ofPushStyle();
		ofSetColor(color);
		ofDrawCircle(position, size);
		ofPopStyle();
	}
	
	void Particle::onNotify(const std::string & event) {
		if (event == "attract") {
			setState(new AttractState());
		} else if (event == "repel") {
			setState(new RepelState());
		} else if (event == "stop") {
			setState(new StopState());
		} else if (event == "normal") {
			setState(new NormalState());
		}
		//NUEVO EVENTO
		else if (event == "crazy") {
			setState(new CrazyState());
		}
	}
	
	void Particle::keepInsideWindow() {
		const float W = static_cast<float>(ofGetWidth());
		const float H = static_cast<float>(ofGetHeight());
	
		if (position.x < 0.0f) {
			position.x = 0.0f;
			velocity.x *= -1.0f;
		} else if (position.x > W) {
			position.x = W;
			velocity.x *= -1.0f;
		}
		if (position.y < 0.0f) {
			position.y = 0.0f;
			velocity.y *= -1.0f;
		} else if (position.y > H) {
			position.y = H;
			velocity.y *= -1.0f;
		}
	}
	
	void NormalState::onEnter(Particle * particle) {
		particle->velocity.set(ofRandom(-0.5f, 0.5f), ofRandom(-0.5f, 0.5f));
	}
	
	void NormalState::update(Particle * particle) {
		particle->position += particle->velocity;
	}
	
	static void steer(Particle * particle, const ofVec2f & toward, float accel, float vmax, float posScale) {
		ofVec2f dir = toward - particle->position;
		float len = dir.length();
		if (len > 1e-6f) {
			dir /= len;
			particle->velocity += dir * accel;
		}
		particle->velocity.limit(vmax);
		particle->position += particle->velocity * posScale;
	}
	
	void AttractState::update(Particle * particle) {
		ofVec2f mouse(ofGetMouseX(), ofGetMouseY());
		steer(particle, mouse, 0.05f, 3.0f, 0.2f);
	}
	
	void RepelState::update(Particle * particle) {
		ofVec2f mouse(ofGetMouseX(), ofGetMouseY());
		ofVec2f away = particle->position - mouse;
		float len = away.length();
		if (len > 1e-6f) {
			away /= len;
			particle->velocity += away * 0.05f;
		}
		particle->velocity.limit(3.0f);
		particle->position += particle->velocity * 0.2f;
	}
	
	void StopState::update(Particle * particle) {
		particle->velocity *= 0.80f;
		if (particle->velocity.lengthSquared() < 1e-4f) {
			particle->velocity.set(0.0f, 0.0f);
		}
		particle->position += particle->velocity;
	}
	
	// NUEVO ESTADO
	
	void CrazyState::onEnter(Particle * particle) {
		particle->color = ofColor(255, 0, 255);
	}
	
	void CrazyState::update(Particle * particle) {
		particle->velocity += ofVec2f(ofRandom(-1, 1), ofRandom(-1, 1));
		particle->velocity.limit(5.0f);
		particle->position += particle->velocity;
	}
	
	Particle * ParticleFactory::createParticle(const std::string & type) {
		Particle * particle = new Particle();
		if (type == "star") {
			particle->size = ofRandom(2.0f, 4.0f);
			particle->color = ofColor(255, 0, 0);
		} else if (type == "shooting_star") {
			particle->size = ofRandom(3.0f, 6.0f);
			particle->color = ofColor(0, 255, 0);
			particle->velocity *= 3.0f;
		} else if (type == "planet") {
			particle->size = ofRandom(5.0f, 8.0f);
			particle->color = ofColor(0, 0, 255);
		}
		// NUEVO TIPO
		else if (type == "comet") {
			particle->size = ofRandom(4.0f, 7.0f);
			particle->color = ofColor(255, 255, 0);
			particle->velocity *= 2.0f;
		}
		return particle;
	}
	
	ofApp::~ofApp() {
		for (Particle * p : particles) {
			removeObserver(p);
			delete p;
		}
		particles.clear();
	}
	
	void ofApp::setup() {
		ofBackground(0);
		particles.reserve(100 + 5 + 10 + 5);
	
		for (int i = 0; i < 100; ++i) {
			Particle * p = ParticleFactory::createParticle("star");
			particles.push_back(p);
			addObserver(p);
		}
		for (int i = 0; i < 5; ++i) {
			Particle * p = ParticleFactory::createParticle("shooting_star");
			particles.push_back(p);
			addObserver(p);
		}
		for (int i = 0; i < 10; ++i) {
			Particle * p = ParticleFactory::createParticle("planet");
			particles.push_back(p);
			addObserver(p);
		}
		// NUEVAS PARTICULAS
		for (int i = 0; i < 5; ++i) {
			Particle * p = ParticleFactory::createParticle("comet");
			particles.push_back(p);
			addObserver(p);
		}
	}
	
	void ofApp::update() {
		for (Particle * p : particles) {
			p->update();
		}
	}
	
	void ofApp::draw() {
		for (Particle * p : particles) {
			p->draw();
		}
	}
	
	void ofApp::keyPressed(int key) {
		switch (key) {
		case 's':
			notify("stop");
			break;
		case 'a':
			notify("attract");
			break;
		case 'r':
			notify("repel");
			break;
		case 'n':
			notify("normal");
			break;
			// NUEVA TECLA
		case 'c':
			notify("crazy");
			break;
		default:
			break;
		}
	}

##EVIDENCIAS 

Evidencia 1
<img width="1025" height="561" alt="image" src="https://github.com/user-attachments/assets/f06e7ac0-9364-4b06-8d9b-4157cd5714dc" />
Puse un breakpoint en la parte donde se crean las partículas tipo "comet". Al ejecutar el programa, se observa que sí entra en esa condición. En el depurador se puede ver que la partícula tiene un tamaño dentro del rango definido y una velocidad asignada. Además, se ve el código donde se le asigna el color amarillo y se aumenta su velocidad, lo que demuestra que la factory está creando correctamente este nuevo tipo de partícula.

Evidencia 2
<img width="956" height="639" alt="image" src="https://github.com/user-attachments/assets/7e32c7c3-dad0-4b1d-948e-d496e6521f6c" />
<img width="957" height="722" alt="image" src="https://github.com/user-attachments/assets/3d9876b6-5a62-4f94-bc50-fcfcd7e979c5" />
Se establece un breakpoint en el método onNotify, específicamente en la línea donde se ejecuta setState(new CrazyState()), ya que este es el punto donde ocurre el cambio de estado de la partícula. Esto permite observar en el depurador el momento exacto en el que el puntero state es actualizado.

En el depurador se inspecciona el puntero state y se observa que cuando la partícula cambia de NormalState a CrazyState, también cambia la dirección del _vfptr. Esto indica que cada estado posee su propia tabla de funciones virtuales (_vtable).

Al expandir esta tabla, se pueden ver métodos como update y onEnter, los cuales pertenecen específicamente a cada estado. Esto demuestra que el programa utiliza polimorfismo dinámico, permitiendo que cada partícula ejecute un comportamiento distinto dependiendo de su estado actual.

Evidencia 3
<img width="921" height="486" alt="image" src="https://github.com/user-attachments/assets/c736df50-5f99-4e70-b21e-bbdb33ecae0c" />
Se establece un breakpoint en el método keyPressed para verificar la entrada del usuario. Al presionar la tecla 'c', se observa en el depurador que el valor de la variable key es 99, lo cual corresponde a dicha tecla. Esto confirma que el evento se activa correctamente a partir de la interacción del usuario.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/e8d38cd7-f142-4b0e-b8ab-f3cccff2682a" />
Se establece un breakpoint en el método notify, ya que este es el punto clave donde el evento generado en keyPressed se distribuye a todos los observadores. Colocar el breakpoint en este lugar permite verificar que el evento se está transmitiendo correctamente antes de que cada partícula lo procese. En el método notify, se observa que el evento generado corresponde a "crazy", el cual es enviado a todos los observadores mediante la llamada a onNotify. A pesar de las limitaciones del depurador para mostrar correctamente el contenido del std::string, el flujo de ejecución evidencia que el evento se transmite correctamente dentro del sistema.

<img width="1238" height="679" alt="image" src="https://github.com/user-attachments/assets/7a5d3ec6-81fe-4946-8398-d1ff5a8b32c2" />
Se establece un breakpoint en la llamada a setState(new CrazyState()), ya que este es el punto donde ocurre el cambio de estado de la partícula. En el depurador se observa que, después de ejecutar esta instrucción, el puntero state corresponde a CrazyState. Esto confirma que, tras recibir el evento "crazy" a través del patrón Observer, la partícula cambia correctamente su estado, completando la cadena de ejecución desde keyPressed hasta la transición de estado.

Evidencia 4
<img width="1232" height="568" alt="image" src="https://github.com/user-attachments/assets/01c63aff-1a95-4d04-b462-6c2807f7471b" />
Coloqué un breakpoint en el método CrazyState::update porque ahí es donde se ejecuta el comportamiento propio de este estado. Al revisar el depurador, pude ver que la velocidad de la partícula cambia con valores aleatorios en cada actualización, lo que genera un movimiento caótico y distinto a los demás estados.

Decidí que CrazyState heredara directamente de la clase base State para poder definir un comportamiento completamente independiente. Esto me permite tener un diseño más flexible y con menor acoplamiento, ya que puedo agregar nuevos estados sin tener que modificar los que ya existen.
## Bitácora de reflexión

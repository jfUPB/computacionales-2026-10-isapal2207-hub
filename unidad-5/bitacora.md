# Unidad 5
## Bitácora de proceso de aprendizaje


## Bitácora de aplicación 
## FASE 1 
CODIGOS

ofApp.cpp

		#include "ofApp.h"
	
		// --------------------------------------------------------------
		void
		ofApp::setup() {
		ofSetFrameRate(60);
		ofBackground(0);
	}
	
	// --------------------------------------------------------------
	void ofApp::update() {
		float dt = ofGetLastFrameTime();
	
		for (int i = 0; i < particles.size(); i++) {
			particles[i]->update(dt);
		}
	
		for (int i = particles.size() - 1; i >= 0; i--) {
			if (particles[i]->shouldExplode()) {
				int explosionType = (int)ofRandom(3);
				int numParticles = (int)ofRandom(20, 30);
				for (int j = 0; j < numParticles; j++) {
					if (explosionType == 0) {
						particles.push_back(new CircularExplosion(
							particles[i]->getPosition(), particles[i]->getColor()));
					} else if (explosionType == 1) {
						particles.push_back(new RandomExplosion(
							particles[i]->getPosition(), particles[i]->getColor()));
					} else {
						particles.push_back(new StarExplosion(
							particles[i]->getPosition(), particles[i]->getColor()));
					}
				}
				delete particles[i];
				particles.erase(particles.begin() + i);
			} else if (particles[i]->isDead()) {
				delete particles[i];
				particles.erase(particles.begin() + i);
			}
		}
	}
	
	// --------------------------------------------------------------
	void ofApp::draw() {
		for (int i = 0; i < particles.size(); i++) {
			particles[i]->draw();
		}
	}
	
	// --------------------------------------------------------------
	void ofApp::createRisingParticle() {
		float minX = ofGetWidth() * 0.35f;
		float maxX = ofGetWidth() * 0.65f;
		float spawnX = ofRandom(minX, maxX);
		glm::vec2 pos(spawnX, ofGetHeight());
		glm::vec2 target(ofGetWidth() / 2.0f + ofRandom(-300, 300),
			ofGetHeight() * 0.10f + ofRandom(-30, 30));
		glm::vec2 direction = glm::normalize(target - pos);
		glm::vec2 vel = direction * ofRandom(250, 350);
		ofColor col;
		col.setHsb(ofRandom(255), 220, 255);
		float lifetime = ofRandom(1.5f, 3.5f);
		particles.push_back(new RisingParticle(pos, vel, col, lifetime));
	}
	
	// --------------------------------------------------------------
	void ofApp::mousePressed(int x, int y, int button) {
		createRisingParticle();
	
		
		ofColor col;
		col.setHsb(ofRandom(255), 200, 255);
		particles.push_back(new SpiralParticle(glm::vec2(x, y), col));
	
		
		for (int i = 0; i < 10; i++) {
			glm::vec2 pos(x, y);
			glm::vec2 vel(ofRandom(-50, 50), ofRandom(50, 150));
	
			ofColor col2;
			col2.setHsb(ofRandom(255), 200, 255);
	
			particles.push_back(new FallingParticle(pos, vel, col2, 2.5f));
		}
	}
	
	// --------------------------------------------------------------
	void ofApp::keyPressed(int key) {
		if (key == ' ') {
			for (int i = 0; i < 1000; i++) {
				createRisingParticle();
			}
		}
		if (key == 's') {
			ofSaveScreen("screenshot_" + ofToString(ofGetFrameNum()) + ".png");
		}
	}
	
	// --------------------------------------------------------------
	ofApp::~ofApp() {
		for (int i = 0; i < particles.size(); i++) {
			delete particles[i];
		}
		particles.clear();
	}




ofApp.h

	#pragma once
	#include "ofMain.h"
	#include <vector>
	
	// -------------------------------------------------
	// Clase base abstracta: Particle
	// -------------------------------------------------
	class Particle {
	public:
		virtual ~Particle() { }
		virtual void update(float dt) = 0;
		virtual void draw() = 0;
		virtual bool isDead() const = 0;
		virtual bool shouldExplode() const { return false; }
		virtual glm::vec2 getPosition() const { return glm::vec2(0, 0); }
		virtual ofColor getColor() const { return ofColor(255); }
	};
	
	// -------------------------------------------------
	// RisingParticle
	// -------------------------------------------------
	class RisingParticle : public Particle {
	protected:
		glm::vec2 position;
		glm::vec2 velocity;
		ofColor color;
		float lifetime;
		float age;
		bool exploded;
	
	public:
		RisingParticle(const glm::vec2 & pos, const glm::vec2 & vel,
			const ofColor & col, float life)
			: position(pos)
			, velocity(vel)
			, color(col)
			, lifetime(life)
			, age(0)
			, exploded(false) { }
	
		void update(float dt) override {
			position += velocity * dt;
			age += dt;
			velocity.y += 9.8f * dt * 8;
			float explosionThreshold = ofGetHeight() * 0.15f + ofRandom(-30, 30);
			if (position.y <= explosionThreshold || age >= lifetime) {
				exploded = true;
			}
		}
	
		void draw() override {
			ofSetColor(color);
			ofDrawCircle(position, 10);
		}
	
		bool isDead() const override { return exploded; }
		bool shouldExplode() const override { return exploded; }
		glm::vec2 getPosition() const override { return position; }
		ofColor getColor() const override { return color; }
	};
	
	// -------------------------------------------------
	// NUEVO: FallingParticle 
	// -------------------------------------------------
	class FallingParticle : public Particle {
		glm::vec2 position;
		glm::vec2 velocity;
		ofColor color;
		float lifetime;
		float age;
		bool exploded;
	
	public:
		FallingParticle(const glm::vec2 & pos, const glm::vec2 & vel,
			const ofColor & col, float life)
			: position(pos)
			, velocity(vel)
			, color(col)
			, lifetime(life)
			, age(0)
			, exploded(false) { }
	
		void update(float dt) override {
			position += velocity * dt;
			velocity.y += 9.8f * dt * 5;
			age += dt;
	
			if (position.y >= ofGetHeight() * 0.8f || age >= lifetime) {
				exploded = true;
			}
		}
	
		void draw() override {
			ofSetColor(color);
			ofDrawCircle(position, 8);
		}
	
		bool isDead() const override { return exploded; }
		bool shouldExplode() const override { return exploded; }
		glm::vec2 getPosition() const override { return position; }
		ofColor getColor() const override { return color; }
	};
	
	// -------------------------------------------------
	// NUEVO: SpiralParticle
	// -------------------------------------------------
	class SpiralParticle : public Particle {
		glm::vec2 center;
		float angle;
		float radius;
		float speed;
		float age;
		float lifetime;
		ofColor color;
		bool exploded;
	
	public:
		SpiralParticle(const glm::vec2 & c, const ofColor & col)
			: center(c)
			, angle(0)
			, radius(0)
			, speed(5)
			, age(0)
			, lifetime(2.5f)
			, color(col)
			, exploded(false) { }
	
		void update(float dt) override {
			angle += speed * dt;
			radius += 50 * dt;
			age += dt;
	
			if (age >= lifetime) exploded = true;
		}
	
		void draw() override {
			glm::vec2 pos = center + glm::vec2(cos(angle), sin(angle)) * radius;
			ofSetColor(color);
			ofDrawCircle(pos, 6);
		}
	
		bool isDead() const override { return exploded; }
		bool shouldExplode() const override { return exploded; }
		glm::vec2 getPosition() const override { return center; }
		ofColor getColor() const override { return color; }
	};
	
	// -------------------------------------------------
	// ExplosionParticle (igual)
	// -------------------------------------------------
	class ExplosionParticle : public Particle {
	protected:
		glm::vec2 position;
		glm::vec2 velocity;
		ofColor color;
		float age;
		float lifetime;
		float size;
	
	public:
		ExplosionParticle(const glm::vec2 & pos, const glm::vec2 & vel,
			const ofColor & col, float life, float sz)
			: position(pos)
			, velocity(vel)
			, color(col)
			, age(0)
			, lifetime(life)
			, size(sz) { }
	
		void update(float dt) override {
			position += velocity * dt;
			age += dt;
			float alpha = ofMap(age, 0, lifetime, 255, 0, true);
			color.a = alpha;
		}
	
		bool isDead() const override { return age >= lifetime; }
	};
	
	// -------------------------------------------------
	// Explosiones originales
	// -------------------------------------------------
	class CircularExplosion : public ExplosionParticle {
	public:
		CircularExplosion(const glm::vec2 & pos, const ofColor & col)
			: ExplosionParticle(pos, glm::vec2(0, 0), col, 1.2f, ofRandom(16, 32)) {
			float angle = ofRandom(0, TWO_PI);
			float speed = ofRandom(80, 200);
			velocity = glm::vec2(cos(angle), sin(angle)) * speed;
		}
	
		void draw() override {
			ofSetColor(color);
			ofDrawCircle(position, size);
		}
	};
	
	class RandomExplosion : public ExplosionParticle {
	public:
		RandomExplosion(const glm::vec2 & pos, const ofColor & col)
			: ExplosionParticle(pos, glm::vec2(0, 0), col, 1.5f, ofRandom(16, 32)) {
			velocity = glm::vec2(ofRandom(-200, 200), ofRandom(-200, 200));
		}
	
		void draw() override {
			ofSetColor(color);
			ofDrawRectangle(position.x, position.y, size, size);
		}
	};
	
	class StarExplosion : public ExplosionParticle {
	public:
		StarExplosion(const glm::vec2 & pos, const ofColor & col)
			: ExplosionParticle(pos, glm::vec2(0, 0), col, 1.3f, ofRandom(20, 40)) {
			float angle = ofRandom(0, TWO_PI);
			float speed = ofRandom(90, 180);
			velocity = glm::vec2(cos(angle), sin(angle)) * speed;
		}
	
		void draw() override {
			ofSetColor(color);
			int rays = 5;
			float outerRadius = size;
			float innerRadius = size * 0.5f;
			ofPushMatrix();
			ofTranslate(position);
			for (int i = 0; i < rays; i++) {
				float theta = ofMap(i, 0, rays, 0, TWO_PI);
				float xOuter = cos(theta) * outerRadius;
				float yOuter = sin(theta) * outerRadius;
				float xInner = cos(theta + PI / rays) * innerRadius;
				float yInner = sin(theta + PI / rays) * innerRadius;
				ofDrawLine(0, 0, xOuter, yOuter);
				ofDrawLine(xOuter, yOuter, xInner, yInner);
			}
			ofPopMatrix();
		}
	};
	
	// -------------------------------------------------
	// NUEVO: RingExplosion
	// -------------------------------------------------
	class RingExplosion : public ExplosionParticle {
	public:
		RingExplosion(const glm::vec2 & pos, const ofColor & col)
			: ExplosionParticle(pos, glm::vec2(0, 0), col, 1.4f, 10) {
	
			float angle = ofRandom(0, TWO_PI);
			float speed = 150;
			velocity = glm::vec2(cos(angle), sin(angle)) * speed;
		}
	
		void draw() override {
			ofSetColor(color);
			ofDrawCircle(position, size);
		}
	};
	
	// -------------------------------------------------
	class ofApp : public ofBaseApp {
	public:
		void setup();
		void update();
		void draw();
		void mousePressed(int x, int y, int button);
		void keyPressed(int key);
	
		std::vector<Particle *> particles;
		~ofApp();
	
	private:
		void createRisingParticle();
	};



## FASE 2

EVIDENCIA 1
<img width="1838" height="795" alt="image" src="https://github.com/user-attachments/assets/59882e3b-f051-4e44-a382-7ab7b3195b74" />
Elección del breakpoint:
Puse el breakpoint en el update() cuando se está recorriendo el vector particles, específicamente en una partícula de tipo FallingParticle, porque ahí puedo ver cómo está estructurado el objeto en memoria y su jerarquía.

Explicación:
En el depurador se puede ver que el objeto tiene los atributos heredados de la clase Particle y también los propios de FallingParticle.
Por ejemplo:

De Particle: funciones virtuales
De FallingParticle: position, velocity, color, lifetime, age, exploded

Esto muestra cómo el objeto contiene información de su clase base y su subclase.

Justificación:
Esta captura demuestra que entiendo cómo funciona la herencia, ya que el objeto no es solo de FallingParticle, sino que incluye toda la estructura definida en Particle, lo que permite reutilizar código y mantener una jerarquía organizada.


EVIDENCIA 2
<img width="945" height="229" alt="image" src="https://github.com/user-attachments/assets/5551627c-4505-4c77-8aeb-444788bdaf39" />
Elección del BREAKPOINT:
Puse el breakpoint en particles[i]->update(dt); para inspeccionar la _vtable de diferentes tipos de partículas.

Explicación:
En el depurador se observa la tabla de funciones virtuales (_vtable), donde aparecen métodos como:

update()
draw()
isDead()

Comparando FallingParticle con CircularExplosion, algunas funciones son diferentes porque cada clase tiene su propia implementación de draw() y update().

Justificación:
Esto demuestra que entiendo el polimorfismo a nivel interno, ya que cada objeto tiene su propia _vtable, lo que permite que se ejecute el método correcto dependiendo del tipo real del objeto.


EVIDENCIA 3
<img width="956" height="504" alt="image" src="https://github.com/user-attachments/assets/25839be6-d1a5-41c6-b340-1cc416d3c873" />
Elección del breakpoint:
Breakpoint en:

particles[i]->draw();

Explicación:
En el depurador se ve que, aunque todas las partículas se llaman igual (draw()), dependiendo del tipo:

FallingParticle dibuja un círculo pequeño
SpiralParticle dibuja en movimiento circular
ExplosionParticle dibuja según su tipo

Justificación:
Esto demuestra el polimorfismo en tiempo de ejecución porque el programa decide qué función ejecutar dependiendo del tipo real del objeto, no del tipo del puntero.


EVIDENCIA 4
<img width="940" height="646" alt="image" src="https://github.com/user-attachments/assets/bc51bef3-184a-484e-8b9b-0df492bc95b4" />
Elección del breakpoint:
Inspeccioné una instancia de RisingParticle en el depurador.

Explicación:
Se observa que:

Variables como position, velocity, color están accesibles porque son protected
No hay acceso directo a variables privadas desde fuera de la clase

Justificación:
Esto demuestra el encapsulamiento porque los datos están protegidos y solo pueden ser accedidos o modificados según el nivel de visibilidad definido en la clase.

EVIDENCIA 5
<img width="1006" height="693" alt="image" src="https://github.com/user-attachments/assets/08c0bad2-39a8-4541-bd08-69af14833c09" />
<img width="976" height="685" alt="image" src="https://github.com/user-attachments/assets/02085da3-1f26-4866-b264-7a1709069164" />
<img width="941" height="724" alt="image" src="https://github.com/user-attachments/assets/03396669-7a5c-4422-9648-9a2c5c30401f" />

Elección del breakpoint:

Creación → mousePressed()
Uso → update()
Eliminación → dentro del for cuando se hace delete

Explicación:

Cuando hago click, la partícula se agrega al vector
En update() cambia su posición y edad
Cuando cumple su condición (isDead()), se elimina del vector

Justificación:
Esto demuestra que entiendo el ciclo completo de vida de un objeto: creación, uso y destrucción.



EVIDENCIA 6
<img width="930" height="608" alt="image" src="https://github.com/user-attachments/assets/6a7de0ab-f349-4600-8066-71333c262151" />
<img width="963" height="717" alt="image" src="https://github.com/user-attachments/assets/7c4c92ed-221b-43db-825a-2f4c41e5920d" />

Breakpoint en:

delete particles[i];

Explicación:
Se observa que:

El objeto es eliminado con delete
Luego se remueve del vector con erase()

Justificación:
Esto demuestra que no hay fugas de memoria porque cada objeto creado con new es eliminado correctamente, evitando acumulación innecesaria de memoria.

EVIDENCIA 7

## Bitácora de reflexión

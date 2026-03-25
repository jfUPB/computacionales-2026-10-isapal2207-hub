# Unidad 5
## Bitácora de proceso de aprendizaje


## Bitácora de aplicación 
# FASE 1 
CODIGOS

ofApp.cpp
	
	#include "ofApp.h"
	// --------------------------------------------------------------
	void ofApp::setup() {
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
	
				
				int explosionType = (int)ofRandom(4);
	
				int numParticles = (int)ofRandom(20, 30);
				for (int j = 0; j < numParticles; j++) {
					if (explosionType == 0) {
						particles.push_back(new CircularExplosion(
							particles[i]->getPosition(), particles[i]->getColor()));
					} else if (explosionType == 1) {
						particles.push_back(new RandomExplosion(
							particles[i]->getPosition(), particles[i]->getColor()));
					} else if (explosionType == 2) {
						particles.push_back(new StarExplosion(
							particles[i]->getPosition(), particles[i]->getColor()));
					}
				
					else if (explosionType == 3) {
						particles.push_back(new RingExplosion(
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
	// RisingParticle: Partícula que nace en la parte inferior y sube
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
	// Clase base para explosiones: ExplosionParticle
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
	// CircularExplosion
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
	
	// -------------------------------------------------
	// RandomExplosion
	// -------------------------------------------------
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
	
	// -------------------------------------------------
	// StarExplosion
	// -------------------------------------------------
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
	// ofApp
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


## Bitácora de reflexión

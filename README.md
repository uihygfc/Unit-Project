// Final Project: Rabbit and Fox Simulation (Improved Visuals)
// Based on The Nature of Code - Chapter 5: Autonomous Agents

let rabbits = [];
let foxes = [];
let food = [];
const numRabbits = 10;
const numFoxes = 3;
const numFood = 20;

function setup() {
  createCanvas(800, 600);
  angleMode(DEGREES);
  for (let i = 0; i < numRabbits; i++) {
    rabbits.push(new Rabbit(random(width), random(height)));
  }
  for (let i = 0; i < numFoxes; i++) {
    foxes.push(new Fox(random(width), random(height)));
  }
  for (let i = 0; i < numFood; i++) {
    food.push(createVector(random(width), random(height)));
  }
}

function draw() {
  background(245, 252, 245);

  // Draw food
  for (let f of food) {
    noStroke();
    fill(100, 200, 100);
    ellipse(f.x, f.y, 8);
  }

  for (let rabbit of rabbits) {
    let closestFox = getClosest(rabbit, foxes);
    if (closestFox) rabbit.flee(closestFox);
    rabbit.eatFood(food);
    rabbit.update();
    rabbit.edges();
    rabbit.display();
  }

  for (let fox of foxes) {
    let closestRabbit = getClosest(fox, rabbits);
    if (closestRabbit) fox.seek(closestRabbit);
    fox.update();
    fox.edges();
    fox.display();
  }
}

function getClosest(agent, others) {
  let record = Infinity;
  let closest = null;
  for (let o of others) {
    let d = dist(agent.pos.x, agent.pos.y, o.pos.x, o.pos.y);
    if (d < record) {
      record = d;
      closest = o;
    }
  }
  return closest;
}

class Rabbit {
  constructor(x, y) {
    this.pos = createVector(x, y);
    this.vel = p5.Vector.random2D();
    this.acc = createVector();
    this.maxSpeed = 3;
    this.maxForce = 0.2;
    this.size = 14;
  }

  applyForce(force) {
    this.acc.add(force);
  }

  flee(predator) {
    let desired = p5.Vector.sub(this.pos, predator.pos);
    let d = desired.mag();
    if (d < 100) {
      desired.setMag(this.maxSpeed);
      let steer = p5.Vector.sub(desired, this.vel);
      steer.limit(this.maxForce);
      this.applyForce(steer);
    }
  }

  eatFood(foodArr) {
    for (let i = foodArr.length - 1; i >= 0; i--) {
      if (p5.Vector.dist(this.pos, foodArr[i]) < 10) {
        foodArr.splice(i, 1);
        this.maxSpeed = min(this.maxSpeed + 0.2, 5);
      }
    }
  }

  update() {
    this.vel.add(this.acc);
    this.vel.limit(this.maxSpeed);
    this.pos.add(this.vel);
    this.acc.mult(0);
  }

  edges() {
    if (this.pos.x < 0) this.pos.x = width;
    if (this.pos.x > width) this.pos.x = 0;
    if (this.pos.y < 0) this.pos.y = height;
    if (this.pos.y > height) this.pos.y = 0;
  }

  display() {
    push();
    translate(this.pos.x, this.pos.y);
    rotate(this.vel.heading());
    fill(180, 220, 255);
    stroke(80);
    strokeWeight(1);
    beginShape();
    vertex(-this.size * 0.5, this.size * 0.5);
    vertex(this.size, 0);
    vertex(-this.size * 0.5, -this.size * 0.5);
    endShape(CLOSE);
    pop();
  }
}

class Fox {
  constructor(x, y) {
    this.pos = createVector(x, y);
    this.vel = p5.Vector.random2D();
    this.acc = createVector();
    this.maxSpeed = 4;
    this.maxForce = 0.3;
    this.size = 18;
  }

  applyForce(force) {
    this.acc.add(force);
  }

  seek(prey) {
    let desired = p5.Vector.sub(prey.pos, this.pos);
    desired.setMag(this.maxSpeed);
    let steer = p5.Vector.sub(desired, this.vel);
    steer.limit(this.maxForce);
    this.applyForce(steer);
  }

  update() {
    this.vel.add(this.acc);
    this.vel.limit(this.maxSpeed);
    this.pos.add(this.vel);
    this.acc.mult(0);
  }

  edges() {
    if (this.pos.x < 0) this.pos.x = width;
    if (this.pos.x > width) this.pos.x = 0;
    if (this.pos.y < 0) this.pos.y = height;
    if (this.pos.y > height) this.pos.y = 0;
  }

  display() {
    push();
    translate(this.pos.x, this.pos.y);
    rotate(this.vel.heading());
    fill(255, 120, 100);
    stroke(80);
    strokeWeight(1);
    beginShape();
    vertex(-this.size * 0.6, this.size * 0.6);
    vertex(this.size, 0);
    vertex(-this.size * 0.6, -this.size * 0.6);
    endShape(CLOSE);
    pop();
  }
}

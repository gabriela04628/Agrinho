# Agrinho
let bubbles = [];
let score = 0;
let record = 0;
let misses = 0;
let level = 1;
let gameState = "start"; // start, playing, gameover
let spawnRate = 60;

function setup() {
  createCanvas(400, 400);
  textAlign(CENTER, CENTER);
}

function draw() {
  background(100, 150, 255);

  if (gameState === "start") {
    drawStartScreen();
  } else if (gameState === "playing") {
    playGame();
  } else if (gameState === "gameover") {
    drawGameOver();
  }
}

function drawStartScreen() {
  fill(255);
  textSize(24);
  text("🎈 Caçador de Bolhas 🎈", width / 2, 100);
  textSize(16);
  text("Clique para Iniciar", width / 2, 150);
}

function playGame() {
  // aumentar dificuldade conforme nível
  if (score > 0 && score % 10 === 0) {
    level = floor(score / 10) + 1;
    spawnRate = max(20, 60 - level * 3);
  }

  // gerar novas bolhas
  if (frameCount % spawnRate === 0) {
    bubbles.push(new Bubble(random(width), height + 20));
  }

  // atualizar bolhas
  for (let i = bubbles.length - 1; i >= 0; i--) {
    let b = bubbles[i];
    b.move();
    b.show();

    if (b.y < -20) {
      bubbles.splice(i, 1);
      misses++;
      if (misses >= 3) {
        gameState = "gameover";
        if (score > record) {
          record = score;
        }
      }
    }
  }

  // placar
  fill(255);
  textSize(14);
  text("Pontos: " + score, 60, 20);
  text("Nível: " + level, width / 2, 20);
  text("Erros: " + misses + " / 3", width - 70, 20);
}

function drawGameOver() {
  fill(255);
  textSize(24);
  text("Game Over", width / 2, 100);
  textSize(16);
  text("Pontuação: " + score, width / 2, 140);
  text("Recorde: " + record, width / 2, 170);
  text("Clique para Reiniciar", width / 2, 220);
}

function mousePressed() {
  if (gameState === "start" || gameState === "gameover") {
    startGame();
  } else if (gameState === "playing") {
    // verificar clique nas bolhas
    for (let i = bubbles.length - 1; i >= 0; i--) {
      if (bubbles[i].clicked(mouseX, mouseY)) {
        bubbles.splice(i, 1);
        score++;
        break;
      }
    }
  }
}

function startGame() {
  score = 0;
  misses = 0;
  level = 1;
  spawnRate = 60;
  bubbles = [];
  gameState = "playing";
}

class Bubble {
  constructor(x, y) {
    this.x = x;
    this.y = y;
    this.r = random(15, 30);
    this.speed = random(1 + level * 0.2, 2 + level * 0.5);
    this.col = color(random(100, 255), random(100, 255), random(255));
  }

  move() {
    this.y -= this.speed;
  }

  show() {
    noStroke();
    fill(this.col);
    ellipse(this.x, this.y, this.r * 2);
  }

  clicked(mx, my) {
    let d = dist(mx, my, this.x, this.y);
    return d < this.r;
  }
}

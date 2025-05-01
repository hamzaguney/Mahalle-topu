# Mahalle-topu
Kafa topu benzeri
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Kafa Topu Mini</title>
  <style>
    canvas { background: #6ab04c; display: block; margin: auto; }
  </style>
</head>
<body>
  <canvas id="gameCanvas" width="800" height="400"></canvas>
  <script src="game.js"></script>
</body>
</html>

const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');

const player1 = { x: 100, y: 300, radius: 30, color: 'red', vy: 0, grounded: true };
const player2 = { x: 700, y: 300, radius: 30, color: 'blue', vy: 0, grounded: true };
const gravity = 1;
const jumpPower = -15;
const speed = 5;

const ball = { x: 400, y: 200, radius: 15, vx: 3, vy: 3 };

const keys = {};

document.addEventListener('keydown', e => keys[e.code] = true);
document.addEventListener('keyup', e => keys[e.code] = false);

function updatePlayer(player, leftKey, rightKey, jumpKey) {
  if (keys[leftKey]) player.x -= speed;
  if (keys[rightKey]) player.x += speed;

  if (player.grounded && keys[jumpKey]) {
    player.vy = jumpPower;
    player.grounded = false;
  }

  player.vy += gravity;
  player.y += player.vy;

  if (player.y + player.radius > canvas.height) {
    player.y = canvas.height - player.radius;
    player.vy = 0;
    player.grounded = true;
  }
}

function checkCollision(p, b) {
  const dx = p.x - b.x;
  const dy = p.y - b.y;
  const dist = Math.sqrt(dx * dx + dy * dy);
  if (dist < p.radius + b.radius) {
    const angle = Math.atan2(dy, dx);
    const force = 5;
    b.vx = -Math.cos(angle) * force;
    b.vy = -Math.sin(angle) * force;
  }
}

function updateBall() {
  ball.x += ball.vx;
  ball.y += ball.vy;

  // Wall bounce
  if (ball.x < ball.radius || ball.x > canvas.width - ball.radius) ball.vx *= -1;
  if (ball.y < ball.radius || ball.y > canvas.height - ball.radius) ball.vy *= -1;

  // Goal detection
  if (ball.x < 0) resetGame('Mavi kazandı!');
  if (ball.x > canvas.width) resetGame('Kırmızı kazandı!');
}

function resetGame(msg) {
  alert(msg);
  ball.x = 400;
  ball.y = 200;
  ball.vx = 3;
  ball.vy = 3;
}

function drawCircle(obj) {
  ctx.beginPath();
  ctx.arc(obj.x, obj.y, obj.radius, 0, Math.PI * 2);
  ctx.fillStyle = obj.color;
  ctx.fill();
  ctx.closePath();
}

function draw() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  updatePlayer(player1, 'KeyA', 'KeyD', 'KeyW');
  updatePlayer(player2, 'ArrowLeft', 'ArrowRight', 'ArrowUp');

  updateBall();
  checkCollision(player1, ball);
  checkCollision(player2, ball);

  drawCircle(player1);
  drawCircle(player2);
  drawCircle(ball);

  requestAnimationFrame(draw);
}

draw();

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Kafa Topu Mobil</title>
  <style>
    body { margin: 0; overflow: hidden; }
    canvas { background: #6ab04c; display: block; }
    .controls {
      position: fixed;
      bottom: 10px;
      width: 100%;
      display: flex;
      justify-content: space-around;
      pointer-events: auto;
    }
    .btn {
      background: rgba(0,0,0,0.5);
      color: white;
      font-size: 24px;
      border: none;
      padding: 20px;
      border-radius: 50%;
      width: 60px;
      height: 60px;
      touch-action: manipulation;
    }
  </style>
</head>
<body>
  <canvas id="gameCanvas"></canvas>

  <div class="controls">
    <button class="btn" id="left">←</button>
    <button class="btn" id="jump">↑</button>
    <button class="btn" id="right">→</button>
  </div>

  <script src="game.js"></script>
</body>
</html>

const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');

function resizeCanvas() {
  canvas.width = window.innerWidth;
  canvas.height = window.innerHeight;
}
window.addEventListener('resize', resizeCanvas);
resizeCanvas();

const player = { x: 100, y: 300, radius: 30, color: 'red', vy: 0, grounded: true };
const ball = { x: 200, y: 200, radius: 20, vx: 4, vy: 4 };

const gravity = 0.8;
const jumpPower = -15;
const speed = 5;

let keys = {
  left: false,
  right: false,
  jump: false,
};

// Mobil buton olayları
document.getElementById('left').addEventListener('touchstart', () => keys.left = true);
document.getElementById('left').addEventListener('touchend', () => keys.left = false);

document.getElementById('right').addEventListener('touchstart', () => keys.right = true);
document.getElementById('right').addEventListener('touchend', () => keys.right = false);

document.getElementById('jump').addEventListener('touchstart', () => {
  if (player.grounded) {
    player.vy = jumpPower;
    player.grounded = false;
  }
});

function updatePlayer() {
  if (keys.left) player.x -= speed;
  if (keys.right) player.x += speed;

  player.vy += gravity;
  player.y += player.vy;

  if (player.y + player.radius > canvas.height) {
    player.y = canvas.height - player.radius;
    player.vy = 0;
    player.grounded = true;
  }
}

function updateBall() {
  ball.x += ball.vx;
  ball.y += ball.vy;

  if (ball.x < ball.radius || ball.x > canvas.width - ball.radius) ball.vx *= -1;
  if (ball.y < ball.radius || ball.y > canvas.height - ball.radius) ball.vy *= -1;
}

function checkCollision(p, b) {
  const dx = p.x - b.x;
  const dy = p.y - b.y;
  const dist = Math.sqrt(dx * dx + dy * dy);
  if (dist < p.radius + b.radius) {
    const angle = Math.atan2(dy, dx);
    const force = 5;
    b.vx = -Math.cos(angle) * force;
    b.vy = -Math.sin(angle) * force;
  }
}

function drawCircle(obj) {
  ctx.beginPath();
  ctx.arc(obj.x, obj.y, obj.radius, 0, Math.PI * 2);
  ctx.fillStyle = obj.color;
  ctx.fill();
}

function draw() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  updatePlayer();
  updateBall();
  checkCollision(player, ball);

  drawCircle(player);
  drawCircle(ball);

  requestAnimationFrame(draw);
}

draw();

<div id="characterSelect" style="text-align:center; padding:20px; background:#95afc0;">
  <h2>Karakterini Seç</h2>
  <button onclick="selectCharacter('red')">Kırmızı</button>
  <button onclick="selectCharacter('blue')">Mavi</button>
</div>

let player, bot, score = { player: 0, bot: 0 };

const music = new Audio('music.mp3');
music.loop = true;

function initGame() {
  music.play();

  player = { x: 100, y: 300, radius: 30, color: playerColor, vy: 0, grounded: true };
  bot = { x: canvas.width - 100, y: 300, radius: 30, color: 'gray', vy: 0, grounded: true };
  ball.x = canvas.width / 2;
  ball.y = canvas.height / 2;
  ball.vx = 4;
  ball.vy = 4;
  score = { player: 0, bot: 0 };
  draw();
}

function updateBot() {
  if (ball.x < bot.x) bot.x -= 2;
  else bot.x += 2;

  if (bot.grounded && Math.random() < 0.02) {
    bot.vy = jumpPower;
    bot.grounded = false;
  }

  bot.vy += gravity;
  bot.y += bot.vy;

  if (bot.y + bot.radius > canvas.height) {
    bot.y = canvas.height - bot.radius;
    bot.vy = 0;
    bot.grounded = true;
  }
}

function checkGoal() {
  if (ball.x < 0) {
    score.bot++;
    resetRound();
  } else if (ball.x > canvas.width) {
    score.player++;
    resetRound();
  }

  if (score.player >= 3) {
    alert("Kazandın!");
    location.reload();
  } else if (score.bot >= 3) {
    alert("Kaybettin!");
    location.reload();
  }
}

function resetRound() {
  ball.x = canvas.width / 2;
  ball.y = canvas.height / 2;
  ball.vx = 4 * (Math.random() > 0.5 ? 1 : -1);
  ball.vy = 4;
}

function draw() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  updatePlayer();
  updateBot();
  updateBall();
  checkCollision(player, ball);
  checkCollision(bot, ball);
  checkGoal();

  drawCircle(player);
  drawCircle(bot);
  drawCircle(ball);

  ctx.fillStyle = "white";
  ctx.font = "20px Arial";
  ctx.fillText(`Sen: ${score.player} - Bot: ${score.bot}`, 10, 30);

  requestAnimationFrame(draw);
}

npm install -g cordova
cordova create kafaTopu com.example.kafatopu "KafaTopu"
cd kafaTopu
cordova platform add android

cordova build android





<!DOCTYPE html><html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Run, Princess, Run!</title>
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; }
    body { overflow: hidden; font-family: sans-serif; }
    canvas { display: block; background: linear-gradient(#87ceeb, #fff); }
    #info {
      position: absolute;
      top: 10px;
      left: 10px;
      color: white;
      font-weight: bold;
      background: rgba(0,0,0,0.5);
      padding: 10px;
      border-radius: 10px;
    }
  </style>
</head>
<body>
<canvas id="gameCanvas"></canvas>
<div id="info">Coins: <span id="coins">0</span></div><script>
const canvas = document.getElementById("gameCanvas");
const ctx = canvas.getContext("2d");
canvas.width = window.innerWidth;
canvas.height = window.innerHeight;

let princess = {
  x: 100,
  y: canvas.height - 150,
  width: 50,
  height: 80,
  yVelocity: 0,
  jumping: false
};

let monster = {
  x: 0,
  y: canvas.height - 140,
  width: 60,
  height: 100
};

let coins = [];
let obstacles = [];
let score = 0;

function createCoin() {
  coins.push({
    x: canvas.width,
    y: canvas.height - 180,
    radius: 10
  });
}

function createObstacle() {
  obstacles.push({
    x: canvas.width,
    y: canvas.height - 100,
    width: 40,
    height: 40
  });
}

function drawPrincess() {
  ctx.fillStyle = "pink";
  ctx.fillRect(princess.x, princess.y, princess.width, princess.height);
}

function drawMonster() {
  ctx.fillStyle = "black";
  ctx.fillRect(monster.x, monster.y, monster.width, monster.height);
}

function drawCoins() {
  ctx.fillStyle = "gold";
  coins.forEach((coin, i) => {
    ctx.beginPath();
    ctx.arc(coin.x, coin.y, coin.radius, 0, Math.PI * 2);
    ctx.fill();
    coin.x -= 5;

    // collision
    if (
      princess.x < coin.x + coin.radius &&
      princess.x + princess.width > coin.x &&
      princess.y < coin.y + coin.radius &&
      princess.y + princess.height > coin.y
    ) {
      coins.splice(i, 1);
      score++;
      document.getElementById("coins").innerText = score;
    }
  });
}

function drawObstacles() {
  ctx.fillStyle = "brown";
  obstacles.forEach((obs, i) => {
    ctx.fillRect(obs.x, obs.y, obs.width, obs.height);
    obs.x -= 7;

    if (
      princess.x < obs.x + obs.width &&
      princess.x + princess.width > obs.x &&
      princess.y < obs.y + obs.height &&
      princess.y + princess.height > obs.y
    ) {
      alert("Game Over! Coins collected: " + score);
      window.location.reload();
    }
  });
}

function updatePrincess() {
  princess.y += princess.yVelocity;
  princess.yVelocity += 1.5; // gravity

  if (princess.y > canvas.height - 150) {
    princess.y = canvas.height - 150;
    princess.jumping = false;
  }
}

function gameLoop() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  drawPrincess();
  drawMonster();
  drawCoins();
  drawObstacles();
  updatePrincess();

  monster.x += 0.3;
  if (monster.x + monster.width > princess.x) {
    alert("The monster caught you! Game Over!");
    window.location.reload();
  }

  requestAnimationFrame(gameLoop);
}

setInterval(createCoin, 2000);
setInterval(createObstacle, 3000);

window.addEventListener("keydown", (e) => {
  if (e.code === "Space" && !princess.jumping) {
    princess.yVelocity = -25;
    princess.jumping = true;
  }
});

gameLoop();
</script></body>
</html>

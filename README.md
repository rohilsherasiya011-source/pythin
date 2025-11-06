<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>🐍 Snake Game</title>
<style>
  body {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    background-color: white;
    font-family: Arial, sans-serif;
  }
  canvas {
    background-color: #fff;
    border: 2px solid black;
  }
  #score {
    position: absolute;
    top: 20px;
    left: 20px;
    font-size: 20px;
    color: black;
  }
</style>
</head>
<body>

<div id="score">Score: 0</div>
<canvas id="gameCanvas" width="600" height="400"></canvas>

<script>
const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');

const WIDTH = canvas.width;
const HEIGHT = canvas.height;
const BLOCK_SIZE = 20;

let snake = [{x: WIDTH/2, y: HEIGHT/2}];
let dx = 0;
let dy = 0;
let food = getRandomFood();
let score = 0;
let gameOver = false;

document.addEventListener('keydown', changeDirection);

function gameLoop() {
    if (gameOver) return;

    update();
    draw();
    setTimeout(gameLoop, 100); // snake speed
}

function update() {
    const head = {x: snake[snake.length-1].x + dx, y: snake[snake.length-1].y + dy};

    // Wall collision
    if (head.x < 0 || head.x >= WIDTH || head.y < 0 || head.y >= HEIGHT) {
        gameOverScreen();
        return;
    }

    // Self collision
    for (let segment of snake) {
        if (head.x === segment.x && head.y === segment.y) {
            gameOverScreen();
            return;
        }
    }

    snake.push(head);

    // Eat food
    if (head.x === food.x && head.y === food.y) {
        score++;
        food = getRandomFood();
    } else {
        snake.shift(); // remove tail
    }

    document.getElementById('score').innerText = "Score: " + score;
}

function draw() {
    ctx.clearRect(0, 0, WIDTH, HEIGHT);

    // draw snake
    ctx.fillStyle = "green";
    for (let segment of snake) {
        ctx.fillRect(segment.x, segment.y, BLOCK_SIZE, BLOCK_SIZE);
    }

    // draw food
    ctx.fillStyle = "red";
    ctx.fillRect(food.x, food.y, BLOCK_SIZE, BLOCK_SIZE);
}

function changeDirection(event) {
    const key = event.key;
    if (key === "ArrowUp" && dy === 0) {
        dx = 0; dy = -BLOCK_SIZE;
    } else if (key === "ArrowDown" && dy === 0) {
        dx = 0; dy = BLOCK_SIZE;
    } else if (key === "ArrowLeft" && dx === 0) {
        dx = -BLOCK_SIZE; dy = 0;
    } else if (key === "ArrowRight" && dx === 0) {
        dx = BLOCK_SIZE; dy = 0;
    }
}

function getRandomFood() {
    const x = Math.floor(Math.random() * (WIDTH / BLOCK_SIZE)) * BLOCK_SIZE;
    const y = Math.floor(Math.random() * (HEIGHT / BLOCK_SIZE)) * BLOCK_SIZE;
    return {x, y};
}

function gameOverScreen() {
    gameOver = true;
    ctx.fillStyle = "black";
    ctx.font = "30px Arial";
    ctx.fillText("Game Over! Press F5 to Restart", 100, HEIGHT/2);
}

gameLoop();
</script>

</body>
</html>

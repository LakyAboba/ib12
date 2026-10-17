---
{"dg-publish":true,"permalink":"/ib-12/testovye-fajly-ne-vhodit/test2/","dg-note-properties":{}}
---

# 🎮 Перерыв на ИБ-12 | Терминальная Змейка v2.0

_Управление: стрелочки на клавиатуре или кнопки на экране (для мобилок). Змейка теперь умеет проходить сквозь стены!_

<div id="game-container">
  <div id="score-board">Счёт: <span id="score">0</span> | Рекорд: <span id="high-score">0</span></div>
  <!-- Размер поля увеличен до 500x500 -->
  <canvas id="gameCanvas" width="500" height="500"></canvas>
  
  <!-- Кнопки для игры с телефона -->
  <div id="controls">
    <button onclick="changeDirection({keyCode: 38})">🔼</button><br>
    <button onclick="changeDirection({keyCode: 37})">◀️</button>
    <button onclick="changeDirection({keyCode: 40})">🔽</button>
    <button onclick="changeDirection({keyCode: 39})">▶️</button>
  </div>
</div>

<style>
  #game-container { text-align: center; font-family: monospace; background: #111; padding: 20px; border-radius: 10px; max-width: 540px; margin: 0 auto; color: #00ff00; }
  #score-board { font-size: 20px; margin-bottom: 10px; }
  canvas { border: 2px solid #00ff00; background-color: #000; display: block; margin: 0 auto; box-shadow: 0 0 15px rgba(0, 255, 0, 0.3); }
  #controls { margin-top: 15px; display: none; }
  #controls button { font-size: 24px; padding: 10px 20px; margin: 3px; background: #222; border: 1px solid #00ff00; color: #00ff00; border-radius: 5px; cursor: pointer; }
  @media (max-width: 768px) { #controls { display: block; } }
</style>

<script>
  const canvas = document.getElementById("gameCanvas");
  const ctx = canvas.getContext("2d");
  const scoreElement = document.getElementById("score");
  const highScoreElement = document.getElementById("high-score");

  const gridSize = 25; // Увеличили шаг сетки, чтобы на большем поле всё смотрелось соразмерно
  const tileCount = canvas.width / gridSize;
  
  let snake = [{x: 10, y: 10}];
  let food = {x: 5, y: 5};
  let dx = 1;
  let dy = 0;
  let score = 0;
  let highScore = localStorage.getItem("snake-hi") || 0;
  highScoreElement.innerText = highScore;

  function drawGame() {
    clearCanvas();
    moveSnake();
    drawFood();
    drawSnake();
    checkGameOver();
  }

  function clearCanvas() {
    ctx.fillStyle = "#000";
    ctx.fillRect(0, 0, canvas.width, canvas.height);
  }

  function drawSnake() {
    snake.forEach((part, index) => {
      ctx.fillStyle = index === 0 ? "#00cc00" : "#00ff00";
      ctx.fillRect(part.x * gridSize, part.y * gridSize, gridSize - 2, gridSize - 2);
    });
  }

  function drawFood() {
    ctx.fillStyle = "#ff0000";
    ctx.fillRect(food.x * gridSize, food.y * gridSize, gridSize - 2, gridSize - 2);
  }

  function moveSnake() {
    let nextX = snake[0].x + dx;
    let nextY = snake[0].y + dy;

    // ТЕЛЕПОРТАЦИЯ СКВОЗЬ СТЕНЫ: если выходим за границы, переносим на противоположную сторону
    if (nextX < 0) nextX = tileCount - 1;
    if (nextX >= tileCount) nextX = 0;
    if (nextY < 0) nextY = tileCount - 1;
    if (nextY >= tileCount) nextY = 0;

    const head = {x: nextX, y: nextY};
    snake.unshift(head);

    if (head.x === food.x && head.y === food.y) {
      score += 10;
      scoreElement.innerText = score;
      if (score > highScore) {
        highScore = score;
        highScoreElement.innerText = highScore;
        localStorage.setItem("snake-hi", highScore);
      }
      generateFood();
    } else {
      snake.pop();
    }
  }

  function generateFood() {
    food.x = Math.floor(Math.random() * tileCount);
    food.y = Math.floor(Math.random() * tileCount);
    if (snake.some(part => part.x === food.x && part.y === food.y)) {
      generateFood();
    }
  }

  function changeDirection(event) {
    const keyPressed = event.keyCode;
    const goingUp = dy === -1;
    const goingDown = dy === 1;
    const goingRight = dx === 1;
    const goingLeft = dx === -1;

    if (keyPressed === 37 && !goingRight) { dx = -1; dy = 0; }
    if (keyPressed === 38 && !goingDown) { dx = 0; dy = -1; }
    if (keyPressed === 39 && !goingLeft) { dx = 1; dy = 0; }
    if (keyPressed === 40 && !goingUp) { dx = 0; dy = 1; }
  }

  function checkGameOver() {
    const head = snake[0];
    // Теперь врезаться можно только в свой собственный хвост
    for (let i = 1; i < snake.length; i++) {
      if (snake[i].x === head.x && snake[i].y === head.y) {
        alert("Игра окончена! Твой счёт: " + score);
        resetGame();
      }
    }
  }

  function resetGame() {
    snake = [{x: 10, y: 10}];
    dx = 1;
    dy = 0;
    score = 0;
    scoreElement.innerText = score;
    generateFood();
  }

  document.addEventListener("keydown", changeDirection);
  
  // СКОРОСТЬ: Изменили интервал со 100 на 140 миллисекунд (игра станет более плавной и размеренной)
  setInterval(drawGame, 140);
</script>

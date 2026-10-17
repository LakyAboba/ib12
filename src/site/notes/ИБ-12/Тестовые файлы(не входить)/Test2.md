---
{"dg-publish":true,"permalink":"/ib-12/testovye-fajly-ne-vhodit/test2/","dg-note-properties":{}}
---

# 🎮 Перерыв на ИБ-12 | Терминальная Змейка v2.4

_Управление: стрелочки на клавиатуре (на ПК) или удобная большая крестовина на экране (на телефонах)._

<div id="game-container">
  <div id="score-board">Счёт: <span id="score">0</span> | Рекорд: <span id="high-score">0</span></div>
  
  <!-- Адаптивный контейнер для холста -->
  <div id="canvas-wrapper">
    <canvas id="gameCanvas" width="400" height="400"></canvas>
  </div>
  
  <!-- Большая центрированная крестовина (D-pad) для мобилок -->
  <div id="controls-wrapper">
    <table id="dpad-table">
      <tr>
        <td></td>
        <td><button class="dpad-btn" onclick="changeDirection({keyCode: 38})">🔼</button></td>
        <td></td>
      </tr>
      <tr>
        <td><button class="dpad-btn" onclick="changeDirection({keyCode: 37})">◀️</button></td>
        <td><button class="dpad-btn" onclick="changeDirection({keyCode: 40})">🔽</button></td>
        <td><button class="dpad-btn" onclick="changeDirection({keyCode: 39})">▶️</button></td>
      </tr>
    </table>
  </div>
</div>

<style>
  #game-container { 
    text-align: center; 
    font-family: monospace; 
    background: #111; 
    padding: 15px; 
    border-radius: 10px; 
    max-width: 440px; 
    margin: 0 auto; 
    color: #00ff00;
    box-sizing: border-box;
  }
  #score-board { font-size: 18px; margin-bottom: 12px; }
  
  /* НА ПК: Уменьшенный и аккуратный размер игрового поля (350x350px) */
  #canvas-wrapper {
    width: 350px;
    height: 350px;
    margin: 0 auto;
  }
  canvas { 
    border: 2px solid #00ff00; 
    background-color: #000; 
    box-shadow: 0 0 15px rgba(0, 255, 0, 0.3);
    display: block;
    width: 100%;
    height: 100%;
  }
  
  /* НА ПК: Кнопки гарантированно и полностью скрыты */
  #controls-wrapper { 
    display: none !important; 
  }

  /* Стили большой и отцентрированной таблицы-крестовины */
  #dpad-table {
    margin: 0 auto !important; /* Центрирование таблицы по горизонтали */
    border-collapse: collapse !important;
    border: none !important;
    background: transparent !important;
  }
  #dpad-table td {
    width: 75px !important;  /* Увеличили ширину ячейки кнопки */
    height: 70px !important; /* Увеличили высоту ячейки кнопки */
    padding: 4px !important;
    border: none !important;
    text-align: center !important;
    vertical-align: middle !important;
    background: transparent !important;
  }
  .dpad-btn { 
    width: 100% !important;
    height: 100% !important;
    font-size: 26px !important; /* Сделали эмодзи стрелочек крупнее */
    background: #222 !important; 
    border: 2px solid #00ff00 !important; 
    color: #00ff00 !important; 
    border-radius: 12px !important; /* Более плавные скруглённые углы */
    cursor: pointer !important; 
    padding: 0 !important;
    margin: 0 !important;
    box-sizing: border-box !important;
    box-shadow: 0 3px 6px rgba(0,0,0,0.5);
  }
  .dpad-btn:active {
    background: #00ff00 !important;
    color: #000 !important;
  }

  /* НА СМАРТФОНАХ: Включаем адаптивное сжатие поля под экран и активируем центрированный D-pad */
  @media (max-width: 768px) { 
    #game-container {
      max-width: 100%;
    }
    #canvas-wrapper {
      width: 100% !important;
      height: auto !important;
    }
    canvas {
      width: 100% !important;
      height: auto !important;
    }
    #controls-wrapper { 
      display: block !important; /* Показываем блок управления на мобилках */
      text-align: center !important;
      margin-top: 25px !important;
    } 
  }
</style>

<script>
  const canvas = document.getElementById("gameCanvas");
  const ctx = canvas.getContext("2d");
  const scoreElement = document.getElementById("score");
  const highScoreElement = document.getElementById("high-score");

  const gridSize = 20; // Оптимальный шаг сетки для разрешения 400x400
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
  setInterval(drawGame, 140);
</script>

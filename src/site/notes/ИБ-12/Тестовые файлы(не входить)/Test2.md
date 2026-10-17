---
{"dg-publish":true,"permalink":"/ib-12/testovye-fajly-ne-vhodit/test2/","dg-note-properties":{}}
---

# 🎮 Перерыв на ИБ-12 | Терминальная Змейка v2.7

_Управление: стрелочки на клавиатуре (на ПК) или супер-гигантская крестовина (на телефонах)._

<!-- Фиксированный центрирующий контейнер -->
<div align="center" style="width: 100%; text-align: center !important; margin: 0 auto !important;">

  <div id="game-container">
    <div id="score-board">Счёт: <span id="score">0</span> | Рекорд: <span id="high-score">0</span></div>
    
    <!-- Контейнер холста с жестким ограничением размеров -->
    <div id="canvas-wrapper">
      <canvas id="gameCanvas" width="400" height="400"></canvas>
    </div>
    
    <!-- Супер-гигантская крестовина управления, прижатая к полю -->
    <div id="controls-wrapper" align="center">
      <table id="dpad-table" align="center">
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

</div>

<style>
  #game-container { 
    text-align: center !important; 
    font-family: monospace; 
    background: #111; 
    padding: 8px !important; 
    border-radius: 10px; 
    max-width: 216px; /* Жестко ограничили ширину контейнера под размер поля на ПК */
    margin: 0 auto !important; 
    color: #00ff00;
    box-sizing: border-box !important;
    display: block;
  }
  #score-board { font-size: 13px; margin-bottom: 5px; }
  
  /* НА ПК: Мини-масштаб холста (200x240px с рамкой), поле сжато и видно целиком */
  #canvas-wrapper {
    width: 200px !important;
    height: 200px !important;
    margin: 0 auto !important;
    box-sizing: border-box !important;
  }
  canvas { 
    border: 2px solid #00ff00 !important; 
    background-color: #000 !important; 
    box-shadow: 0 0 10px rgba(0, 255, 0, 0.3);
    display: block !important;
    width: 100% !important;   /* Масштабируем внутреннюю графику 400x400 */
    height: 100% !important;  /* под внешние пиксельные размеры */
    box-sizing: border-box !important;
  }
  
  /* НА ПК: Полностью вырезаем блок кнопок */
  #controls-wrapper { 
    display: none !important; 
  }

  /* Стили СУПЕР-ГИГАНТСКОЙ крестовины, максимально поднятой вверх */
  #dpad-table {
    margin: 0 auto !important;
    border-collapse: collapse !important;
    border: none !important;
    background: transparent !important;
  }
  #dpad-table td {
    width: 115px !important;  /* Сверх-большая ширина для пальцев */
    height: 100px !important; /* Сверх-большая высота для пальцев */
    padding: 2px !important;  /* Минимальные зазоры между кнопками */
    border: none !important;
    text-align: center !important;
    vertical-align: middle !important;
    background: transparent !important;
  }
  .dpad-btn { 
    width: 100% !important;
    height: 100% !important;
    font-size: 42px !important; /* Огромные стрелки-эмодзи */
    background: #222 !important; 
    border: 2px solid #00ff00 !important; 
    color: #00ff00 !important; 
    border-radius: 18px !important; 
    cursor: pointer !important; 
    padding: 0 !important;
    margin: 0 !important;
    box-sizing: border-box !important;
    box-shadow: 0 4px 6px rgba(0,0,0,0.6);
  }
  .dpad-btn:active {
    background: #00ff00 !important;
    color: #000 !important;
  }

  /* НА ТЕЛЕФОНАХ: Разворачиваем игру и убираем лишние отступы */
  @media (max-width: 768px) { 
    #game-container {
      max-width: 100% !important;
      padding: 6px !important;
    }
    #canvas-wrapper {
      width: 100% !important;
      height: auto !important;
      max-width: 310px; 
    }
    #controls-wrapper { 
      display: block !important; 
      visibility: visible !important;
      margin: 5px auto 0 auto !important; /* Практически вплотную прижали джойстик к полю */
      width: 100% !important;
      text-align: center !important;
    } 
  }
</style>

<script>
  const canvas = document.getElementById("gameCanvas");
  const ctx = canvas.getContext("2d");
  const scoreElement = document.getElementById("score");
  const highScoreElement = document.getElementById("high-score");

  const gridSize = 20; 
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

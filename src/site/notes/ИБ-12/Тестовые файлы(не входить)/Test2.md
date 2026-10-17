---
{"dg-publish":true,"permalink":"/ib-12/testovye-fajly-ne-vhodit/test2/","dg-note-properties":{}}
---

# 🎮 Перерыв на ИБ-12 | Терминальная Змейка v3.0

_Управление: стрелочки на клавиатуре (на ПК) или супер-гигантская крестовина (на телефонах)._

<div id="game-super-wrapper">
  <div id="game-container">
    <div id="score-board">Счёт: <span id="score">0</span> | Рекорд: <span id="high-score">0</span></div>
    
    <!-- Изолированный контейнер холста -->
    <div id="canvas-wrapper">
      <canvas id="gameCanvas" width="400" height="400"></canvas>
    </div>
    
    <!-- Компактная блочная крестовина, прижатая к полю -->
    <div id="controls-wrapper">
      <div class="dpad-row">
        <div class="dpad-empty"></div>
        <button class="dpad-btn" onclick="changeDirection({keyCode: 38})">🔼</button>
        <div class="dpad-empty"></div>
      </div>
      <div class="dpad-row">
        <button class="dpad-btn" onclick="changeDirection({keyCode: 37})">◀️</button>
        <button class="dpad-btn" onclick="changeDirection({keyCode: 40})">🔽</button>
        <button class="dpad-btn" onclick="changeDirection({keyCode: 39})">▶️</button>
      </div>
    </div>
  </div>
</div>

<style>
  /* НА ПК: Полностью отключаем прокрутку страницы и убираем полосы прокрутки */
  @media (min-width: 769px) {
    html, body, .quartz-page, main, article {
      overflow: hidden !important;
      height: 100% !important;
    }
  }

  /* Полная изоляция от темы сайта */
  #game-super-wrapper {
    width: 100% !important;
    display: flex !important;
    justify-content: center !important;
    text-align: center !important;
    padding: 0 !important;
    margin: 0 auto !important;
  }
  #game-container { 
    font-family: monospace; 
    background: #111 !important; 
    padding: 10px !important; 
    border-radius: 10px !important; 
    width: 250px !important; /* Фиксированная ширина на ПК */
    max-width: 250px !important;
    box-sizing: border-box !important;
    display: flex !important;
    flex-direction: column !important;
    align-items: center !important;
    margin-top: 10px !important;
  }
  #score-board { font-size: 14px !important; color: #00ff00 !important; margin-bottom: 6px !important; }
  
  /* НА ПК: Сжатие холста до 230x230px, чтобы поле было видно ЦЕЛИКОМ */
  #canvas-wrapper {
    width: 230px !important;
    height: 230px !important;
    display: block !important;
    box-sizing: border-box !important;
    margin: 0 auto !important;
  }
  canvas { 
    border: 2px solid #00ff00 !important; 
    background-color: #000 !important; 
    box-shadow: 0 0 10px rgba(0, 255, 0, 0.3) !important;
    display: block !important;
    width: 230px !important;   
    height: 230px !important;  
    box-sizing: border-box !important;
  }
  
  /* НА ПК: Полностью вырезаем кнопки управления */
  #controls-wrapper { 
    display: none !important; 
  }

  /* НА СМАРТФОНАХ: Перестраиваем размеры и поднимаем кнопки наверх */
  @media (max-width: 768px) { 
    #game-container {
      width: 100% !important;
      max-width: 340px !important; /* Контейнер растягивается под мобильный экран */
    }
    #canvas-wrapper {
      width: 100% !important;
      height: auto !important;
      max-width: 320px !important; 
    }
    canvas {
      width: 100% !important;
      height: auto !important;
    }
    /* Показываем кнопки и убираем отступы, прижимая их к Canvas */
    #controls-wrapper { 
      display: flex !important; 
      flex-direction: column !important;
      align-items: center !important;
      margin-top: 5px !important; /* Минимальный зазор под полем */
      width: 100% !important;
    } 
  }

  /* Стили блочной крестовины (D-pad) */
  .dpad-row {
    display: flex !important;
    justify-content: center !important;
    width: 100% !important;
    margin: 0 !important;
    padding: 0 !important;
  }
  .dpad-btn, .dpad-empty {
    width: 105px !important;  /* Сверх-большой размер */
    height: 90px !important;
    box-sizing: border-box !important;
    margin: 2px !important;
  }
  .dpad-btn { 
    font-size: 38px !important; 
    background: #222 !important; 
    border: 2px solid #00ff00 !important; 
    color: #00ff00 !important; 
    border-radius: 16px !important; 
    cursor: pointer !important; 
    display: flex !important;
    align-items: center !important;
    justify-content: center !important;
    box-shadow: 0 4px 6px rgba(0,0,0,0.6) !important;
  }
  .dpad-btn:active {
    background: #00ff00 !important;
    color: #000 !important;
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

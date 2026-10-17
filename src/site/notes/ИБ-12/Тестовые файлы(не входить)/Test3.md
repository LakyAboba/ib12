---
{"dg-publish":true,"permalink":"/ib-12/testovye-fajly-ne-vhodit/test3/","dg-note-properties":{}}
---

# 🕹️ Перерыв на ИБ-12 | Терминальный Тетрис v1.8

_Управление на ПК: Стрелочки (Влево/Вправо — движение, Вверх — поворот, Вниз — ускорить падение), Пробел — мгновенный сброс._

<!-- Изолированный супер-враппер для центрирования и сжатия отступов -->
<div id="tetris-super-wrapper">
  <div id="game-container">
    <div id="score-board">Счёт: <span id="score">0</span> | Линии: <span id="lines">0</span></div>
    
    <div id="canvas-wrapper">
      <canvas id="tetrisCanvas" width="240" height="400"></canvas>
    </div>
    
    <!-- Неубиваемый D-pad на блоках -->
    <div id="controls-wrapper">
      <div class="dpad-row">
        <div class="dpad-empty"></div>
        <button class="dpad-btn" onclick="handleDpad(38)">🔄</button>
        <div class="dpad-empty"></div>
      </div>
      <div class="dpad-row">
        <button class="dpad-btn" onclick="handleDpad(37)">◀️</button>
        <button class="dpad-btn" onclick="handleDpad(40)">🔽</button>
        <button class="dpad-btn" onclick="handleDpad(39)">▶️</button>
      </div>
      <div class="dpad-row" style="margin-top: 3px !important;">
        <button class="dpad-btn drop-btn" onclick="handleDpad(32)">💥 МГНОВЕННЫЙ СБРОС 💥</button>
      </div>
    </div>
  </div>
</div>

<style>
  /* Блокируем скролл страницы на ПК */
  @media (min-width: 769px) {
    html, body, .quartz-page, main, article {
      overflow: hidden !important;
      height: 100% !important;
    }
  }

  /* Полное принудительное центрирование и схлопывание внешних параграфов */
  #tetris-super-wrapper {
    width: 100% !important;
    display: flex !important;
    justify-content: center !important;
    text-align: center !important;
    padding: 0 !important;
    margin: 0 auto !important;
  }
  #tetris-super-wrapper p, #tetris-super-wrapper div {
    margin: 0 !important;
    padding: 0 !important;
  }

  #game-container { 
    font-family: monospace; 
    background: #111 !important; 
    padding: 8px !important; 
    border-radius: 10px !important; 
    width: 260px !important; 
    max-width: 260px !important;
    box-sizing: border-box !important;
    display: flex !important;
    flex-direction: column !important;
    align-items: center !important;
    margin-top: 10px !important;
  }
  #score-board { font-size: 14px !important; color: #00ff00 !important; margin-bottom: 5px !important; width: 100%; text-align: center !important; }
  
  #canvas-wrapper {
    width: 240px !important;
    height: 400px !important;
    display: block !important;
    box-sizing: border-box !important;
    margin: 0 auto !important;
  }
  canvas { 
    border: 2px solid #00ff00 !important; 
    background-color: #000 !important; 
    box-shadow: 0 0 10px rgba(0, 255, 0, 0.3) !important;
    display: block !important;
    width: 240px !important;   
    height: 400px !important;  
    box-sizing: border-box !important;
  }
  
  #controls-wrapper { 
    display: none !important; 
  }

  /* НА СМАРТФОНАХ: Сжимаем поле и жестко притягиваем джойстик наверх */
  @media (max-width: 768px) { 
    #game-container {
      width: 100% !important;
      max-width: 340px !important; 
    }
    #canvas-wrapper {
      width: 100% !important;
      height: auto !important;
      max-width: 175px !important; /* Оптимальное сжатие стакана на мобилках */
    }
    canvas {
      width: 100% !important;
      height: auto !important;
    }
    #controls-wrapper { 
      display: flex !important; 
      flex-direction: column !important;
      align-items: center !important;
      margin-top: 5px !important; /* Кнопки прижаты строго под стаканом */
      width: 100% !important;
      max-height: 240px !important; /* Защита от расползания вниз */
    } 
  }

  /* Стили крестовины */
  .dpad-row {
    display: flex !important;
    justify-content: center !important;
    width: 100% !important;
    margin: 0 !important;
    padding: 0 !important;
  }
  .dpad-btn, .dpad-empty {
    width: 105px !important;  
    height: 85px !important; 
    box-sizing: border-box !important;
    margin: 2px !important;
  }
  .drop-btn {
    width: 324px !important;
    height: 50px !important;
    font-size: 14px !important;
    font-weight: bold !important;
  }
  .dpad-btn { 
    font-size: 36px !important; 
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
  const canvas = document.getElementById('tetrisCanvas');
  const context = canvas.getContext('2d');
  const scoreElement = document.getElementById('score');
  const linesElement = document.getElementById('lines');

  context.scale(24, 24);

  function arenaSweep() {
    let rowCount = 1;
    outer: for (let y = arena.length - 1; y > 0; --y) {
      for (let x = 0; x < arena[y].length; ++x) {
        if (arena[y][x] === 0) {
          continue outer;
        }
      }
      const row = arena.splice(y, 1).fill(0);
      arena.unshift(row);
      ++y;

      score += rowCount * 100;
      lines += 1;
      rowCount *= 2;
    }
    scoreElement.innerText = score;
    linesElement.innerText = lines;
  }

  function collide(arena, player) {
    const m = player.matrix;
    const o = player.pos;
    for (let y = 0; y < m.length; ++y) {
      for (let x = 0; x < m[y].length; ++x) {
        if (m[y][x] !== 0) {
          if (arena[y + o.y] === undefined || 
              arena[y + o.y][x + o.x] === undefined || 
              arena[y + o.y][x + o.x] !== 0) {
            return true;
          }
        }
      }
    }
    return false;
  }

  function createMatrix(w, h) {
    const matrix = [];
    while (h--) {
      matrix.push(new Array(w).fill(0));
    }
    return matrix;
  }

  function createPiece(type) {
    if (type === 'T') return [[0, 1, 0], [1, 1, 1], [0, 0, 0\|0, 1, 0], [1, 1, 1], [0, 0, 0]];
    if (type === 'O') return [[2, 2], [2, 2\|2, 2], [2, 2]];
    if (type === 'L') return [[0, 0, 3], [3, 3, 3], [0, 0, 0\|0, 0, 3], [3, 3, 3], [0, 0, 0]];
    if (type === 'J') return [[4, 0, 0], [4, 4, 4], [0, 0, 0\|4, 0, 0], [4, 4, 4], [0, 0, 0]];
    if (type === 'I') return [[0, 5, 0, 0], [0, 5, 0, 0], [0, 5, 0, 0], [0, 5, 0, 0\|0, 5, 0, 0], [0, 5, 0, 0], [0, 5, 0, 0], [0, 5, 0, 0]];
    if (type === 'S') return [[0, 6, 6], [6, 6, 0], [0, 0, 0\|0, 6, 6], [6, 6, 0], [0, 0, 0]];
    if (type === 'Z') return [[7, 7, 0], [0, 7, 7], [0, 0, 0\|7, 7, 0], [0, 7, 7], [0, 0, 0]];
  }

  function draw() {
    context.fillStyle = '#000';
    context.fillRect(0, 0, canvas.width, canvas.height);
    drawMatrix(arena, {x: 0, y: 0});
    drawMatrix(player.matrix, player.pos);
  }

  const colors = [null, '#00ff00', '#00ee00', '#00dd00', '#00cc00', '#00bb00', '#00aa00', '#009900'];

  function drawMatrix(matrix, offset) {
    if (!matrix) return;
    matrix.forEach((row, y) => {
      row.forEach((value, x) => {
        if (value !== 0) {
          context.fillStyle = colors[value];
          context.fillRect(x + offset.x, y + offset.y, 1, 1);
          context.strokeStyle = '#111'; 
          context.lineWidth = 0.05;
          context.strokeRect(x + offset.x, y + offset.y, 1, 1);
        }
      });
    });
  }

  function merge(arena, player) {
    player.matrix.forEach((row, y) => {
      row.forEach((value, x) => {
        if (value !== 0) {
          arena[y + player.pos.y][x + player.pos.x] = value;
        }
      });
    });
  }

  function playerDrop() {
    player.pos.y++;
    if (collide(arena, player)) {
      player.pos.y--;
      merge(arena, player);
      playerReset();
      arenaSweep();
    }
    dropCounter = 0;
  }

  function playerHardDrop() {
    while (!collide(arena, player)) {
      player.pos.y++;
    }
    player.pos.y--;
    merge(arena, player);
    playerReset();
    arenaSweep();
    dropCounter = 0;
  }

  function playerMove(dir) {
    player.pos.x += dir;
    if (collide(arena, player)) {
      player.pos.x -= dir;
    }
  }

  function playerReset() {
    const pieces = 'ILJOTSZ';
    player.matrix = createPiece(pieces[pieces.length * Math.random() | 0]);
    player.pos.y = 0;
    player.pos.x = Math.floor(arena[0].length / 2) - Math.floor(player.matrix[0].length / 2);
    
    if (collide(arena, player)) {
      alert('Игра окончена! Счёт: ' + score);
      arena.forEach(row => row.fill(0));
      score = 0;
      lines = 0;
      scoreElement.innerText = score;
      linesElement.innerText = lines;
    }
  }

  function playerRotate(dir) {
    const pos = player.pos.x;
    let offset = 1;
    rotate(player.matrix, dir);
    while (collide(arena, player)) {
      player.pos.x += offset;
      offset = -(offset + (offset > 0 ? 1 : -1));
      if (offset > player.matrix[0].length) {
        rotate(player.matrix, -dir);
        player.pos.x = pos;
        return;
      }
    }
  }

  function rotate(matrix, dir) {
    for (let y = 0; y < matrix.length; ++y) {
      for (let x = 0; x < y; ++x) {
        [matrix[x][y], matrix[y][x]] = [matrix[y][x], matrix[x][y]];
      }
    }
    if (dir > 0) {
      matrix.forEach(row => row.reverse());
    } else {
      matrix.reverse();
    }
  }

  let dropCounter = 0;
  let dropInterval = 1000; 
  let lastTime = 0;

  function update(time = 0) {
    const deltaTime = time - lastTime;
    lastTime = time;
    dropCounter += deltaTime;
    if (dropCounter > dropInterval) {
      playerDrop();
    }
    draw();
    requestAnimationFrame(update);
  }

  const arena = createMatrix(10, 20);
  const player = { pos: {x: 0, y: 0}, matrix: null };

  document.addEventListener('keydown', event => {
    if (event.keyCode === 37) playerMove(-1);
    else if (event.keyCode === 39) playerMove(1);
    else if (event.keyCode === 40) playerDrop();
    else if (event.keyCode === 38) playerRotate(1);
    else if (event.keyCode === 32) playerHardDrop();
  });

  function handleDpad(code) {
    if (code === 37) playerMove(-1);
    if (code === 39) playerMove(1);

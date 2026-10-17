---
{"dg-publish":true,"permalink":"/ib-12/testovye-fajly-ne-vhodit/test-files/","dg-note-properties":{}}
---

# 📝 Персональный трекер ДЗ | ИБ-12

_Каждый может отмечать галочками свои задачи. Progress сохраняется на твоём устройстве!_

<div id="homework-tracker">
  <!-- Матан -->
  <h3>📐 Математический анализ</h3>
  <label class="todo-item"><input type="checkbox" id="matan-1"> <span><b>БДЗ</b> — В процессе выполнения</span></label><br>
  <label class="todo-item"><input type="checkbox" id="matan-2"> <span>Подготовка к КР</span></label><br>

  <!-- Физика -->
  <h3>⚖️ Физика (Механика)</h3>
  <label class="todo-item"><input type="checkbox" id="fiz-1"> <span><b>Лабораторная работа №2</b> — Оформить отчёт</span></label><br>
  <label class="todo-item"><input type="checkbox" id="fiz-2"> <span>Подготовка к 2 части КР-1</span></label><br>

  <!-- Линал -->
  <h3>🗺️ Линейная алгебра и геометрия</h3>
  <label class="todo-item"><input type="checkbox" id="linal-1"> <span><b>БДЗ</b> — Посмотреть варианты задач</span></label><br>

  <!-- Инфа -->
  <h3>💻 Информатика</h3>
  <label class="todo-item"><input type="checkbox" id="info-1"> <span>Все лабы до 4-й лабораторной включительно</span></label><br>

  <!-- Англ -->
  <h3>🇬🇧 Иностранный язык</h3>
  <label class="todo-item"><input type="checkbox" id="eng-1"> <span>Принести текст на подпись (до конца октября)</span></label><br>
  <label class="todo-item"><input type="checkbox" id="eng-2"> <span>Номер 20, 22 + теория к паре</span></label><br>

  <!-- ОРГ -->
  <h3>🇷🇺 Основы российской государственности</h3>
  <label class="todo-item"><input type="checkbox" id="org-1"> <span>Презентация к защите</span></label><br>
</div>

<style>
  #homework-tracker h3 { margin-top: 20px; margin-bottom: 10px; }
  .todo-item { display: inline-block; margin: 4px 0; cursor: pointer; font-size: 16px; }
  .todo-item input[type="checkbox"] { margin-right: 8px; transform: scale(1.1); cursor: pointer; }
  .todo-item input:checked + span { text-decoration: line-through; color: #888888; }
</style>

<script>
  document.addEventListener("DOMContentLoaded", function() {
    const checkboxes = document.querySelectorAll('#homework-tracker input[type="checkbox"]');
    
    // Загружаем сохраненный статус из памяти браузера
    checkboxes.forEach(box => {
      const savedStatus = localStorage.getItem(box.id);
      if (savedStatus === "true") {
        box.checked = true;
      }
      
      // Слушаем клики: при изменении сохраняем статус
      box.addEventListener('change', function() {
        localStorage.setItem(box.id, box.checked);
      });
    });
  });
</script>

---

## 📓 Мои личные заметки к ДЗ
_Этот блокнот персональный. Текст видишь только ты, и он автоматически сохраняется в твоём браузере!_

<div id="personal-notebook-container">
  <textarea 
    id="personal-notebook" 
    placeholder="Впиши сюда свои дедлайны, номера задач по вариантам или ссылки на отчёты... Программа всё запомнит!"
  ></textarea>
  <div id="notebook-status">✓ Все изменения сохранены локально</div>
</div>

<style>
  #personal-notebook-container {
    width: 100% !important;
    max-width: 640px;
    margin: 15px auto !important;
    box-sizing: border-box;
    text-align: left !important;
  }
  #personal-notebook {
    width: 100% !important;
    height: 150px !important;
    background: #1e1e1e !important;
    color: #00ff00 !important;
    border: 2px solid #00ff00 !important;
    border-radius: 8px !important;
    padding: 12px !important;
    font-family: monospace !important;
    font-size: 14px !important;
    line-height: 1.5 !important;
    resize: vertical !important;
    box-sizing: border-box !important;
    box-shadow: 0 0 10px rgba(0, 255, 0, 0.1);
  }
  #personal-notebook:focus {
    outline: none !important;
    box-shadow: 0 0 15px rgba(0, 255, 0, 0.3) !important;
  }
  #notebook-status {
    font-size: 12px !important;
    color: #888888 !important;
    margin-top: 5px !important;
    font-style: italic !important;
    text-align: right !important;
    transition: opacity 0.3s ease;
    opacity: 0; /* Прячется, когда пользователь не пишет */
  }
</style>

<script>
  document.addEventListener("DOMContentLoaded", function() {
    const notebook = document.getElementById("personal-notebook");
    const status = document.getElementById("notebook-status");
    let saveTimeout;

    // 1. Загружаем сохраненный ранее текст
    const savedText = localStorage.getItem("ib12-personal-notes");
    if (savedText) {
      notebook.value = savedText;
    }

    // 2. Отслеживаем ввод текста и сохраняем с авто-дебаунсом (чтобы не спамить память)
    notebook.addEventListener("input", function() {
      status.style.opacity = "0.5";
      status.innerText = "Печатает...";

      clearTimeout(saveTimeout);
      saveTimeout = setTimeout(function() {
        localStorage.setItem("ib12-personal-notes", notebook.value);
        status.innerText = "✓ Все изменения сохранены локально";
        status.style.opacity = "1";
        
        // Плавно прячем надпись через 2 секунды после сохранения
        setTimeout(() => {
          if (status.innerText === "✓ Все изменения сохранены локально") {
            status.style.opacity = "0";
          }
        }, 2000);
      }, 500); // Сохраняем через 500мс после того, как пользователь перестал нажимать клавиши
    });
  });
</script>

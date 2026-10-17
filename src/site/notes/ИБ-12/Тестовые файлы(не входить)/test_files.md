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

---
---

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
  /* Взламываем рамки узкой статьи: делаем поле огромным на 92% ширины всего экрана */
  #personal-notebook-container {
    width: 92vw !important;
    max-width: 92vw !important;
    position: relative !important;
    left: 50% !important;
    transform: translateX(-50%) !important; /* Ровное центрирование без слома верстки */
    margin: 20px 0 !important;
    box-sizing: border-box !important;
    text-align: left !important;
    display: block !important;
  }
  
  #personal-notebook {
    width: 100% !important;      /* Изначально на всю ширь огромного контейнера */
    height: 250px !important;    /* Увеличили начальную высоту, чтобы поле было массивным */
    background: #1e1e1e !important;
    color: #00ff00 !important;
    border: 2px solid #00ff00 !important;
    border-radius: 10px !important;
    padding: 18px !important;    /* Больше внутренних отступов */
    font-family: monospace !important;
    font-size: 16px !important;   /* Увеличили размер шрифта для масштабности */
    line-height: 1.6 !important;
    resize: vertical !important; 
    box-sizing: border-box !important;
    box-shadow: 0 0 20px rgba(0, 255, 0, 0.15);
  }
  
  #personal-notebook:focus {
    outline: none !important;
    box-shadow: 0 0 25px rgba(0, 255, 0, 0.35) !important;
  }
  
  #notebook-status {
    font-size: 13px !important;
    color: #888888 !important;
    margin-top: 6px !important;
    font-style: italic !important;
    text-align: right !important;
    transition: opacity 0.3s ease;
    opacity: 0;
  }

  /* НА СМАРТФОНАХ: Чуть поджимаем под экраны мобилок, чтобы не прилипало к краям дисплея */
  @media (max-width: 768px) {
    #personal-notebook-container {
      width: 95vw !important;
      max-width: 95vw !important;
    }
    #personal-notebook {
      font-size: 15px !important;
      height: 220px !important;
    }
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

    // 2. Отслеживаем ввод текста и сохраняем локально
    notebook.addEventListener("input", function() {
      status.style.opacity = "0.5";
      status.innerText = "Печатает...";

      clearTimeout(saveTimeout);
      saveTimeout = setTimeout(function() {
        localStorage.setItem("ib12-personal-notes", notebook.value);
        status.innerText = "✓ Все изменения сохранены локально";
        status.style.opacity = "1";
        
        setTimeout(() => {
          if (status.innerText === "✓ Все изменения сохранены локально") {
            status.style.opacity = "0";
          }
        }, 2000);
      }, 500);
    });
  });
</script>

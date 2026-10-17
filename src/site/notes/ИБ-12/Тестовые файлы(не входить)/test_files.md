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

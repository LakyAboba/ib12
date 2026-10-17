---
{"dg-publish":true,"permalink":"/ib-12/testovye-fajly-ne-vhodit/test3/","dg-note-properties":{}}
---

# 🧠 Проверка на прочность ИБ-12 | DOS DOOM (1993)

_Управление: Стрелочки или WASD — движение, Ctrl — огонь, Пробел — открыть дверь/рычаг, Alt + Стрелочки — стрейф (боковой шаг)._

<!-- Инкапсулированный контейнер для изоляции от темы Quartz -->
<div align="center" style="width: 100%; text-align: center !important; margin: 0 auto !important;">

  <div id="doom-container">
    <!-- Окно эмулятора js-dos -->
    <div id="jsdos-player"></div>
  </div>

</div>

<!-- Подключаем оригинальные библиотеки эмулятора DOSBox через защищенный CDN -->
<link rel="stylesheet" href="https://js-dos.com">
<script src="https://js-dos.com"></script>

<style>
  /* Фиксируем скролл на ПК, чтобы экран не прыгал при нажатии на Пробел или Стрелочки */
  @media (min-width: 769px) {
    html, body, .quartz-page, main, article {
      overflow: hidden !important;
      height: 100% !important;
    }
  }

  #doom-container {
    width: 640px;
    height: 400px;
    background: #000;
    border: 3px solid #ff0000;
    border-radius: 8px;
    box-shadow: 0 0 25px rgba(255, 0, 0, 0.4);
    margin: 15px auto !important;
    overflow: hidden;
  }

  #jsdos-player {
    width: 100%;
    height: 100%;
  }

  /* Адаптивность для мобилок, чтобы окно пропорционально сжималось */
  @media (max-width: 768px) {
    #doom-container {
      width: 100% !important;
      height: 240px !important;
    }
  }
</style>

<script>
  // Запуск DOSBox и автоматическое скачивание оригинального Shareware-архива DOOM
  document.addEventListener("DOMContentLoaded", function() {
    if (typeof Dos !== "undefined") {
      Dos(document.getElementById("jsdos-player"), {
        wdosboxUrl: "https://js-dos.com",
        cycles: 15000, // Оптимальная скорость процессора 486 DX2 для плавной игры
        autostart: true
      }).then((runtime) => {
        // Скачиваем архив с классическим DOOM1.WAD с надежного CDN js-dos
        runtime.bootstrap("https://js-dos.com").then(() => {
          console.log("DOOM успешно загружен в ИБ-12!");
        });
      });
    }
  });
</script>

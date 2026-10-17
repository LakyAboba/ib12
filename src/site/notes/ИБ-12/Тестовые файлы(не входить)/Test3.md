---
{"dg-publish":true,"permalink":"/ib-12/testovye-fajly-ne-vhodit/test3/","dg-note-properties":{}}
---

# 📺 Полезные видеоматериалы | ИБ-12

_Ниже пример того, как видео с YouTube будет аккуратно отображаться на компьютерах и смартфонах без косяков с версткой._

<div id="video-super-wrapper">
  <div id="video-container">
    <[!-- Сюда вставляется код, скопированный с YouTube -](https://www.youtube.com/watch?v=AuKR2fQbMBk)->
    <iframe 
      src="https://youtube.com" 
      title="YouTube video player" 
      frameborder="0" 
      allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" 
      allowfullscreen>
    </iframe>
  </div>
</div>

<style>
  #video-super-wrapper {
    width: 100% !important;
    max-width: 640px; /* Максимальная ширина видео на ПК */
    margin: 15px auto !important;
    padding: 0 10px;
    box-sizing: border-box;
  }
  
  /* Делаем контейнер адаптивным (соотношение сторон 16:9) */
  #video-container {
    position: relative;
    padding-bottom: 56.25%; /* 16:9 */
    height: 0;
    overflow: hidden;
    border: 2px solid #00ff00; /* Хакерская зеленая рамка в стиле ИБ */
    border-radius: 8px;
    background: #000;
    box-shadow: 0 0 15px rgba(0, 255, 0, 0.2);
  }
  
  #video-container iframe {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
  }
</style>

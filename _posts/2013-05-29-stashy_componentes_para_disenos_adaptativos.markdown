---
layout: post
title: Stashy, componentes para diseños adaptativos
date: '2013-05-29 13:10:41'
tags:
- stashy
categories:
- Frontend
---


Hace poco he publicado un nuevo proyecto en Github llamado Stashy en el que trataré de ir añadiendo componentes y utilidades que ayuden con diseños adaptativos.

El objetivo de [Stashy](https://github.com/yagopv/stashy "Stashy") es crear una librería en la que se puedan encontrar componentes (CSS + JavaScript) que faciliten el diseño de sitios web adaptativos y que se visualicen correctamente en cualquier tipo de dispositivo.

Los componentes incluidos se podrán usar de manera independiente sin necesidad de incluir una referencia a la librería completa. Las dependencias a incluir en cada caso se pueden comprobar en la [documentación del proyecto](http://stashy.azurewebsites.net "Stashy"). La mejor forma de visualizar los documentos del proyecto y comprobar cómo funcionan los distintos componentes es acudiendo a las secciones **Playground** de cada página o bien adaptando el tamaño de la ventana de tu navegador para ver como se acoplan a diferentes anchos.

El objetivo será ir incluyendo más y más componentes según vaya descubriéndolos y mejorar los actuales según las características de los navegadores vayan avanzando.

Los navegadores soportados, en principio y a falta de más testeo son todos los navegadores actuales. He hecho comprobaciones en Firefox (últimas versiones), IE9-10, Chrome (últimas versiones), Safari, iOs (5.0+), Android (4+). También me he aventurado :) con IE8 y funciona incluyendo algún extra como htmlshiv (soporte para etiquetas html5) y respond.js (soporte para media queries). No soy tan valiente como para comprobar nada en IE7 y 6.

El proyecto se licencia baja la licencia MIT. Por supuesto, si alguien está interesado en contribuir, yo encantado.

<span style="font-size: 1em; line-height: 1.6em;">Actualmente, se incluye lo siguiente:</span>

- **OffCanvas** : Para crear layouts que se adaptan lateralmente al tamaño de la pantalla. Los menus se descubren pulsando botones o mediante deslizamiento táctil. Usa la técnica de márgenes negativos y transiciones CSS3.
- **Flyout** : Menu lateral que se oculta o muestra pulsando un botón o mediante desplazamiento táctil. Usa transformaciones 3D CSS3 por lo que en dispositivos móviles el desplazamiento lateral es bastante fluido.
- **Toggle** : Para crear el menú principal de un página que se desplaza hacia abajo al pulsar un botón.
- **Sticky** : Estructura para fijar los pies de página al final de la misma independientemente de si los contenidos lo alcanzan.
- **ShowMeMore** : Para ocultar items en una lista y mostrar un botón que me permita “mostrar más”
- **FocalPoint** : Crear imágenes adaptativas que además focalicen ciertos puntos de la misma al mostrarse en tamaños de pantalla reducidos.
- **Loader** : Loader CSS3 puro al estilo del nuevo Window 8 / Phone
- **Slider** : Para crear carruseles o similares que se desplacen mediante controles o de forma táctil
- **Refresh** : <span>Control que permite refrescar una parte de la página mediante un movimiento táctil de arrastre hacia abajo</span>
- <span>**ElasticVideo** : <span>Adapta videos al tamaño del contenedor padre</span></span>
- <span><span>**ElasticText** : <span>Adapta texto al tamaño de la pantalla</span></span></span>
- <span><span><span>**List** : <span>Listas adaptativas</span></span></span></span>

Espero que le sirva a alguien … de eso se trata siempre ;)

Hasta pronto!!



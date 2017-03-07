---
layout: post
title: Scripts simplemente imprescindibles para la compatibilidad con navegadores
  antiguos
date: '2012-05-24 09:33:30'
tags:
- scripts
---


Con la llegada de los nuevos estándares HTML5 y CSS3, se plantean nuevos problemas para los desarrolladores Web en cuanto a la compatibilidad de nuevos proyectos de desarrollo o actualizaciones de portales en funcionamiento con navegadores que no soportan dichos formatos como por ejemplo Internet Explorer 8 y anteriores.

Tal vez no merezca la pena hacer un esfuerzo notable en que nuestros sitios Web se visualicen de la misma manera en los nuevos navegadores que en los antiguos ya que poco a poco los usuarios se van actualizando y posiblemente sería mejor sería una estrategia en la que nuestros componentes se vayan degradando visualmente o funcionalmente en proporción al soporte que ofrezca el navegador que estemos usando. Esta estrategia se conoce como “progressive enhancement” y podemos encontrar en la red multitud de artículos que nos hablan de qué es y cómo emplearla.

Independientemente de esto, hay una serie de scripts de muy fácil uso que nos permiten con un esfuerzo mínimo retrocompatibilizar nuestros desarrollos con navegadores anteriores y el objetivo de este post es mencionar algunos de ellos


## html5shiv

**[html5shiv](https://github.com/aFarkas/html5shiv "html5shiv")** hace que se reconozcan los nuevos tags de HTML5 en navegadores que no lo soportan. Simplemente incluyendo **html5shiv** al final de nuestros scripts ya estamos haciendo que nuestra página web se visualice correctamente en gran cantidad de navegadores en los que no lo hacía como por ejemplo Internet Explorer 6 – 9, Safari 4.x (e iPhone 3.x) o Firefox 3.x.


## Respond.js

Cuando hablamos de diseño adaptativo (*responsive design*), no podemos obviar el poder de las *media queries* y su facilidad de uso para hacer nuestro diseño flexible y compatible con multitud de dispositivos y pantallas.

**[Respond.js](https://github.com/scottjehl/Respond "respond.js")** hace que nuestras *media queries* se ejecuten correctamente en navegadores que no las soportan como podrían ser los comentados anteriormente. Actualmente sólo soporta las *media queries* **min-width** y **max-width** que, aunque en muchos casos pueden ser suficientes, en otros necesitaremos un soporte de mayor peso. Hay otros scripts más completos que sí que soportan más tipos de *media queries*, pero **Respond.js** tiene la ventaje de ser muy liviano y en la mayoría de las situaciones suficiente. Si necesitamos un soporte ampliado podemos acudir a otros plugins como por ejemplo [este](http://code.google.com/p/css3-mediaqueries-js/ "css3-mediaqueries-js") que además se recomienda desde la propia web de **Respond.js**.

Tal y como en el caso de **html5shiv**, **Respond.js** funciona simplemente añadiendolo al final de nuestros scripts.


## History.js

[**History.js**](https://github.com/balupton/History.js/ "History.js") permite usar la History API de HTML5 en navegadores que no la implementan y así permitir el uso de url´s amigables y facilitar el desarrollo de aplicaciones ajax compatibles con el máximo de navegadores posible. Hay una versión para navegadores HTML5 y otra para HTML5 + HTML4 que se basa en el uso de hashs.


## Modernizr

No voy a descubrir [**Modernizr**](http://modernizr.com/ "Modernizr")aquí porque su uso es sin duda ya generalizado. No hay más que ver su inclusión por defecto en los proyectos web de las últimas versiones de **Visual Studio.** **Modernizr** permite detectar características de HTML5 y CSS3 en el navedagor que abre la página web y actuar en consecuencia. Además hay múltiples plugins que permiten solucionar la situación cuando un navegador no soporta una característica (*polyfills*). [En este enlace](https://github.com/Modernizr/Modernizr/wiki/HTML5-Cross-browser-Polyfills "Polyfills") se puede acceder a una lista y además comprobar que el resto de scripts aqui comentados se incluyen en la misma para paliar la falta de las características HTML5 o CSS3 comentadas y se permiten cargar mediante la sencilla instruccion

{% highlight javascript %}
Modernizr.load({ test: Modernizr.history, nope: 'History.js' });
{% endhighlight %}

con esta instrucción estamos evaluando la existencia de la característica *history API* en el navegador y en caso de que no exista (*nope*) cargamos el script de **History.js**. *load()* tiene otras muchas opcciones para aplicar en caso de que sí detecte la caraccterística (*yep*), una vez cargada (*complete*)… etc.

En resumen, estos scripts facilitan sin duda el tedioso trabajo de compatibilizar y probar el funcionamiento de las aplicaciones web en múltiples navegadores. Hay muchos más … pero estos son los que yo conozco.

Hasta pronto!!

[<span>html5shiv</span>](http://code.google.com/p/html5shiv/)



---
layout: post
title: Cómo posicionarse sobre un elemento usando jquery
date: '2012-01-02 21:51:05'
---


Si necesitamos posicionarnos encima de un elemento en una página, cómo suele pasar al pulsar en un enlace o al cargar una nueva página y seguidamente ir a una sección concreta, podemos hacerlo de manera sencilla mediante el siguiente código:

$('html,body').animate( { scrollTop: $("elemento-sobre-el-que-me-posicionare").position().top }, { duration: 'slow', easing: 'swing' } );

Usando la función animate() de jquery puedo posicionarme encima de un elemento usando un selector. Puedo indicar la duración de la animación y el efecto a utilizar.

Por supuesto usando anchors hago lo mismo … pero así queda más bonito ![:)](http://www.yagoperez.com/wp-includes/images/smilies/simple-smile.png)

Hasta pronto!!



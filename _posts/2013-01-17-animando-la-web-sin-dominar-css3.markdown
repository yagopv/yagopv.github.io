---
layout: post
title: Animando la Web (...sin dominar CSS3...)
date: '2013-01-17 07:06:47'
---


El auge de CSS3 abre un nuevo mundo de posibilidades para los diseñadores permitiendo crear experiencias de usuario mucho más ricas de lo que estábamos acostumbrados a ver.

Es muy complicado dominar todos los frentes cuando se trata de encarar un proyecto web. <span style="font-size: 1em; line-height: 1.6em;">CSS, HTML, lenguajes servidor, javascript y todas sus librerías (jquery, underscores, backbone, knockout) … etc.  
</span>

Desde luego, no es tarea fácil, por lo que en muchos casos acabamos teniendo un mayor grado de conocimiento en el lado del cliente o bien en el lado del servidor.

Si eres mejor en una cosa no implica que no puedas usar muchas de las herramientas que te facilitarán la vida en la otra.

Me he encontrado con un par de proyectos en Github que desde luego ayudan una barbaridad a plasmar lo que pasa por tu imaginación

<span style="font-size: 1em; line-height: 1.6em;">Estas herramientas, aunque en el fondo trabajan con lo mismo (transiciones, transformaciones, animaciones CSS), en realidad son diferentes y totalmente complementarias de manera que c</span><span style="font-size: 1em; line-height: 1.6em;">ombinándolas pueden hacer que creemos una experiencia de usuario completa sin dominar en profundidad las nuevas propiedades de CSS3.</span>

Se trata de la librería de clases CSS [animate.css](http://daneden.me/animate/ "animate.css") y del plugin [jQuery Transit](http://ricostacruz.com/jquery.transit/ "jQuery Transit")

### Animate.css

Dice su autor …

```
animate.css is a bunch of cool, fun, and cross-browser animations for you to use in your projects. Great for emphasis, home pages, sliders, and general just-add-water-awesomeness.
```

… efectivamente, este proyecto nos ofrece una hoja de estilos que simplemente define una colección de clases que nos proporcionan diferentes efectos tan solo con aplicarlos a un elemento HTML.

```language-javascript
$('#tuelemento).addClass('animated bounceOutLeft');
```

<span style="font-size: 1em; line-height: 1.6em;">La clase .animated define la duración de la animación. Puedo añadir también la clase .hinge para definir una mayor duración. La clase .bounceOutLeft simplemente es una de las muchas animaciones que nos da por defecto la librería.

También podemos crear una descarga personalizada con las animaciones que nos interesan y así disminuir el tamaño de la hoja de estilos.</span>

<span style="font-size: 1em; line-height: 1.6em;">En mis pruebas he descubierto que usando ajax y aplicando directamente estas clases sobre los elementos HTML, podemos crear unos efectos bastante interesantes cada vez que se carga una nueva página de manera asíncrona creando una sensación de dinamismo en el contexto global de la aplicación (Al estilo Windows 8).</span>

### <span style="font-size: 1em; line-height: 1.6em;">jQuery Transit  
</span>

Esta librería javascript trae las transformaciones y transiciones de CSS3 a jQuery.

Las transformaciones nos las permite realizar usando la función css() de jQuery

```language-javascript
$('.box').css({ transform: 'rotate(40deg)' }); 
$('.box').css({ x: '90px', y: '10px', rotate: '-10deg' });
```

Las transiciones, las podemos lanzar de forma muy sencilla mediante la función ***transition()***

```language-javascript
$('...').transition(options, [duration], [easing], [complete])
```
Como se puede apreciar, es muy sencillo de usar

Otra de las cosas que me gusta de ambas librerías es lo bien documentadas que están, indicándonos todas las posibilidades que tenemos con sus respectivos ejemplos. Esto hace que la curva de aprendizaje sea muy pequeña. Simplemente añadimos los componentes a nuestra página y cuando queremos usar algo, abrimos la página del producto, buscamos lo que pretendemos hacer, y lo escribimos en nuestro código.

También me gusta lo complementarias que son, ya que [animate.css](http://daneden.me/animate/ "animate.css") hace uso intensivo de la propiedad ***animate***, mientras que [jQuery Transit](http://ricostacruz.com/jquery.transit/ "jQuery Transit") lo hace de ***transform*** y ***transition***.

<span style="font-size: 1em; line-height: 1.6em;">Genial verdad?</span>

Hasta pronto!!



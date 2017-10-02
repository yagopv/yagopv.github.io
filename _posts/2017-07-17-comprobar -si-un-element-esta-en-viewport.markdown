---
layout: post
title: Comprobar si un elemento está en el viewport
date: '2017-07-17 13:00:00'
tags:
- jquery
categories:
- JavaScript
---

# Comprobar si un elemento está en el viewport

Muchas veces necesitamos realizar cálculos para comprobar si un elemento de la página está en el viewport del usuario. Con la siguiente función podemos comprobarlo

```javascript
function inView(element, fullHeightInView) {
    var $element = $(element);
    var $window = $(window);
    var docViewTop = $window.scrollTop();
    var docViewBottom = docViewTop + $window.height();
    var elemTop = $element.offset().top;
    var elemBottom;
    if (fullHeightInView) {
        elemBottom = elemTop + $element.height();
    } else {
        elemBottom = elemTop;
    }
    
    return ((elemBottom >= docViewTop) &&
        (elemTop <= docViewBottom) &&
        (elemBottom <= docViewBottom) &&
        (elemTop >= docViewTop));
}
```

A la función le pasaremos el elemento y un parámetro opcional para evaluar si el elemento está completamente en la vista o bien sólo una parte.

También es útil saber cual es la posición relativa de dicho elemento en el viewport. Para eso podríamos utilizar la siguiente función

```javascript
function getViewportOffset(element) {
    var $element = $(element),
        $window = $(window);
    var scrollLeft = $window.scrollLeft(),
        scrollTop = $window.scrollTop(),
        offset = $element.offset
    return {
        left: offset.left - scrollLeft,
        top: offset.top - scrollTop
    };
}
```

Esta función nos daría un objeto con la distancia `left` y `top` a la parte izquierda y al inicio de la página respectivamente.

Combinando estas dos funciones podemos hacer interesantes cálculos como por ejemplo comprobar el número de pixeles que un elemento es visible en la pantalla al realizar scroll

```javascript
$(window).scroll(function () {
    var target = document.querySelector('.my-selector'), //El elemento que quiero testear
        windowHeight = $(window).height();

    //Si el elemento es visible al realizar scroll
    if (inView(target)) {
        var offset = getViewportOffset(target);
        //Si el elemento aparece por la parte de abajo de la ventana
        if (windowHeight > offset.top) {
            //Desplazo otro elemento de la página los mismos pixeles que el 
            //element .my.selector ha entrado en el viewport al realizar el scroll
            $('.element-selector').css({
                top: (windowHeight - offset.top)
            });
        }
    }
});
```

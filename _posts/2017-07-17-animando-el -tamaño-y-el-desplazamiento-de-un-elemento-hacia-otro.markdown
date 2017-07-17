---
layout: post
title: Animando el tamaño y desplazamiento de un elemento hacia otro
date: '2017-07-17 13:00:00' 
tags:
- jquery
categories:
- JavaScript
---

# Animando el tamaño y desplazamiento de un elemento hacia otro

Con la siguiente función podemos animar el desplazamiento de un elemento por la pantalla utilizando un elemento virtual que creamos al iniciarse la animación y eliminamos al llegar a su destino.

Por el camino cambiaremos el tamaño del elemento para que se corresponda con el de destino.

```javascript
var $source = $('.source'),
    $target = $('.target'),
    $start = $($source)[0]).clone(),
    $end = $target[0],
    rectStart = $source[0].getBoundingClientRect(),
    rectEnd = $end.getBoundingClientRect();

$start.css({ 
    position: 'absolute',
    top: rectStart.top + window.scrollY, 
    left: rectStart.left,
    bottom: rectStart.bottom,
    height: rectStart.height,
    width: rectStart.width,
    right: rectStart.right,
    opacity: 0
}).appendTo($('body'));
$start.animate({
    top: rectEnd.top + window.scrollY, 
    left: rectEnd.left,
    bottom: rectEnd.bottom,
    height: rectEnd.height,
    width: rectEnd.width,
    right: rectEnd.right,
    opacity: 1
},{
    duration: 666,
    complete: function () {
        $start.remove();
}
}); 
```

En el siguiente Codepen podemos ver un ejemplo


<div>
    <p data-height="600" data-theme-id="0" data-slug-hash="zzXbxY" data-default-tab="js,result" data-user="yagopv" data-embed-version="2" data-pen-title="Element source animation to target" class="codepen">See the Pen <a href="https://codepen.io/yagopv/pen/zzXbxY/">Element source animation to target</a> by Yago Pérez Vázquez (<a href="https://codepen.io/yagopv">@yagopv</a>) on <a href="https://codepen.io">CodePen</a>.</p>
    <script src="https://production-assets.codepen.io/assets/embed/ei.js"></script> 
</div>


---
layout: post
title: El patrón Observer con jQuery
date: '2014-04-10 14:38:48'
tags:
- Patterns
catgories:
- JavaScript
---


El patrón **Observer** nos permite subscribirnos a objetos y ser notificados cuando algo de interés ocurre en nuestra aplicación.

Realmente jQuery ya está preparado para este tipo de gestión desde hace tiempo a través del sistema de eventos a los que me puedo suscribir a través de ***.on()*** y finalizar dicha suscripción a través de ***.off().***

<span style="line-height: 1.5em;">Pero aquí vamos a ver otra forma muy sencilla de implementar este mecanismo usando nuestras propias funciones y el objeto **callbacks**</span>

{% highlight javascript %}
var gestor = {}; 
jQuery.Gestor = function( id ) { 
    var callbacks, 
        tarea = id && gestor[ id ];

    if ( !tarea ) { 
        callbacks = jQuery.Callbacks(); 
        tarea = { 
            publish: callbacks.fire, 
            subscribe: callbacks.add, 
            unsubscribe: callbacks.remove 
        }; 
        if ( id ) { 
            gestor[ id ] = tarea; 
        } 
    } 
    return tarea; 
};
{% endhighlight %}

Con esto hemos creado un sencillo gestor de tareas que podemos usar de la siguiente forma … Nos suscribimos a cierto evento …

{% highlight javascript %}
$.Gestor("sumaNumeros").subscribe(function(a,b) {    
    console.log(a + b); 
}); 

$.Gestor("sumaNumeros").subscribe(function(a,b) {
    alert(a + b); 
});
{% endhighlight %}
… y a continuación podemos publicar la tarea a realizar cuando ocurra ese evento:

{% highlight javascript %}
$.Gestor("sumaNumeros").publish(3,4);
{% endhighlight %}

Esto haría que se escribiese en la consola y se lanzase una alerta mostrando un 7

Sencillo no?

Hasta pronto!!

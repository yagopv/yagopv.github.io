---
layout: post
title: Jugando con $.Deferred()
date: '2013-03-15 08:54:16'
tags:
- promises
---


Vamos a jugar un poco con $.Deferred() y ver las posibilidades que nos ofrece.

### $.Deferred. ¿Qué es?

El objeto deferred se introdujo en jQuery a partir de la versión 1.5 y provocó una importante mejora en las interfaces de programación asíncrona (y síncrona …) que se usaban hasta entonces. Es un objeto concatenable sobre el que se pueden registrar múltiples callbacks que se ejecutarán en función del resultado final de la deferred.

### ¿Como creamos uno?

Cuando creamos un objeto deferred mediante …

{% highlight javascript %}
var deferred = new $.Deferred(); 
//o bien 
var deferred = $.Deferred();
{% endhighlight %}

… tendremos disponible un objeto sobre el que podemos aplicar una serie de métodos que vamos a ver de forma general. Si se quieren repasar uno por uno se pueden revisar [en el api de jQuery](http://api.jquery.com/category/deferred-object/ "jQuery Deferred").

### ¿…y para qué me vale…?

Este objeto deferred puede estar en 3 estados:

```
pending: El objeto está en un estado incompleto
resolved: El objeto se ha resuelto
rejected: el objeto se ha rechazado
```

Cuando lo creamos, por supuesto, estará “pending”. Si llamados al método *resolve()* del objeto deferred lo pasaremos al estado “resolved”. Por el contrario, si llamamos al método *reject()* lo pasaremos al estado “rejected”.

Además, puedo adjuntarle una serie de callbacks que se ejecutarán en función del estado al que lo esté pasando. Por ejemplo, puedo usar el método *done()* para asociar un callback (o varios) que se ejecutarán cuando la deferred pase a “resolved”. Además puedo hacer lo mismo si en lugar de a “resolved” pasa a “rejected” usando el método *fail()*. Esto nos abre un mundo de posibilidades si sabemos usar estos objetos de forma inteligente. El objeto deferred puede pasar a “resolved” o “rejected” y cuando alcanza uno de estos estados su uso se da por finalizado.

Además de *done() y fail()* hay otros muchos más métodos que nos permiten hacer otras cosas como:

```
always(): Ejecutará sus callbacks independientemente del estado al que pase la deferred
progress(): Ejecutará callbacks cuando la deferred emita notificaciones de progreso mediante *notify()*
notify(): Hace que se llamen las callbaks de progress()
then(): puedo usar *then()* para asociar callbacks para done, fail o progress
```

También tenemos otra serie de métodos para averiguar el estado de la deferred, por ejemplo

```
isRejected(): Para saber si ha sido rechazada
isResolved(): Para averigar si se ha resuelto
state(): Para comprobar el estado actual
```
Como se puede ver, todos estos métodos nos pueden dar mucho juego.</span>

Comentar además que tanto *reject()* como *resolve()* admiten una lista de argumentos que se pasarán a los métodos *done()*, *fail()* … correspondientes.

### $.Deferred y ajax

<span style="font-size: 1em; line-height: 1.6em;">Si hemos trabajado algo con ajax a partir de la versión 1.5, habremos comprobado que ahora las llamadas se pueden hacer de una manera un poco diferente a lo que estábamos acostumbrados. Por ejemplo, si antes hacíamos …</span>

{% highlight javascript %}
$.getJSON("url", function() { 
    alert("Datos descargados!!") 
});
{% endhighlight %}

… ahora, aunque esto sigue funcionando, podemos hacer lo mismo de una forma más limpia mediante:

{% highlight javascript %}
$.getJSON("url").done(function() { 
    alert("Datos descargados!!") 
});
{% endhighlight %}

*done()* veíamos que era uno de los varios métodos que nos ofrece la interfaz del objeto $.Deferred y tiene como objetivo ejecutar el callback definido cuando el objeto $.Deferred se ha resuelto correctamente (resolved).

¿Porqué puedo hacer esto?, bueno, pues la razón es que a partir de la versíón 1.5 las llamadas ajax de jquery *$.ajax(), $.get(), $.getJSON(), $.post()*, …etc. devuelven un objeto *promise* que no es otra cosa que una interfaz de sólo lectura del objeto deferred, es decir, no tendré los métodos *resolve()* y *reject()* disponibles aunque sí podré asociar callbacks. Además resuelven la deferred en su interior (llama a *resolve()* si todo ha ido bien o bien a *reject()* si todo ha ido mal) cuando la llamada asíncrona finaliza. Por tanto tendremos la ejecución de los callbacks que toquen y hayamos definido mediante *done(), fail(), always(), then()* o lo que sea. El motivo de retornar una *promise* en lugar del propio objeto deferred es evitar la manipulación fuera del contexto de la petición ajax.

Comentar también que puedo asociar callbacks cuando quiera y no concatenarlos directamente en la llamada asíncrona, es decir

{% highlight javascript %}
var async = $.get("url"); 
async.done(function() { 
    alert("Todo bien!!"); 
}); 
async.fail(function() { 
    alert("Todo mal!!"); 
});
{% endhighlight %}

Hay otro método de gran utilidad en el api del objeto $.Deferred() y no es otro que ***$.when() ***que permite sincronizar la resolución o rechazo de varias deferred. Es decir, aplicandolo usando ajax:

{% highlight javascript %}
$.when($.get("url1"), $.get("url2"), $.get("url3"))  
    .done(function() { 
        alert("Todo bien!!"); 
    }) 
    .fail(function() { 
         alert("Todo mal!!");
    });
{% endhighlight %}

Lo que hace este código es ejecutar las tres peticiones ajax y cuando se resuelvan los deferreds de cada petición, si todos están “resolved” ejecutará el callback de *done()* y si alguno se rechaza el de *fail().*

### Jugando con todo esto sin ajax

Las deferred no sólo valen para usar con ajax. El api que nos ofrece el objeto $.Deferred no es otra cosa que la implementación de un patrón que podremos usar de la manera que mejor nos convenga. Por ejemplo, supongamos que quiero enviar datos contenidos en tres controles de tipo *input* que pertenecen a un formulario ajax cuando han pasado una validación y son todos correctos. Podríamos definir tres deferreds suponiendo que los campos sean *nombre, apellidos y direccion:*

{% highlight javascript %}
var nombre = $.Deferred(); 
var apellidos = $.Deferred(); 
var direccion = $.Deferred();
{% endhighlight %}

Después podríamos asociarles un callback para enviar el formulario mediante un $.post() cuando las tres deferreds pasen a “resolved” debido a que la validación de cada campo es correcta o bien asociarle que lance una alerta si alguna falla:

{% highlight javascript %}
// Defino callback 
var enviarFormulario = function() { 
    $.post("miurl", {
        nombre : nombre, 
        apellidos : apellidos, 
        direccion : direccion}); 
     } 
var tratarError = function() { 
    alert("Error en la validación!!"); 
} 
// Asocio el callback con las deferred mediante $.when() $.when(nombre, apellidos, direccion) 
    .done(enviarFormulario)
    .fail(tratarError);
{% endhighlight %}

Si paso las tres deferreds a “resolved” se enviará el formulario

{% highlight javascript %}
nombre.resolve(); 
apellidos.resolve(); 
direccion.resolve();
{% endhighlight %}

por el contrario, si rechazo alguna de ellas se mostrará la alerta en cuanto la rechazo y nunca se enviará el formulario ya que aunque las otras deferreds pasen a “resolved” la condición expresada mediante *$.when()* nunca podrá ser cierta:

{% highlight javascript %}
nombre.resolve(); 
apellidos.reject(); 
direccion.resolve();
{% endhighlight %}

### Retornando promises 

 Comentábamos anteriormente que las llamadas ajax de jquery retornaban objetos promise en lugar de deferreds y que estos eran interfaces de sólo lectura de un objeto deferred. El objetivo no es otro que “capar” la manipulación en el exterior del objeto deferred. Vamos a ver un ejemplo que lo ilustre:

{% highlight javascript %}
function wait() { 
    var deferred = $.Deferred(); 
    setTimeout(function() { 
        deferred.resolve(); 
    }, 5000); 
    return deferred.promise(); 
} 
wait().done(function() { 
    alert("He esperado 5 segundos!!"); 
});
{% endhighlight %}

Lo que estoy haciendo aquí es crear una función *wait()* que retorna una interfaz de sólo lectura de una deferred. En la función *wait()* espero 5 segundos y resuelvo la deferred. En el exterior, no puedo acceder a los métodos resolve y reject ya que se trata de una promise pero sí que puedo asociar callbacks de la manera habitual ya vista. Por tanto, si ejecutamos este código veremos una bonita alerta a los 5 segundos que nos muestra el texto “He esperado 5 segundos!!”.

Si tratamos de hacer algo como *wait().resolve()* fuera del cuerpo de la función veremos que nos indica que no existe un método *resolve()* en la promise.

Bueno, pues hasta aquí todo … espero que le sirva a alguien …

<span style="font-size: 1em; line-height: 1.6em;">Hasta pronto!!</span>



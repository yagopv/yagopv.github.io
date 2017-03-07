---
layout: post
title: Cómo crear un plugin con jQuery
date: '2012-01-02 13:44:16'
tags:
- plugins-y-widgets
---


Si usas jQuery y nunca has escrito un plugin, entonces no estas aprovechando una de las principales funciones que nos ofrece esta excelente libreria javascript.

jQuery proporciona un mecanismo para añadir métodos y funcionalidades extra al propio objeto $. La gran ventaja de los plugins es que nos aportan reusabilidad en diferentes partes de una aplicación o en diferentes aplicaciones.

En este post vamos a ver una de las muchas maneras de escribir un plugin utilizando jQuery.


## El plugin más básico

La manera más básica de construir un plugin es añadiendo propiedades al propio objeto jQuery

{% highlight javascript %}
(function( $ ) { 
    $.miPlugin = function(var1,var2,...,varN) { 
       // Añade la funcionalidad de tu plugin 
    }; 
})( jQuery );
{% endhighlight %}

Con esto ya tenemos un plugin listo para ser usado de la siguiente forma:

{% highlight javascript %}
<script src="MiPluginPath" type="text/javascript"></script> 

$(document).ready(function() {    
    $.miPlugin(var1,var2,...,varN); 
});
{% endhighlight %}

## Creando un plugin que itere sobre los elementos seleccionados del DOM

La forma de crear un plugin anterior es la más básica y desde luego no la más eficiente ya que la potencia de jquery reside en poder ejecutar los métodos sobre múltiples elementos del DOM usando selectores.

Esto lo podemos hacer fácilmente añadiendo una nueva propiedad al objeto jquery.fn, en el que el nombre de la propiedad será mi plugin jquery:

{% highlight javascript %}
(function ($) { 
    $.fn.miPlugin= function () { 
        return this.each(function () { 
        // Añade la funcionalidad del plugin 
        // Se aplicacará sobre todos los elementos seleccionados 
        }); 
    }; 
})(jQuery);
{% endhighlight %}`

Con el código anterior ya tenemos un plugin que iterará sobre los N elementos que se seleccionen con el selector. Lo usaremos así …

{% highlight javascript %}
<script src="MiPluginPath" type="text/javascript"></script> 
$(document).ready(function() {   
    $(".elemento").miPlugin(); 
});
{% endhighlight %}

## Encadenando llamadas a métodos

Escribiendo el plugin de esta forma se mantiene la capacidad de jquery de encadenar llamadas a métodos. Lo único que faltaría sería devolver el objeto mediante *return*. Si no lo hago, entonces no puedo encadenar las llamadas, pero si devuelvo el objeto puedo hacer cosas como:

{% highlight javascript %}
$(".elemento").miPlugin().fadeIn();
{% endhighlight %}

## Pasando opciones a mi nuevo plugin

Lo normal cuando creas un nuevo plugin es que necesites pasarle parámetros que se usarán en el interior del mismo para . Lo normal en estos casos es establecer las opciones por defecto en el propio plugin y en la llamada al mismo pasarle las opciones de usuario. Un vez el plugin esté ejecutándose se “mezclarán” las opciones del usuario con las por defecto:

{% highlight javascript %}
(function ($) {
    $.fn.miPlugin = function (opciones_usuario) {
        var opciones_por_defecto = {Texto: 'Opción de texto', ForeColor: 'red', BackColor: 'gray'};
        var opciones = $.extend(opciones_por_defecto, opciones_usuario);
        return this.each(function () {
            var objeto = $(this);
            var textoActual = objeto.text();
            var bgColorActual = objeto.css("background-color");
            var colorActual = objeto.css("color");
            objeto.hover(function () {
                objeto.text(opciones.Text);
                objeto.css("background-color", opciones.BackColor);
                objeto.css("color", opciones.ForeColor);
            }, function () {
                objeto.text(textoActual);
                objeto.css("background-color", bgColorActual);
                objeto.css("color", colorActual);
            });
        });
    }
})(jQuery);
{% endhighlight %}

Con el código anterior, hemos creado un plugin que modifica el texto, color y fondo de los elementos que hayamos seleccionado al hacer *hover* sobre el elemento. Cuando abandonamos el elemento, este volvera a tener su apariencia original.

Hasta aquí esta breve descripción de cómo crear plugins con la librería de jquery

Hasta otra!!



---
layout: post
title: AJAX con jquery
date: '2012-01-24 15:00:40'
tags:
- jquery
categories:
- JavaScript
---

Una de las funcionalidades más importantes que nos ofrece jquery es la simplificación de las llamadas ajax mediante una serie de métodos y eventos.

En este post se describen los principales métodos de jquery para interactuar con el servidor sin realizar recargas totales de página.

#### jQuery y AJAX

AJAX es el término que sirve de acrónimo para Aynchronous JavaScript And XML y que se refiere a la capacidad de enviar mediante código JavaScript y desde el cliente peticiones asíncronas al servidor de manera que este al recibirlas devuelva al cliente código HTML, scripts, JSON … etc.

jquery proporciona una serie de métodos para facilitar esta comunicación ya que la programación que habría que realizar manualmente para conseguir el mismo resultado (mediante el objeto XMLHttpRequest) es compleja y propensa a errores de codificación.

Existen otras librerías JavaScript con las que se puede conseguir el mismo resultado pero en este post se introducen las posibilidades que brinda jquery .

#### Métodos disponibles en la librería jQuery para peticiones ajax

**jQuery.ajax(opciones)**

Carga una página remota utilizando una petición http. Devuelve el objeto XMLHttpRequest que se crea internamente para realizar esta tarea

**load(url, datos, callback)**

Carga HTML desde mediante una url que recupera datos del servidor y los inyecta en el DOM

**jQuery.get(url, datos, callback, tipo)**

Carga una página HTML remota desde un servidor utilizando una petición de tipo GET

**jQuery.getJSON(url, datos, callback, tipo)**

Carga datos de tipo JSON utilizando una petición de tipo GET

**jQuery.getScript(url, callback)**

Carga y ejecuta una archivo de tipo script utilizando una petición del tipo GET contra un servidor

**jQuery.post(url, datos, callback, tipo)**

Carga una página desde un servidor remoto utilizando una petición de tipo POST

#### Eventos disponibles en la librería jquery para peticiones AJAX

**ajaxComplete(callback)**

Conecta una función que se ejecutará cuando se completa una petición AJAX

**ajaxError(callback)**

Conecta una función que se ejecutará cuando falle una petición AJAX

<span style="font-size: small;">**ajaxSend(callback)**</span>

Conectará una función que se ejecutará antes de enviar cada petición AJAX

**<span style="font-size: small;">ajaxStart(callback)</span>**

Conecta una función que se ejecutará cuando comience una petición ajax y no exista ninguna que ya se encuentre activa

**<span style="font-size: small;">ajaxStop(callback)</span>**

Conecta una función que se ejecutará cuando todas las peticiones AJAX activas terminen su ejecución

**<span style="font-size: small;">ajaxSuccess(callback)</span>**

Conecta una función que se ejecutará cada vez que una petición ajax termine con éxito

Entendemos como “callback” la función que se ejecutará una ver termine la ejecución de cada uno de los métodos anteriormente descritos y que se puede definir directamente en el método, como de costumbre con jquery, o bien pasar una función que ya exista al método.

Las funciones anteriores derivan en su mayoría de la función *$.ajax()*. Con *$.ajax()* podemos realizar lo mismo que con cualquiera de las indicadas a continuación. Simplemente están para abreviar la sintaxis de $.ajax() y para facilitar su lectura.  
Un ejemplo de llamada a la función $.ajax sería el siguiente

```javascript
$.ajax({ 
    url: "midominio/Post/GetLatestPosts", 
    type: "GET", 
    success: function(datos) { 
        $("#micontenedor").append(datos); 
    } 
});
```

Con la sintaxis anterior realizaríamos una petición de tipo GET contra la url indicada y los datos devueltos se añadirían a una div con id #micontenedor.

La lista completa de opciones de $.ajax() la podemos ver en http://api.jquery.com/jQuery.ajax/

$.get() o $.post() son funciones de más alto nivel que derivan de $.ajax() y que envían al servidor peticiones de tipo GET o POST respectivamente.

#### Descripción de las funciones AJAX más utilizadas

**jQuery.get()**

La función $.get() sólo tiene como obligatorio el parámetro url. Los demás son opcionales

```javascript
jQuery.get(url, [datos], [callback], [tipo])
/*
url: La dirección a la que se envía la petición de tipo GET
datos: Parejas clave/valor que se envían al servidor de la misma manera que cualquier petición de tipo GET
callback: función que se conectará a la función $.get() y que se ejecutará una vez finalizada la petición
tipo: El tipo de datos que se espera recibir del servidor (xml, HTML, script, json, jsonp o text)
*/
```

Como ejemplo, vemos a continuación una petición con $.get() que hace exactamente lo mismo que la petición vista anteriormente con $.ajax()

$.get({ url: "midominio/Post/GetLatestPosts", function(datos) { $("#micontenedor").append(datos); } });

**jQuery.post()**

$.get() se usa como método para peticiones de tipo GET. Como contrapunto también tenemos el método $.post() para peticiones de tipo POST

```javascript
jQuery.post(url, [datos], [callback], [tipo]);
```

Los parámetros son los mismos que el el caso de $.get() siendo sólo obligatoria la url para efectuar la petición.

Se suele usar para el envío de datos al servidor como si de un formulario se tratase enviando las parejas clave/valor necesarias.

Por ejemplo:

```javascript
$.post("midominio/CrearComentario/", { titulo:"Comentario de prueba", mensaje: "El mensaje de prueba" });
```

Mediante la llamada anterior enviamos una petición a una url mediante la cual se crea un comentario en la aplicación que se ejecute en el servidor. Ese comentario se crea mediante dos campos, titulo y mensaje.

**jQuery.getJSON()**

La funcion $.getJSON() recupera datos de tipo JSON del servidor. También se puede realizar lo mismo mediante $.get() y $.ajax() pero necesitaríamos parsear los datos devueltos. Esta función lo hace internamente por lo que tenemos acceso directamente al objeto devuelto que contendrá los datos.  
Jquery.getJSON(url, [datos], [callback])

Un ejemplo de usa de $.getJSON sería el siguiente:

```javascript
$.getJSON({"midominio/Users/GetUserNameJSON/112", function(datos) {
        $("#micontenedor").html("" + datos.Nombre + " " + datos.Apellido + ""); 
    } 
});
```

La anterior llamada recupera en formato JSON en Nombre y Apellido de un usuario de una aplicación ficticia.

Los datos devueltos en el objeto datos serán por ejemplo

```json
{"Id": 112,"Nombre":"Nombre de usuario","Apellido": "Apellido de usuario"}
```

**load()**

Este método es de gran utilidad para cargar datos HTML directamente desde el servidor reemplazando con ellos el contenido del elemento sobre el que se esté aplicando. La sintaxis es la siguiente:

```javascript
$(selector).load(url, [datos], [callback]);
```

Un ejemplo de utilización podría ser:

```javascript
$("#micontenedor").load("midominio/datos.html");
```

Con esta sentencia lo que haríamos es cargar el contenido de la página datos.html en el interior del elemento con id = micontenedor.

Como vemos es muy parecido a $.get() pero con una sintaxis más compacta. La ventaja que ofrece sobre $.get() es que permite cargar sólo un fragmento de la url sobre la que se hace la petición simplemente añadiendo un espacio y un selector después de la url a cargar, es decir:

```javascript
$("#micontenedor").load("midominio/datos.html #seccionacargar");
```

Con esto se cargaría dentro de #micontenedor los datos que se seleccionen mediante el selector #seccionacargar dentro de la página datos.html. Esto es de gran utilidad para recargas parciales de páginas por ejemplo.

Como indicar que se está procesando una petición asíncrona

Cuando hacemos llamadas ajax, es conveniente mostrar un texto o indicador de carga que avise al usuario de que una petición está en marcha. Con jquery es muy sencillo hacer este tipo de cosas:

```javascript
$("#cargando").ajaxStart(function() {
    $("#cargando").text("Cargando datos...");
});
$("#cargando").ajaxComplete(function() {
    $("#cargando").text("");
});
```

Es conveniente usar estos métodos, ya que actúan sobre la totalidad de las peticiones AJAX que se estén produciendo y no de una en una.

Conectando este tipo de eventos jquery a elementos del DOM podemos fácilmente indicar que una petición ajax se está produciendo en estos momentos.

Hasta aquí este breve resumen de las posibilidades de ajax combinadas con la librería jquery.



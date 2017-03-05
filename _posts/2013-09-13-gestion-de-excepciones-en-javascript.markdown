---
layout: post
title: Gestión de excepciones en JavaScript
date: '2013-09-13 04:45:16'
tags:
- errores-y-excepciones
---


El sistema de gestión de excepciones en JavaScript es similar al de otros lenguajes como por ejemplo C#.

Cuando se produce un error en una sentencia, se lanza una excepción y comienza la propagación del error entre la pila de llamadas a la función. Esta propagación continúa hasta que se encuentra el mecanismo para gestionarla o bien finaliza el programa al alcanzarse el nivel superior de la pila de llamadas.


## La clase “Error” y sus derivadas

### Error

Cuando ocurre una excepción, se crea un objeto representando dicho error y se lanza la excepción. El objeto creado es de tipo **Error**y se usa frecuentemente para la gestión de excepciones de usuario. Podemos crear un objeto de tipo error de la siguiente forma:

```language-javascript
var error = new Error("Se ha producido un error");
```

Dicho objeto contendrá <span>principalmente</span>dos propiedades, *name* y *message, que se*corresponden con el nombre del tipo de excepción (“Error”) y el mensaje incluido en la instanciación (“Se ha producido un error”) respectivamente.

Además de la clase *Error*, tenemos otra serie de clases derivadas que hacen referencia a errores más específicos.

### RangeError

Errores generados por números fuera de rango, por ejemplo, con la siguiente sentencia:

```language-javascript
var array = new Array(10000000000);
```

obtendremos como resultado el lanzamiento de una excepción con el error:

<span style="color: #ff0000;">RangeError: Invalid array length</span>

### ReferenceError

Se produce cuando referenciamos variables que no existen:

```language-javascript
var x = y;
```

<span style="font-size: 1em; line-height: 1.6em;">Tendrá como consecuencia la generación de un error:</span>

<span style="color: #ff0000;">ReferenceError: y is not defined</span>

### SyntaxError

Se lanza al no cumplirse las reglas del lenguaje:

```language-javascript
fnction myFunc() { }
```

Genera la excepción:

<span style="color: #ff0000;">SyntaxError: Unexpected identifier</span>

### TypeError

Se producen cuando un valor no es de un tipo esperado, por ejemplo al llamar a un método no existente de un objeto:

```language-javascript
var x= {}; x.y(); // TypeError
```

“y” no existe como función en “x” y por tanto se producirá un:

<span style="color: #ff0000;">TypeError: Object “Object” has no method ‘y’</span>

### URIError

<span>Se lanza cuando métodos propios del lenguaje como</span>*encodeURI()* o * decodeURI()* se invocan con URIs no válidas:

```language-javascript
decodeURIComponent("http://%ominio.com"); // URIError
```

Produce la excepción:

<span style="color: #ff0000;">URIError: URI malformed</span>


## Gestión de excepciones

Comentábamos que la gestión de excepciones en JavaScript es similar a la de otros lenguajes. Dicha gestión debe ser realizada si no queremos que nuestros programas dejen de funcionar de forma adecuada.

El típico bloque ***try…catch…finally*** será el encargado de permitirnos dicha gestión.

```language-javascript
try { 
    // Intentamos ejecutar el código 
} catch (exception) { 
    // Gestionamos la excepción si es que se produce 
} finally { 
    // Se ejecuta siempre 
}
```

La única parte obligatoria del bloque es ***try***. Podemos seguirlo con ***catch***, con ***finally***o bien con ambos.

**catch** sólo se ejecuta si se produce una excepción en el ***try*** y aunque es opcional, si no lo incluimos, realmente no estaremos gestionando nada.

En cuanto la excepción se produce, el control pasa a la sentencia **catch** y  tendremos disponible la información acerca de la misma en la variable </span>***exception***<span style="font-size: 1em; line-height: 1.6em;">. Por tanto si hacemos lo siguiente:</span>

```language-javascript
try { 
    var x = y; // ReferenceError 
} catch (exception) { 
    console.log(exception.message);     
    console.log(exception.name); 
}
```
Veremos en la consola JavaScript los mensajes:

<div class="console-message console-log-level"><span class="console-message-text source-code" style="color: #ff0000;">y is not defined</span></div><div class="console-message console-log-level"><span class="console-message-text source-code" style="color: #ff0000;">ReferenceError</span></div><div class="console-message console-log-level"><span class="console-message-text source-code">  
</span></div><span>Por supuesto, podríamos necesitar distinguir entre diferentes tipos de excepciones para gestionar los errores de diversas maneras. Para esto, podemos usar *instanceOf* dentro del bloque ***catch***</span>

```language-javascript
if (exception instanceof TypeError) { 
    ... 
} else if (exception instanceof ReferenceError) { 
    ... 
} else { 
    ... El resto ... 
}
```

Por último, el bloque ***finally*** se ejecuta siempre haya o no haya excepción. Hay que tener en cuenta, que incluso se ejecutará aunque se haga un *return* en el bloque ***catch***.

## Lanzando Excepciones

También por supuesto podemos lanzar nuestras propias excepciones a través de la sentencia **throw**.

<span style="font-size: 1em; line-height: 1.6em;">Algunos ejemplos del uso de ***throw*** serían.</span>

```language-javascript
throw true; 
throw "Mensaje"; 
throw undefined; 
throw { error : "Error", mensaje : "Mensaje de error" }; 
throw new SyntaxError("Mensaje de error");
```
<span style="font-size: 1em; line-height: 1.6em;">Como se puede apreciar, no hay ningún tipo de restricción en el tipo de datos de la excepción. Pese a no existir esta restricción, el usar los tipos nativos de error puede darnos algún bonus en forma de tratamiento especial en herramientas de depuración como ***Google Dev Tools*** o ***Firebug.***Este tratamiento especial podría venir en forma de nombre de fichero en el que se produce la excepción, línea en la que se produce, … etc.</span>


## <span style="font-size: 1em; line-height: 1.6em;">Objetos personalizados de error</span>

<span style="font-size: 1em; line-height: 1.6em;">Además de los tipos nativos que nos ofrece JavaScript, podemos crear nuestros propios tipos de error. La forma en la que podemos hacerlo es aplicando herencia. Por ejemplo, suponiendo que tenemos un servicio web con el que se puede perder la conexión, podríamos crear una excepción personalizada a la que podemos llamar ***ConnectionLostError***</span>

```language-javascript
function ConnectionLostError(message) { 
    this.name = "ConnectionLostError"; 
    this.message = (message || ""); 
} 
ConnectionLostError.prototype = new Error(); ConnectionLostError.prototype.constructor = ConnectionLostError;

```

Ahora ya podríamos usarla cuando nuestra aplicación pierda la conexión con el servicio

```language-javascript
if (connection.lost() == true) { 
    throw new ConnectionLostError("Se ha producido una perdida de conexión en la aplicación"); 
}
```

Viendo por la consola JavaScript que se lanza la excepción

<span style="color: #ff0000;">ConnectionLostError: Se ha producido una perdida de conexión en la aplicación</span>

<span style="font-size: 1em; line-height: 1.6em;">Ahora lo lógico sería que la controlásemos en un nivel superior de la pila de llamadas:</span>

```language-javascript
try { 
    insertarAlgo(); 
} catch (exception) { 
    if (exception instanceof ConnectionLostError) { reconectar() 
    } 
}
```

## Resumen

Bueno, pues hemos visto una serie de métodos para escribir un mejor código a prueba de fallos … ahora ya no hay excusa para no gestionar los errores en nuestros scripts.

Hasta pronto!!



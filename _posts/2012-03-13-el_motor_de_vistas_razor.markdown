---
layout: post
title: El motor de vistas Razor
date: '2012-03-13 10:46:43'
tags:
- razor
---


Razor es el nuevo motor de generación de vistas de ASP MVC. Hasta la llegada de la versión 3 de este Framework, la única posibilidad de construcción de vistas era mediante ASP clásico. En este post vamos a ver las principales características de Razor y en qué nos puede ayudar para acelerar el proceso de generación de nuestro código HTML.


## **@bienvenidoarazor**

El motor de vistas **Razor**, permite escribir el código de nuestras vistas de una manera mucho más ágil y limpia que utilizando las típicas construcciones **ASP****“” o ““**. Con **Razor**, las expresiones anteriores quedarían reducidas simplemente a “**@expresion”. **

Supongamos, por ejemplo, que vamos a escribir en un párrafo **HTML** un campo *Text*o*Post* de un modelo de datos ficticio. Con **ASP** lo haríamos de la siguiente forma

Sin embargo con **Razor** sería así

@Model.TextoPost

Como se puede apreciar, la sintaxis de **Razor** es mucho más compacta y expresiva. Además, el motor de **Razor** detecta automáticamente donde acaba la expresión y comienza de nuevo el marcado **HTML**.

**Razor** se encarga automáticamente de codificar la salida, por esta razón no se necesita código adicional para evitar inyección de scripts ya que es el motor de vistas el que se encarga de la sustitución de las etiquetas “peligrosas” al renderizar sobre el canal de salida. Si no estoy interesado en que se produzca esta codificación automática ya que alguno de mis campos contiene **HTML** es suficiente con utilizar el helper *Raw().*

//Suponiendio que TextoPost contiene HTML introducido por el usuario y generado mediante un editor. La siguiente intrucción evita que se codifique la salida. @Html.Raw(Model.TextoPost)


## **Procesando código en nuestras vistas**

En **webforms** podemos procesar fragmentos de código que se encuentren entre . Con **Razor** por supuesto podemos hacer lo mismo simplemente con @{ … }

@{ int a = 3; int b = 4; int suma = a + b; } ... @suma

Estos bloques tienen además la capacidad de mezclar marcado **HTML **y código, es decir, puedo “salirme” del código para “escribir” HTML y volver al código de nuevo, simplemente usando la @. **Razor** es capaz de identificar cuando ha de procesar marcado **HTML ** o bien cuando ha procesar código.

@{ int a = 3; int b = 4;  @(a + b)

 }

Como se aprecia en el ejemplo anterior, **Razor **pasa a modo marcado cuando detecta etiquetas y sigue en este modo hasta que detecta el cierre de las mismas. Con la @ pasamos de nuevo a modo código.

Si quiero escribir **HTML** cuando estoy en modo código, **Razor** detecta el cambio al encontrar una etiqueta **HTML**. Si estoy en modo **HTML** y quiero pasar a modo código lo hago introduciendo el carácter de escape @.


## **<text> y @:**

Hay casos especiales en los que el *parser* de **Razor** puede no ser capaz de identificar lo que ha de hacer. Por ejemplo, el siguiente caso provoca un error de compilación.

@for(int i = 1; i }

**Razor **no es capaz de identificar lo que ha de hacer porque no se encuentra ni marcado **HTML **ni código. ¿Cómo podemos hacer para ayudarle un poco? El carácter **@:** indica que lo que viene continuación es texto o marcado por lo que si cambio el código anterior por

@for(int i = 1; i }

Ahora sí que se mostrarán los diez textos consecutivos. De todas maneras, hay otra posibilidad de hacer lo mismo. Utilizar **<text>…</text>**, que nos indica que lo que viene a continuación debe ser tratado como texto

@for(int i = 1; iEl bucle va en la iteración @i }

El resultado será el mismo. El tag no aparece en el código enviado al cliente ya que es un tag especial del motor **Razor.**


## **Y que pasa con los mails y la @**

Razor reconoce el formato de las direcciones de mail por lo que no es necesaria ninguna construcción especial.


## Y para acabar … una guía de referencia rápida …

A continuación podemos ver una serie de ejemplos de código a modo resumen que ilustran las diferentes situaciones que se nos van a producir cuando escribimos código con **Razor.**

***Bloque de código***

@{ int a = 3; int b = 4; int suma = a + b; }

***Expresión codificada***

@Model.TextoPost

***Expresión sin codificar***

@Html.Raw(Model.TextoPost)

***Combinación de texto y marcado***

@foreach(var post in Model.Posts) { @post.TextoPost

 }

***Combinación de código y texto plano***

@if (Model.TipoDePost == "articulo") { <text>Articulo @Model.TipoDeArticulo</text> } @if (Model.TipoDePost == "articulo") { @:Articulo @Model.TipoDeArticulo }

***Direcciones de mail***

Hola usuario@dominio.com

***Expresion explicita***

POST@(Model.PostId)

***Escape del símbolo @***

Si incluyo dos arrobas en @@Model.Text lo que hago es escribir el valor de Model.Text

***Comentario***

@* Esto es un comentario *@

***Llamada a un método***

@(MyClase.MiMetodo())

***Creación de delegados Razor***

@{ Func<dynamic object=""> b = @**@item**; } @b("Ponme en negrita") </dynamic>

***Mezclando expresiones y texto plano***

Numero de Post @Model.Post: @Model.Title. Model.Text. (... el motor Razor como se puede apreciar reconoce los ":" y el punto "." ...)

******Esto es todo por ahora … Hasta pronto!!



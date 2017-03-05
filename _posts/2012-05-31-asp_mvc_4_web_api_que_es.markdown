---
layout: post
title: ASP MVC 4 Web API. ¿Qué es?
date: '2012-05-31 07:30:53'
tags:
- rest
---


Una de las principales novedades de la nueva versión de ASP MVC 4 es la Web API, mediante la cual podemos crear una auténtica capa de servicios REST de manera muy similar a como veníamos desarrollando los controladores hasta ahora.

En **ASP MVC 3** era posible construir una capa de de servicios simplemente retornando un **JsonResult** en las distintas acciones de los controladores. La [**Web API**](http://www.asp.net/web-api "ASP NET Web API") se encarga de realizar este trabajo **de forma específica**, ofreciendo unas funcionalidades más avanzadas y apropiadas para crear este tipo de capas de servicios de tipo **REST**.

La **Web API** está pensada para el trabajo con *HTTP* y aunque su funcionamiento es muy similar al de cualquier controlador, es cierto que hay también bastantes diferencias.

Construyendo la capa de servicios a través de la **Web API**, podemos dejar que nuestros controladores se centren solamente en la función de la que deberían encargarse y que no es otra que tratar con las vistas. Serán los controladores de la **Web API** los que ofrecerán los métodos de la capa de servicios que devuelvan a las aplicaciones clientes los datos Json, XML o cualquier otra cosa dependiendo de la negociación de contenidos efectuada a través de [*formatters*](http://www.asp.net/web-api/overview/formats-and-model-binding/media-formatters "Media Formatteres").


## Media Formatters

Cuando se hace una petición se puede incluir el tipo de contenido que se acepta como respuesta en el cliente a través de la cabecera *Accept*

```
Accept: text/html,application/xhtml+xml,application/xml
```

La cabecera anterior, por ejemplo, está indicando al servidor que el cliente acepta como respuesta *html, xhtml o xml*. En la **Web API**, los media formatters determinan la forma de serializar o deserializar el contenido del mensaje *HTTP*.

Existe soporte para los tipos *XML y JSON* por defecto pero es posible crear mis propios *formatters* derivando las clases *MediaTypeFormatter* o *BufferedMediaTypeFormatter* para soportar cualquier otro tipo.

Que exista este soporte por defecto quiere decir que si hago una petición jquery del tipo *$.getJSON()* automáticamente se devolverá JSON al cliente y si hago una petición *$.get()* indicando que quiero *xml*, pues entonces se enviará de vuelta el contenido como *xml*.


## **Nueva clase base**

Los controladores dela Web API NO heredan de *Controller*, lo harán de *ApiController* que se encargará de gestionar las respuestas a los verbos *GET, POST, PUT y DELETE* que se envíen desde los clientes *REST.*


## **Enrutado**

El enrutado es un poco diferente a cómo estábamos acostumbrados. Ahora se añade una **nueva ruta** a la tabla de rutas. Si estamos tratando con un proyecto **ASP MVC 4** además de la ruta de la **Web API** aparecerá la ruta de los controladres “normales” como venía sucediendo.

```language-javascript
routes.MapHttpRoute(

    name: "DefaultAPIRoute",

    routeTemplate: "api/{controller}/{id}",

    defaults: new { id = RouteParameter.Optional }

);
```

Comentar que en la última revisión de los proyectos ASP MVC 4 RC se ha movido la configuración de la tabla de rutas a una clase de la carpeta *App_Start*. Hasta ahora esta definición residía en el *global.asax*.

La principal novedad de esta ruta está en que no se incluye el parámetro de la acción *({action}).* Además las rutas hacia la **Web API** están precedidas por el literal *“api/”*.

La carencia de la acción tiene sentido, ya que como comentábamos, la **Web API** se basa totalmente en *HTTP* y para “ella” la acción depende del verbo con el que se hace la petición (*GET, POST, PUT, DELETE*), facilitando de esta forma el acceso al recurso en lugar de a las acciones (aunque veremos que podemos modificar este comportamiento por defecto para hacer lo mismo que veníamos haciendo en los controladores **ASP MVC 3**).

Para encontrar la acción a ejecutar, en primer lugar se comprueba el verbo de la petición y a continuación se buscará algún método cuyo nombre comience por dicho verbo y cuyos parámetros encajen con los de la petición.

Supongamos una petición del tipo

```
GET api/products/
```

la **Web API** buscará acciones del tipo *GetXXXX *como podrían ser *GetProduct(), GetAllProducts() *o simplemente**Get().**

Una petición del tipo

```
GET api/products/3
```

buscará acciones del tipo *GetXXXX(int id)* como por ejemplo *Get(3)* o *GetProduct(5)*.

Mientras que otra petición del tipo

```
GET api/products
```

buscará acciones del tipo *GetXXXX()* como *GetAllProducts()* ya que la petición no contiene parámetros formales.

En la ruta por defecto estamos indicando que el parámetro *id* es opcional, pero si aparece en la petición, en la acción el nombre del parámetro ha de ser también *id*, es decir, si tengo un método de acción

```language-javascript
public Product GetProduct(int productid)
```

y efectúo una llamada a la api como esta

```
GET api/products/13
```

No encontrará el método de acción, ya que su parámetro no se llama *id*. Sí que podría invocar al método anterior mediante

```
GET api/products?productid=13
```

El comportamiento por defecto del sistema de búsqueda por convención se puede modificar usando los atributos de acción **HttpGet**, **HttpPost** que ya nos sonarán de los controladores clásicos de ASP MVC. Si decoro una acción con *[HttpGet]*, puedo nombrar al método de acción como desee y cuando se produzca una petición de tipo *GET*, la acción decorada con dicho atributo se incluirá en el rastreo de búsqueda de métodos de acción a ejecutar.

Por supuesto también se puede configurar el uso de otros verbos personalizados mediante

```
[AcceptVerbs("miverbopersonalizado, miotroverbopersonalizado")]
```


## Modificando el sistema de rutas por defecto

Decíamos anteriormente que también podíamos usar el sistema de rutas clásico de **ASP MVC** (o cualquier otro personalizado). Si quisiéramos usar este sistema de rutas podríamos incluir el parámetro de acción *({action})* en la tabla de rutas en lugar de la que viene por defecto.

```language-javascript
routes.MapHttpRoute(

    name: "APIDefault",

    routeTemplate: "api/{controller}/{action}/{id}",

    defaults: new { id = RouteParameter.Optional }

);
```

En este caso tenemos que tener en cuenta que hay que incluir siempre como atributo el tipo de verbo a la acción *[HttpGet], [HttpPost]* ya que estamos eliminando la convención de rastreo de acciones de la **Web API**.

Conviene también comentar que existe un atributo mediante el cual puedo evitar que un método sea invocado desde el exterior. Este atributo es *[NonAction]*.


## Respuestas a las peticiones

Los métodos de acción de la **Web API** son similares a los de cualquier controlador, con la excepción de que no devuelven una Vista.

En su lugar se intercambiarán una serie de mensajes y en muchos casos nuestros métodos de acción devolverán instancias de *HttpResponseMessage*. Estas instancias permiten retornar un código de respuesta HTTP además de incluir la propia entidad en caso de necesitarlo.


## Resumen

En este post hemos hecho una pequeña introducción a la **Web API**. Hemos visto cual es su objetivo, principales funcionalidades, qué son los formatters, cómo se hace el enrutado y cómo se crean las respuestas.

En el [siguiente post](../../../Post/GetPostByCode/asp_mvc_4_web_api_primeros_pasos "Ejemplo de Web API") veremos cómo implementar una capa de servicios usando la **Web API**.

Hasta pronto!!


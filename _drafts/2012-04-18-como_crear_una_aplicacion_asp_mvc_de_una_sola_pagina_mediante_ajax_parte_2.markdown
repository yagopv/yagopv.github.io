---
layout: post
title: Como crear una aplicación ASP MVC de una sóla página mediante ajax (parte 2)
date: '2012-04-18 12:33:57'
tags:
- spa
---


En el [primer post](../../../Post/GetPostByCode/como_crear_una_aplicacion_ASP_MVC_de_una_s%C3%B3la_p%C3%A1gina_mediante_ajax_parte_1 "Aplicación SPA parte 1") de esta serie, vimos cómo podíamos crear un widget de <span data-tag="">jquery</span> ui que permitía hacer recargas de página en modo ajax de manera que nuestra aplicación web se convertía en una aplicación de tipo **“Single Page Application”**. Este tipo de aplicaciones se denominan así porque cargan una sóla página inicialmente y a partir de esta el resto de cargas se hacen mediante ajax en una sección designada de la página inicial.

En este post vamos a crear una aplicación **MVC 4** desde cero y vamos a probar el funcionamiento de nuestro widget. De paso, para la interfaz de usuario usaremos [Bootstrap](http://twitter.github.com/bootstrap/ "Documentación de Bootstrap") que nos permite crear interfaces de una manera rápida, ágil y sencilla.

El sitio web que vamos a crear lo podemos ver en esta [dirección](http://mvcspaapplication.apphb.com/ "Aplicación de ejemplo").

## Comenzamos

La aplicación está creada con Visual Studio 2011 aunque con la versión 2010 es suficiente ya que no vamos a usar características de **.NET Framework 4.5 o de ASP MVC 4** que no estén incluidas en **.NET Framework 4 o ASP MVC 3**. De hecho la solución está compilada con .NET 4 para poder desplegarla en [AppHarbor](https://appharbor.com/ "AppHarbor perrmite desplegar aplicaciones para pruebas"). Bien, abrimos Visual Studio y escogemos crear una nueva aplicación **ASP MVC** a la que llamaremos **MVCSPAApplication**. La plantilla que seleccionaremos será la **plantilla vacía**.

![Nueva aplicación](/content/images/2017/01/4d1b75c7-8d8a-4a0c-b5fb-b54d042ed1a3_Nueva_Aplicacion.jpg)

El sitio que vamos a crear será un mock de un sitio de venta de cualquier tipo de productos y tendrá las siguientes páginas:

<em><strong>Index</strong></em>:  La página de inicio y en la que se muestran los productos destacados</p>
<p><em><strong>Productos</strong></em>: Las páginas en las que se mostrarán los productos por categoría</p>
<p><em><strong>Contacto</strong></em>: La página de contacto</p>
<p><em><strong>Acercade</strong></em>: El típico Acerca de

 Todas las páginas serán accesibles desde el controlador ***HomeController***, por simplicidad más que nada, en una aplicación real tendríamos seguro un ProductosController.

## Un poco de limpieza de la plantilla

Vamos a limpiar un poco la solución de cosas que no vamos a usar. Empezamos por eliminar todo el contenido de la carpeta de ***Scripts***. Aunque necesitamos algunos de ellos, vamos a descargar nosotros las versiones más actuales y además en los productos que permitan una descarga personalizada sólo las partes necesarias para comprobar cual es el mínimo necesario que necesitamos para el correcto funcionamiento del widget.

Eliminamos la carpeta ***themes*** del directorio **Content**

Abrimos el archivo ***Site.css*** y borramos todo el contenido

Ahora abrimos el ***Global.asax*** y eliminamos las siguientes lineas que no vamos a usar

1) La siguiente línea es para registrar las rutas de los controladores que usen la nueva **Web Api**, obviamente no la queremos para nada …

```language-javascript
            routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
```

2) No vamos a usar base de datos, por lo que la linea que registra la connexion para usar una base de datos con **SqlServer LocalDb** no la necesitamos tampoco

```language-javascript
Database.DefaultConnectionFactory = new SqlConnectionFactory("Data Source=(localdb)\v11.0; Integrated Security=True; MultipleActiveResultSets=True");
```

3) También hay una línea para iniciar las capacidades de combinación y minificación de scripts incluidas en **ASP MVC 4**. No la necesitamos ya que vamos a incluir nuestros scripts de manera clara para ver sólo lo que necesitamos para el funcionamiento del widget. La quitamos por tanto.

```language-javascript
BundleTable.Bundles.RegisterTemplateBundles();
```

4) Eliminamos el contenido de **Layout.cshtml** dentro de **View/Shared**

5) Eliminamos la carpeta **Models** que no vamos a usar

Una vez hecha la limpieza de archivos ya tenemos el proyecto listo para comenzar a trabajar. Quedaría como se puede ver en la siguiente imagen


![Solución lista para comenzar](/content/images/2017/01/f53af8c4-f4d0-41b6-84cd-df0f3ccd8fd5_Solucion_Limpia.jpg)


## Descargando las dependencias necesarias

Las dependencias mínimas para el correcto funcionamiento de la solución propuesta son los scripts de **jquery, jquery ui (widget factory)y modernizr.**

Descargamos la última versión de **jquery** [aquí](http://jquery.com/ "jquery.com") y la añadimos a nuestra carpeta de **Scripts**. Al pulsar download nos mostrará el código minificado así que creamos un nuevo archivo de script en nuestra carpeta **Scripts** que se llame ***jquery.js***, copiamos el código de la página de <span data-tag="">jquery</span> y lo pegamos en nuestro archivo.  


Descargamos **jquery ui** [aquí](http://jqueryui.com/download "Descarga de jquery ui"). La página nos permite crear una descarga personalizada así que sólo necesitaremos marcar la factoría de widgets.



![Descarga factoría de widgets](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/Descarga-jquery-ui.jpg "Descarga factoría de widgets")


Una vez descargado buscamos el script*** jquery.ui.widget.js***, que se encuentra en la carpeta **development-bundle/ui** y lo arrastramos a nuestra carpeta **Scripts**.****Sólo necesitamos este script. El resto de scripts de la librería no los vamos a utilizar. Este script lo que hace es definir la factoría de widgets a partir de la cual nosotros hemos implementado nuestro plugin. Se define en este archivo la función ***widget() ***que hemos usado para crear nuestro componente. El resto de funcionalidades de **jquery ui** no las vamos a usar ya que para la interfaz de usuario vamos a usar **Bootstrap**.

Descargamos **modernizr**[aquí](http://modernizr.com/download/ "Descarga de modernizr"). **modernizr** es un plugin que de una manera sencilla nos permite detectar si el navegador que se está usando tiene la capacidad de interpretar características avanzadas de **CSS** y **HTML5** para poder en caso contrario darunavisualización alternativa a nuestra página. En el enlace que indico se permite, al igual que en el caso de <span data-tag="">jquery</span> ui, hacer una descarga personalizada. Nosotros lo único que queremos descargar es la capacidad de **modernizr** de detectar si el navegador implementa la **history API.**

En el primer post de esta serie en el que creamos nuestro widget, se puede ver que para la navegación “hacia atrás” y para modificar la url del navegador se usa el nuevo objeto ***history*** de la **history API**. Este objeto sólo lo usan los navegadores actuales por lo que en navegadores anteriores no dipondríamos de esta capacidad y por tanto la navegación ajax no se llevaría a cabo, creando un comportamiento inconsistente y extraño en la aplicación web.

Para solucionar esto tenemos varias opciones, una de ellas es usar plugins que añadan esta capacidad a navegadores anteriores como puedeser***[history.js](https://github.com/balupton/History.js/ "Compatibilidad en el objeto history con  los navegadores antiguos"). ***

En la aplicación aquí propuesta vamos a adoptar otra solución. Lo que vamos a hacer es detectar si el navegador puede usar la** history API** mediante **modernizr** y dependiendo de ello instanciaremos nuestro widget o no. Por tanto en navegadores antiguos no funcionaría la recarga ajax y se comportaría como una aplicación web multi-página al uso, que tampoco es mayor problema ya que en poco tiempo se supone que todos los navegadores implementarán el **history API.**

Por tanto, en la página de descarga de **modernizr** marcaremos el checkbox que dice ***history ***y haremos la descarga del script, que se quedará en unos 6K. Añadiremos el script a nuestra carpeta de ***Scripts*******o bien pegamos el código directamente en un archivo **js** llamado **modernizr** que hayamos añadido como Nuevo item, al igual que habíamos hecho al añadir <span data-tag="">jquery</span>.

![Descarga modernizr](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/Descarga-modernizr.jpg "Descarga modernizr")

A continuación añadimos nuestro script ***spalinks***.***js*** creado en el post anterior a la misma carpeta que los demás. Podemos descargarlo o copiar el código [aquí](https://github.com/yagopv/MVCSPAApplication/blob/master/MVCSPAApplication/Scripts/spalinks.js "El script spalinks.js").

Por último descargaremos **Bootstrap** para crear nuestra interfaz de usuario desde la página de descarga oficial que está [aquí](http://twitter.github.com/bootstrap/index.html "Descarga de Bootstrap"). Pulsamos el botón download y obtendremos un archivo .zip.

El archivo zip contiene tres carpetas, ***js, css e img*** cuyo contenido es el siguiente:

<span style="text-decoration: underline;">**Carpeta js**</span>

***bootstrap.js (bootstrap.min.js)***

El código de los widgets de <span data-tag="">bootstrap</span> y scripts necesarios para el funcionamiento de la interfaz

<span style="text-decoration: underline;">**Carpeta css**</span>

***bootstrap.css (bootstrap.min.css)***

Los estilos de <span data-tag="">bootstrap</span>

***bootstrap-responsive.css (bootstrap-responsive.min.css)***

La hoja de estilos para la contrucción de layouts con <span data-tag="">bootstrap</span>. El esqueleto de cada página

<span style="text-decoration: underline;">**Carpeta img**</span>

Contiene las imágenes con los iconos necesarios para la correcta visualización de <span data-tag="">bootstrap</span>.

Añadiremos las carpetas **img y css** a la carpeta **Content** y el contenido de la carpeta **js** a la carpeta **Scripts**. Dentro de las carpetas **css** y **js** elimino las versiones minificadas y me quedo con las de depuracion.

Si quiero variar los estilos por defecto de **bootstrap**, puedo modificarlos en la hoja de estilos**bootstrap.css** o **bootstrap-responsive.css**, que contiene el tema básico y los layout, aunque es mejor idea sobreescribirlos en una hoja de estilos propia que se incluya a continuación de las de **bootstrap**. Es preferible hacer esto para facilitar las posibles actualizaciones de **bootstrap** que querramos hacer.

También hay páginas como [www.bootswatch.com](http://www.bootswatch.com/)que ofrecen estilos predefinidos. Voy a ir a esta página y me voy a descargar uno de los que aquí se ofrecen, por ejemplo el [**cerulean**](http://bootswatch.com/cerulean/ "Bootswatch cerulean"). Obtengo el codigo del mismo directamente de la página y lo sustituyo en la hoja de estilos **bootstrap.css**.

![Bootswatch cerulean](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/7ac09989-c602-46c5-9747-fd3ac18ba6f4_Bootswatch_Cerulean.jpg "Bootswatch cerulean")



Con esto tengo todos los scripts y estilos para comenzar con la aplicación.

En esto momentos la solución estaría así



![Solucion con archivos de script y hojas de estilo añadidas](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/4ff565fa-0a98-4eaa-a0ed-24ddf7aa33eb_Solucion_con_Scripts_Content.jpg "Solucion con archivos de script y hojas de estilo añadidas")


## Creando el Layout de la aplicación

El contenido de **Layout** es la primera y única página que se cargará. Por tanto en esta vista maestra incluiremos los scripts necesarios para el funcionamiento general de la aplicación y las hojas de estilos que vamos a usar. El código de nuestro **Layout** (*Views/Shared*) será el siguiente

   <meta charset="utf-8" /> <title>Ejemplo de aplicacion SPA con Bootstrap 2.0</title> <meta name="author" content="Yago Perez Vazquez"> <link href="~/favicon.ico" rel="shortcut icon" type="image/x-icon" /> <link href="/Content/css/bootstrap.css" rel="stylesheet"> <link href="/Content/css/bootstrap-responsive.css" rel="stylesheet"> <link href="/Content/Site.css" rel="stylesheet"> <meta name="viewport" content="width=device-width" />   <div class="wrapper"> @* Barra de navegacion *@ <div class="navbar navbar-fixed-top"> <div class="navbar-inner"> <div class="container"> <a class="btn btn-navbar" data-toggle="collapse" data-target=".nav-collapse"> <span class="icon-bar"></span> <span class="icon-bar"></span> <span class="icon-bar"></span> </a> <a class="brand" href="#">Navegacion</a> <div class="nav-collapse"> <ul class="nav"> <li>@Html.ActionLink("Inicio","Index",null,new { data_ajax="true"})</li> <li>@Html.ActionLink("Productos", "Productos", new { categoria=1 }, new { data_ajax = "true" })</li> <li>@Html.ActionLink("Contacto", "Contacto", null , new { data_ajax = "true" })</li> <li>@Html.ActionLink("Acerca de", "Acercade", null, new { data_ajax = "true" })</li> </ul> </div> </div> </div> </div> @* Contenedor para recarga de pagina *@ <div class="container-fluid"> @RenderBody() </div> <div class="push"></div> </div> @* Pie de pagina *@ <div class="footer"> <h3>@@Yago Perez Vazquez 2012</h3> </div> <script src="/Scripts/jquery.js"></script> <script src="/Scripts/jquery.ui.widget.min.js"></script> <script src="/Scripts/spalinks.js"></script> <script src="/Scripts/bootstrap.js"></script> <script src="/Scripts/modernizr.js"></script> <script type="text/javascript"> $(document).ready(function () { if (Modernizr.history) { $("body").spalinks({ target: $(".container-fluid") }); } }); </script>  

En el **head** de la página añadimos las tres hojas de estilos que vamos a utilizar, **bootstrap.css, bootstrap_responsive.css y Site.css**.

Al final del**body** (Para mejorar el tiempo de carga) añadiremos nuestros scripts **jquery.js, jquery.ui.widget.min.js, spalinks.js, bootstrap.js y modernizr.js**

También en el **body**,crearemos dos secciones principales **wrapper y footer.** Hacemos esta división para crear un ***sticky footer*** que se pegue al final de la página tanto si está “llena” de contenidos como si no. Para ello además del **wrapper y el footer**, tenemos que añadir al final del **wrapper** la sección **push** que “empuja” el footer hasta el final cuando no hay contenidos para llenar la página. Los estilos que necesitamos los añadimos a** Site.css**, la hoja de estilos de nuestra aplicación.

/* Sticky footer */ html, body { height: 100%; } .wrapper { min-height: 100%; height: auto !important; height: 100%; margin: 0 auto -3em; } .push { height: 4em; } .footer { padding: 2% 2% 2% 5%; background-color: #3F9FD9; height: 4em; margin-left:-20px; margin-right: -20px; } .footer h3 { color: #F5F5F5; } .container-fluid { padding-top: 60px; }

Como siguiente paso, en nuestro **wrapper**, vamos a añadir la barra de navegación. Para más información acerca de lo que significa cada clase de **bootstrap**, acudir a la documentación del producto que es excelente. Aquí tampoco nos vamos a parar punto por punto a explicar qué es cada cosa pero la mayoría de layouts se obtienen de la documentación con un simple copy/paste. Por tanto la barra de navegación quedaría así (las clases de **bootstrap** son bastante autodescriptivas).

<div class="navbar navbar-fixed-top"> <div class="navbar-inner"> <div class="container"> <a class="btn btn-navbar" data-toggle="collapse" data-target=".nav-collapse"> <span class="icon-bar"></span> <span class="icon-bar"></span> <span class="icon-bar"></span> </a> <a class="brand" href="#">Navegacion</a> <div class="nav-collapse"> <ul class="nav"> <li class="active">@Html.ActionLink("Inicio","Index",null,new { data_ajax="true"})</li> <li>@Html.ActionLink("Productos", "Productos", new { categoria=1 }, new { data_ajax = "true" })</li> <li>@Html.ActionLink("Contacto", "Contacto", null , new { data_ajax = "true" })</li> <li>@Html.ActionLink("Acerca de", "Acercade", null, new { data_ajax = "true" })</li> </ul> </div> </div> </div> </div>

Esta barra de navegación contiene los links a cada una de las página que vamos a crear. A destacar que estamos añadiendo a cada link el atributo **data-ajax = “true” **para que el widget detecte qué es lo que tiene que ser recarga ajax y qué no. Si no incluimos este atributo, nuestro widget no hará nada con el link y el comportamiento será el de un link corriente.

Entre la barra de navegación y el pié de página, estará nuestra sección de contenidos, que será la sección en la que inyectaremos las recargas ajax con los datos recuperados del servidor

<div class="container-fluid"> @RenderBody() </div>

Encerramos el ***RenderBody***en un **div**

<div>con la clase ***container-fluid***. Esta clase de **bootstrap** hace que el contenedor ocupe un ancho del 100% y se pueda comportar de manera adaptativa. Permite que se definan otras estructuras en su interior basadas en percentiles y no en anchos fijos.

Por último invocaremos a nuestro widget

 if (Modernizr.history) { $("body").spalinks({ target: $(".container-fluid") }); }

Estamos usando **modernizr** para comprobar si el navegador implementa la **history API**. Por tanto si ***Modernizr.history***devuelve *true* incocamos al widget para crear una instancia sobre el elemento **body** e indicamos como **target** para la recarga al elemento con la clase ***container-fluid***.

Con esto tenemos creado nuestro **Layout**. Ahora tendremos que ir creando el resto de vistas de nuestra aplicación y por supuesto el controlador que se encargará de servirlas.


## HomeController, el controlador de la aplicación

El controlador de la aplicación es muy sencillito ya que no hace nada más que devolver vistas. La única “complicación” reside en la vista de ***Productos***, que vamos a hacer que devuelva un ***ViewModel***con los productos en función de la categoría que se quiera mostrar (1,2 o 3 en nuestra simulación). Añadimos un controlador a la carpeta **Controllers** seleccionando la opción de controlador vacío y sustituimos el código de su interior por este

using System; using System.Collections.Generic; using System.Linq; using System.Web; using System.Web.Mvc; using MVCSPAApplication.ViewModels; namespace MVCSPAApplication.Controllers { public class HomeController : Controller { public ActionResult Index() { return View(); } public ActionResult Acercade() { return View(); } public ActionResult Contacto() { return View(); } public ActionResult Productos(int categoria) { List productos; if (categoria == 1) { ViewBag.Categoria = "Categoria 1"; productos = new List() { new ProductoVM { Nombre= "Producto 1", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 2", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 3", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 4", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 5", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 6", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 7", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 8", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 9", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 10", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 11", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 12", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 13", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 14", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" } }; return View(productos); } else if (categoria == 2) { ViewBag.Categoria = "Categoria 2"; productos = new List() { new ProductoVM { Nombre= "Producto 1", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 2", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 3", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 4", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 5", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 6", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 7", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 8", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 9", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" } }; return View(productos); } else { ViewBag.Categoria = "Categoria 3"; productos = new List() { new ProductoVM { Nombre= "Producto 1", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 2", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 3", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 4", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 5", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 6", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 7", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 8", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 9", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 10", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 11", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 12", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 13", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 14", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 15", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 16", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 17", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 18", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 19", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 20", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 21", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 22", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 23", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 24", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 25", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 26", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" }, new ProductoVM { Nombre= "Producto 27", Descripcion="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id" } }; return View(productos); } } } }

***Index, Contacto, y Acercade*** simplemente devuelven la vista solicitada en una petición *GET*. ***Productos***devuelve una lista de ***ProductoVM***, clase que tiene simplemente dos propiedades Nombre y Descripción y que crearemos en la carpeta **ViewModels**.

Por tanto,creamos la carpeta en la raíz de la aplicación y añadimos la clase ***ProductoVM*** a la misma con el siguiente código

using System; using System.Collections.Generic; using System.Linq; using System.Web; namespace MVCSPAApplication.ViewModels { public class ProductoVM { public string Nombre { get; set; } public string Descripcion { get; set; } } }

La acción ***Productos***tiene como parámetro la Categoría (***categoriaid***), que podrá ser 1, 2 o 3.


## Creando vistas

A partir del controlador, pulsaremos con el segundo botón del ratón sobre cada acción y le daremos a *“Añadir Vista”* para añadir las 4 vistas de nuestra aplicación.

#### Index

<div class="hero-unit"> <h1>Bienvenidos a nuestra pagina!</h1> <p> Texto principal de bienvenida a nuestra pagina. Prueba a redimensinar el navegador y comprobaras como se adapta el contenido </p> <p><a class="btn btn-primary btn-large">Mas informacion</a></p> </div> <div class="row-fluid"> <div class="span12"> <div class="page-header"> <h1>Productos destacados</h1> </div> </div> </div> <div class="row-fluid"> <div class="span4 prod"> <h2>Producto 3</h2> <p><span class="label label-success">Categoria 1</span></p> <p><em> Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id </em></p> @Html.Partial("_productButtons") </div> <div class="span4 prod"> <h2>Producto 12</h2> <p><span class="label label-success">Categoria 2</span></p> <p><em> Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id </em></p> @Html.Partial("_productButtons") </div> <div class="span4 prod"> <h2>Producto 15</h2> <p><span class="label label-success">Categoria 3</span></p> <p><em> Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla metus elit, egestas eu malesuada id </em></p> @Html.Partial("_productButtons") </div> </div>

<span lang="ES-TRAD">En esta vista hay tres secciones. Un*** hero-unit***, que viene a ser el cartel de presentación, una **cabecera** y una última sección donde se mostrarán tres productos destacados.</span>

<span lang="ES-TRAD">Lo que hay que resaltar de este código, aparte de que hay que mirarse la documentación de **bootstrap**, es lo que significan las clases ***row-fluid y span4*** en las que se basará todo nuestro esquema.</span>

<span lang="ES-TRAD">En primer lugar comentar que estas clases funcionan correctamente en el interior de un ***container-fluid***. Cada vez que añado un ****</span>

<div>*** row-fluid*** estoy indicando que se cree una nueva fila que llenaremos de contenidos y que ocupará el 100% dentro de nuestro*** container-fluid***. Dentro de cada ***row-fluid*** añadiremos secciónes con la clase spanX. Cada ***row-fluid*** se divide en 12 partes iguales (de manera predefinida) de forma que 3 ***span4***hacen un **span12**, o bien 4 ***span3***, harán un ***span12***. Es así de sencillo y la hoja con el esqueleto de**bootstrap**ya incluye todas las clases spanX necesarias para construir cualquier tipo de layout.  
 <span lang="ES-TRAD">Por tanto si quiero que mi cabecera ocupe todo el **row-fluid**, encerraré los contenidos en un ***span12***para ocupar las 12 partes, y si quiero que los tres productos se muestren en una fila y ocupen cada uno un tercio de la misma, cada producto ha de ir en un ***span4***. Si quisiera mostrar 4 productos los añadiría en 4 divs con la clase ***span3***, y si quiero mostrar 6, en 6 divs con las clase ***span2***. Es así de sencillo. </span>

<span lang="ES-TRAD">Cuando cambio la resolución del navegador, los contenidos se adaptan a la pantalla mostrándose en una sóla fila. Con esta sencilla configuración obtenemos una aplicación que está preparada para mostrarse en navegadores de escritorio y en dispositivos móviles manteniendo un aspecto visual interesante. **Bootstrap** facilita mucho las cosas para los programadores que no sean especialmente hábiles con las interfaces y que no tengan esa capacidad de crear experiencias de usuario y que es una parte vital de la aplicación web.</span>

#### Productos

<span lang="ES-TRAD">En esta vista mostraremos los **Productos **de cada categoria</span>

@model IEnumerable<MVCSPAApplication.ViewModels.ProductoVM> <div class="row-fluid"> <div class="span12"> <div class="page-header"> <h1>Mostrando productos<small>    @ViewBag.Categoria</small></h1> </div> </div> </div> <div class="row-fluid"> <div class="span4"> <div class="well helper-margin-top"> <ul class="nav nav-list"> <li class="nav-header"> Escoge una categoria </li> <li class="active"> <li>@Html.ActionLink("Productos categoria 1", "Productos", new { categoria=1 }, new { data_ajax = "true", @class="helper-display-inline" })</li> </li> <li> <li>@Html.ActionLink("Productos categoria 2", "Productos", new { categoria=2 }, new { data_ajax = "true", @class="helper-display-inline" })</li> </li> <li> <li>@Html.ActionLink("Productos categoria 3", "Productos", new { categoria=3 }, new { data_ajax = "true", @class="helper-display-inline" })</li> </li> </ul> </div> </div> @{ int counter = 0; } <div class="span8"> @foreach (var item in Model) { if (counter == 0) { @:<div class="row-fluid"> } <div class="span6 prod"> <h2>@item.Nombre</h2> <p><span class="label label-success">@ViewBag.Categoria</span></p> <p>@item.Descripcion</p> @Html.Partial("_productButtons") </div> counter++; if (counter==2) { @:</div> counter = 0; } } @if (counter != 0) { @:</div> } </div> </div>

<span lang="ES-TRAD">Tiene dos filas*** row-fluid***. <span></span>En la primera se muestra la cabecera. La segunda esta dividida en dos columnas. </span>

<span lang="ES-TRAD">En la primera de las columnas, un ***span4***, se permite seleccionar cada categoría. Los links serán ajax (***data-ajax=”true”***). En la segunda de las columnas, un ***span8***, se mostrarán los productos.</span>

<span lang="ES-TRAD">Dentro del ***span8***incluiremos filas con dos productos, por lo que se añadirá un ***row-fluid*** al span con dos ***span6 ***en su interior para definir cada fila. Esto lo hace el bucle **foreach****Razor**. </span>

<span lang="ES-TRAD">En esta vista estamos usando una vista parcial </span>*@Html.Partial(“_productButtons”) *<span lang="ES-TRAD">para los botones de cada producto, por tanto añadiremos esta vista parcial en *Views/Shared*</span>

<div class="btn-group"> <a class="btn btn-primary" href="#"><i class="icon-eye-open icon-white"></i> Detalles</a> <a class="btn btn-success" href="#"><i class="icon-shopping-cart icon-white"></i> Comprar</a> </div>

Además, cada producto lleva la clase ***.prod***para añadir un poco de estilo. Añadimos estas líneas en nuestro **Site.css**

/* Clase para cada producto mostrado */ .prod { background-color: #F5F5F5; position:relative; border: 2px solid #888888; -webkit-box-sizing: border-box; -moz-box-sizing: border-box; box-sizing: border-box; -moz-border-radius: 5px; border-radius: 5px; -moz-box-shadow: 10px 10px 5px #888; -webkit-box-shadow: 10px 10px 5px #888; box-shadow: 10px 10px 5px #888; padding:1em; margin-top:2% !important; }

#### Contacto

<div class="row-fluid"> <div class="span12"> <div class="page-header"> <h1>Contacto<small>    Datos de contacto</small></h1> </div> </div> </div> <div class="row-fluid"> <div class="tabbable"> <ul class="nav nav-tabs"> <li class="active"><a href="#1" data-toggle="tab">Contacto</a></li> <li><a href="#2" data-toggle="tab">Mail</a></li> <li><a href="#3" data-toggle="tab">Telefono</a></li> <li><a href="#4" data-toggle="tab">Fax</a></li> </ul> <div class="tab-content"> <div class="tab-pane active" id="1"> <p>Yago Perez Vazquez</p> </div> <div class="tab-pane" id="2"> <p>mimail@mail.com</p> </div> <div class="tab-pane" id="3"> <p>0034 123456789</p> </div> <div class="tab-pane" id="4"> <p>0034 987654321</p> </div> </div> </div> </div>

<span lang="ES-TRAD">Se generarán dos filas*** row-fluid***. Una con la cabecera y otra con los datos. Para mostrar los datos se usan ***tabs***. Como decía anteriormente, mirar la documentación de **bootstrap** porque es simplemente copiar y pegar y está todo muy bien explicado.</span>

#### Acerca de

<div class="row-fluid"> <div class="span12"> <div class="page-header"> <h1>Acerca de</h1> </div> </div> </div> <div class="row-fluid"> <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Morbi sagittis lectus sed quam luctus rhoncus. Pellentesque habitant morbi tristique senectus et netus et malesuada fames ac turpis egestas. Cras venenatis aliquam condimentum. Vestibulum est risus, volutpat ut volutpat vel, mattis id ligula. Aenean neque elit, molestie vitae pulvinar sed, vestibulum non nisl. In metus purus, tincidunt sit amet posuere ac, euismod et mauris. Vestibulum neque libero, tristique non mollis pellentesque, consequat non eros. Sed et enim eget urna ultrices fringilla non ut erat. In porta tristique varius. Fusce ultrices tempor dignissim. Cras sed nibh eget libero blandit luctus eu at ante.</p> <p>Suspendisse mauris dolor, congue quis gravida ut, sagittis sit amet nisl. Ut pharetra velit in libero ultrices ut sagittis enim placerat. Integer non nunc faucibus odio volutpat sodales. Etiam facilisis, dolor eu dapibus porta, nunc urna fermentum magna, eget tincidunt augue lorem sit amet felis. Proin faucibus, elit sit amet tincidunt vestibulum, ante enim tristique neque, ac sodales magna nunc id est. Praesent nec eros sit amet tortor rutrum aliquam eget in augue. Aenean commodo, arcu et luctus laoreet, sapien arcu luctus nisl, in iaculis lacus magna et nisl. Nunc placerat volutpat iaculis. In placerat sodales dui faucibus tincidunt. Vestibulum vulputate dignissim mollis. Morbi consectetur facilisis vulputate. Aenean sagittis nibh a enim hendrerit luctus in et mi. Sed justo mauris, commodo sit amet auctor interdum, placerat a mauris.</p> <p>Phasellus augue eros, semper vulputate sagittis eu, facilisis ac risus. Nam faucibus, odio sit amet adipiscing condimentum, turpis mi feugiat urna, vel lobortis felis ipsum eu turpis. Curabitur pharetra dapibus leo, in faucibus ipsum gravida ac. Nulla tincidunt, nisl ac suscipit vehicula, dui tellus convallis dolor, at iaculis eros erat et eros. Pellentesque mauris nibh, bibendum vitae condimentum vel, blandit vitae elit. Pellentesque lobortis eros sem. Integer lobortis velit tincidunt nisi dapibus commodo. Nulla ultricies turpis in lacus congue id iaculis ante hendrerit.</p> <p>Mauris non neque vel mi dictum ultrices. Proin vehicula odio rutrum sem semper id semper mi sagittis. Vestibulum dignissim molestie metus id fringilla. Vestibulum ante ipsum primis in faucibus orci luctus et ultrices posuere cubilia Curae; Maecenas quis ante vitae massa facilisis convallis. Curabitur feugiat ipsum id tortor elementum imperdiet. Curabitur at ipsum eu mi auctor fringilla et a sem. Duis non quam leo, et ultrices justo. Phasellus porta varius odio, eu pulvinar odio tincidunt id. Curabitur at mauris lectus, in tincidunt felis.</p> <p>Class aptent taciti sociosqu ad litora torquent per conubia nostra, per inceptos himenaeos. Praesent id erat enim, sed convallis nisl. Proin quis libero sed ligula hendrerit vestibulum. Ut nulla eros, aliquam non euismod nec, molestie at enim. Aenean mattis sagittis est, in ullamcorper sapien placerat eu. Sed blandit ante eu velit sagittis non adipiscing leo mollis. Vestibulum non lacus quam, nec semper nunc. Donec ac dui purus, vel placerat enim. Donec ornare orci est. Ut at accumsan nunc. Aenean aliquet, lorem in egestas mattis, elit leo feugiat nulla, in porttitor turpis mi sed augue. Proin nunc nunc, pellentesque sit amet mattis at, ornare a dolor. Donec nisi eros, vestibulum ut condimentum a, fermentum molestie urna. Ut risus ligula, ornare sed faucibus non, dignissim sed metus. Sed semper pellentesque porttitor.</p> </div>

<span lang="ES-TRAD">Aquí tenemos dos filas ***row-fluid***. Como siempre, una cabecera, y a continuación una serie de párrafos* “Lorem ipsum” …*</span>


## <span lang="ES-TRAD">El toque final</span>

<span lang="ES-TRAD">Para que todo lo anterior funcione con propiedad en una aplicación ASP MVC, sólo se debe renderizar todo el **_Layout** cuando la petición no es ajax, en caso de sí ser una petición ajax sólo vamos a modificar el contenido de ***container-fluid. ***Esto lo podemos hacer en el ***_ViewStart.cshtml*** de nuestra aplicación ASP MVC de manera que preguntamos si la petición es ajax o no mediante *Request.IsAjaxRequest().*</span>

En caso de que nuestros links tengan el atributo **data-ajax=true** se cargará solamente el contenido de la vista recuperada del servidor. En el caso contrario se cargará el contenido de la vista con el **_Layout.**

<span lang="ES-TRAD">Si no hago esto, lo que pasará es que en nuestro ***container-fluid***se cargará de nuevo el **_Layout** de nuestra aplicación de manera que lo tendremosduplicado y la aplicación dejará de funcionar correctamente.</span>

<span lang="ES-TRAD">El código de ***_ViewStart.cshtml*******sería</span>

@{ if (!Request.IsAjaxRequest()) { Layout = "~/Views/Shared/_Layout.cshtml"; } }


## <span lang="ES-TRAD">Resultado final*  
*</span>

<span lang="ES-TRAD">Con esto, ya tenemos todo lo que necesitamos para ejecutar nuestra aplicación.</span>

<span lang="ES-TRAD">Pues nada, compilamos y ejecutamos nuestra solución y si todo ha ido bien no deberían salir errores. Deberíamos por tanto estar navegando por [esta aplicación](http://mvcspaapplication.apphb.com/ "Aplicación ajax de ejemplo")  
</span>

Pulsando en los diferentes links navegamos en modo ajax, siempre que tengamos un navegador que soporte history!!!, pero está claro que si estás leyendo esto tendrás ese navegador …

Si comprobamos las peticiones/respuestas con una herramienta como **Firebug**, veremos que se están haciendo mediante ajax y que no hay recarga completa de página. Esto hace mejorar la experiencia del usuario de manera notable, además de reducir los tiempos de carga de las páginas.


## Resumen

En estos post hemos visto como crear una aplicación ASP MVC que funcionen en modo **Single Page Application** utilizando la factoría de widgets de **jquery, modernizr y bootstrap.**

Hasta pronto!!

****

</div></div>
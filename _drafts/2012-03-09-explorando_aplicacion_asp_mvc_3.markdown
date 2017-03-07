---
layout: post
title: Explorando un aplicación ASP MVC 3
date: '2012-03-09 01:40:49'
---


Cuando creamos una nueva aplicación ASP MVC 3 desde Visual Studio, se genera una estructura de carpetas por defecto. En este post vamos a intentar desmenuzar esta estructura generada por la plantilla por defecto.

****Cuando creamos una nueva aplicación ASP MVC 3 en Visual Studio se genera una estructura de carpetas como la que se puede ver en la siguiente imagen

![Estructura de un proyecto ASP MVC 3](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/4efa2f0a-b475-4321-a807-8313927dd6d1_ASP_MVC_3_Layout.jpg "Estructura de un proyecto ASP MVC 3")

**  
**Vamos a desmenuzar esta estructura para ver para qué sirve cada carpeta y qué contiene. De arriba a abajo podemos ver lo siguiente

**Properties. **La carpeta de propiedades del proyecto, como en cualquier otro proyecto ASP .NET

**References**. La carpeta que contiene las dependencias necesarias para el funcionamiento de la aplicación web.

**App_Data. **Esta es la carpeta donde guardaremos los datos de nuestra aplicación web que se mantengan localmente. Normalmente encontraremos bases de datos (.mdf, .sdf), archivos de datos (xml …) y cualquier tipo de fichero que actúe como almacén de datos para nuestra aplicación

**Content. **Aquí se guardará cualquier tipo de contenido estático que se vaya a mostrar en la web que estemos construyendo, esto es, imágenes, archivos css, iconos, documentos pdf, videos, …etc. Lo normal es crear subcarpetas dentro con los diferentes tipos de recursos de la aplicación. Por defecto vemos que aquí se incluye la hoja de estilos del sitio, ***Site.css*** y una subcarpeta ***themes***que contiene el tema por defecto de **jquery ui**

**Controllers. **Aquí almacenaremos nuestros controladores. Esta carpeta se llama así por convención por lo que es obligatorio guardarlos aquí. Por defecto se incluyen un par de controladores, ***AccountController*******(Gestión de alta de cuentas) y ***HomeController*** (Controlador que devuelve la vista de inicio y la vista “acerca de”)

**Models. **Esta carpeta no sirve para nada :), al menos en una aplicación que no sea básica. Esta carpeta está presente para albergar nuestro modelo de datos, clases, diagrama edmx, etc … Podemos guardar nuestro modelo en esta carpeta o en cualquier otra, no es imprescindible almacenarlo aquí. Cuando vamos a crear una aplicación de cierta complejidad, esta carpeta ya podéis borrarla directamente ya que lo que vamos a hacer es crear una serie de capas que aíslen nuestra aplicación web del modelo de datos y por tanto crearemos nuevos proyectos que contengan nuestro modelo, infraestructura y capas de servicios y los referenciaremos desde la vista

**Scripts. **En esta carpeta se guardarán los scripts que se usan en la aplicación. Podemos almacenarlos aquí o no. No es obligatorio. Si no nos gusta el nombre lo cambiamos e incluiremos nuestros scripts en donde los queramos situar. Por defecto podemos ver que se incluyen un montón de scripts que se usan de forma común en cualquier aplicación web, como por ejemplo [***jquery***](http://jquery.com/ "Página oficial de jquery"),[***jquery ui***](http://jqueryui.com/ "Página oficial de jquery ui"), [***modernizr***](http://www.modernizr.com/ "Página oficial de modernizr"), etc. Observad que **jquery** se incluye por defecto de manera oficial junto con los diferentes archivos minimizamos, sin mimimizar y vsdoc, este último tipo para que dispongamos de intelligence en nuestros desarrollos

**Views. **Esta carpeta se llama así por convención. Aquí guardaremos las vistas de nuestra aplicación, que pueden estar escritas usando **asp** clásico o **Razor**. El que no conozca **Razor** lo invito a probarlo porque mejora enormemente la fluidez en la programación. Lo malo es que no tiene diseñador … pero … ¿quién lo necesita teniendo la tecla F5 en el navegador y [**Firebug**](http://getfirebug.com/ "La mejor herrramienta para desarrollo en el cliente ...")?. Al igual que la carpeta ***Controllers*** es obligatorio mantener una estructura en ella de manera que los controladores encuentren las vistas adecuadas.

Por tanto, en esta carpeta se encontrarán archivos de tipo .***aspx*** (Vistas) o .***ascx***(Vistas parciales) en caso de haber escogido el motor de webforms, o bien ***.cshtml*** en caso de ser **Razor** el elegido.

Esta carpeta contendrá a su vez una serie de subcarpetas, una por controlador, ya que es aquí donde los controladores buscarán las vistas asociadas cuando se haga referencia a las mismas a través de las acciones alojadas en cada uno. Además contiene una carpeta ***Shared***, en la que normalmente encontraremos plantillas de vistas (Master pages en el caso de webforms o Layouts en el caso de Razor) y vistas parciales, es decir, componentes que no están asociados a ningún controladorde forma específica y que son compartidos.

Por defecto, en la plantilla se incluyen carpetas para los controladores ***HomeController***y ***Accoun****tController***, las carpetas se llamarán de la misma manera que el controlador pero sin la palabra “Controller” por convención. Además vemos que en la carpeta ***Shared ***se encuentran los archivos de

*** Error***, que será la vista mostrada cuando se produzca una excepción,

*** LogOnPartial***, una vista parcial para iniciar sesión

*** Site.Master***(_Layout en caso de razor) , la Master Page del sitio

**Global.asax. **Archivo de gran importancia en el que se efecúa la configuración del sistema de routing, definición de filtros globales … etc.

**Web.config. **El típico archivo de configuración web, como en cualquier aplicación webforms

Pues hasta aquí hemos llegado con este pequeño resumen de la estructura de la plantilla de una aplicación ASP MVC 3. Ahora sólo queda comenzar a crear controladores y vistas!!

Hasta pronto!!



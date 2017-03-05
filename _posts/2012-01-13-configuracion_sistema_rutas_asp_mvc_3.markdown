---
layout: post
title: Configuración del sistema de rutas en ASP MVC 3
date: '2012-01-13 07:35:14'
tags:
- asp-mvc-routing
---


El sistema de routing es el controlador frontal de una aplicación ASP MVC.

Cualquier url introducida en el navegador pasará por el sistema de rutas, este se encargará de analizar la información que contiene la url y de compararla con la tabla de rutas y así derivar la petición al controlador que corresponda.

El sistema de routing es una parte fundamental en cualquier desarrollo ASP MVC y se debe comprender bien su funcionamiento para evitar errores en las tablas de rutas de nuestras aplicaciones.

Todas las peticiones que llegan a una aplicación ASP MVC, pasan en primer lugar por la verificación correspondiente contra la tabla de rutas que hayamos definido. Esto hace que la correcta definición de la tabla de rutas sea imprescindible para el funcionamiento de la aplicación ASP MVC.

#### Definición de la tabla de rutas

La tabla de rutas de una aplicación ASP MVC 3 se define en el fichero Global.asax.cs.

En primer lugar se añadirá la llamada a un método en el Application_Start()

 protected void Application_Start() { //Iniciar la tabla de rutas RegisterRoutes(RouteTable.Routes); }

Como se puede observar lo único que se hace es llamar al método RegisterRoutes() pasándole como parámetro la tabla de rutas del sistema.

El método RegisterRoutes() se define de la siguiente manera (Tal y como se puede ver por defecto cuando creamos un nuevo proyecto ASP MVC 3 con Visual Studio)

 public static void RegisterRoutes(RouteCollection routes) { routes.IgnoreRoute("{resource}.axd/{*pathInfo}"); // Importante!! => Las rutas definidas en la tabla de rutas se procesan secuencialmente // El orden importa routes.MapRoute( "Administration", // Route name "Admin/{controller}/{action}/{id}", // URL with parameters new { controller = "Post", action = "Admin", id = UrlParameter.Optional } // Parameter defaults ); }

En el código anterior se están haciendo dos cosas:

- En primer lugar se indican las rutas que van a ser ignoradas, es decir, cuando se reciba una petición que se corresponda con el patrón especificado simplemente se ignorará. Por defecto, en la plantilla de Visual Studio, se incluye el patrón que evita el acceso a los recursos .axd

- En segundo lugar, se definen las rutas mediante el método MapRoute() al que se le pasan los parámetros nombre de la ruta, patrón de la URL, valores por defecto, restricciones y por último espacios de nombres en donde se encuentran los controladores

#### Parametros de la tabla de rutas

***Nombre de la ruta***. El nombre de la ruta será aquel que nos permitirá identificarla de manera única. Por tanto no admite duplicados y el nombre ha de ser único para cada uno de los MapRoute() que incluyamos. Esto nos permitirá obtener direcciones URL para, por ejemplo, utilizar con helpers como Html.RouteLink().

 routes.MapRoute( "Default", // Nombre de la ruta "{controller}/{action}/{id}", // URL con parámetros new { controller = "Home", action = "Index", id = UrlParameter.Optional } // Parametros por defecto );

En el código anterior definimos la ruta por defecto, con un patrón de url que cumpliría con la siguiente URL http://www.midominio.com/Automovil/Index/4. Se incluyen los parámetros por defecto que hacen que una petición a http://www.midominio.com nos termine enrutando a http://www.midominio/Home/Index.

***Patrón de la URL***. Es el formato de la URL. Cuando exista una petición entrante se utiliza para determinar si dicha petición se procesará como se indica en el mapeo. También se utilizará para generación de links con helpers como Html.ActionLink(), siendo en este caso utilizada como plantilla de generación de la url. El patrón codificado en el ejemplo es “{controller}/{action}/{id}”, siendo {controller} un controlador cualquiera de nuestra aplicación, {action} una acción cualquiera dentro del controlador y {id} un parámetro del método de acción. También se puede observar que en los parámetros por defecto este {id} se indica como opcional por lo que de esta manera con este patrón se pueden procesar peticiones del estilo de:

```
<p><a href="http://www.midominio.com/Automovil/Index%C3%82%C2%A0">http://www.midominio.com/Automovil/Index </a> =>  Que nos podría llevar a un índice de todos los automóviles<br /><a href="http://www.midominio.com/Automovil/Edit/3%C3%82%C2%A0">http://www.midominio.com/Automovil/Edit/3 </a> => Con la cual editaríamos el automóvil  con identificador 4</p>
```

aunque también encajaría una petición más compleja como la siguiente:

```
<p><a href="http://www.midominio.com/Automovil/Index?page=0&sort=datecreated&sortdir=DESC&searchstring=bmw%20">http://www.midominio.com/Automovil/Index?page=0&sort=datecreated&sortdir=DESC&searchstring=bmw </a></p>
```

mediante la cual podríamos visualizar los automóviles que contienen el texto “bmw” ordenados por fecha de creación descendente. Esto se puede hacer debido a que el parámetro {id} es opcional. En el método de acción del controlador definiremos todos estos parámetros de la siguiente manera:

public ActionResult Index(int? page, string sort, string sortdir,string searchstring)

Podemos jugar con el patrón de la manera que queramos. Podríamos por ejemplo introducir este patrón:

```
<p>catalogo/{marca}/{modelo}</p>
```

con el que una petición como esta, http://www.midominio.com/catalogo/mercedes/A3/ encajaría perfectamente, recuperando los “mercedes” de tipo “A3” que existen en el catálogo. El sistema de routing se encarga de hacer el emparejamiento de los parámetros de la petición, marca=mercedes, modelo=A3.

***Valores por defecto de los parámetros***. No es necesario que una petición incluya todos los parámetros indicados en el patrón. Esto se permite gracias a los parámetros por defecto. Dado el código de definición de una ruta del que habíamos partido:

 routes.MapRoute( "Default", // Nombre de la ruta "{controller}/{action}/{id}", // URL con parámetros new { controller = "Home", action = "Index", id = UrlParameter.Optional } // Parametros por defecto );

peticiones del tipo *http://www.midominio.com/* o *http://www.midominio.com/Home* encajan con la plantilla de la ruta debido a que el resto de parámetros se completan con los por defecto. Por tanto las peticiones anteriores nos llevan ambas a *http://www.midominio.com/Home/Index*.  
Para el correcto funcionamiento de la aplicación todas las rutas deben contener el controlador y la acción. Por esta razón, si en el patrón no se incluye {controller} y {action} se debe especificar en los parámetros por defecto

 routes.MapRoute( "Automoviles", // Nombre de la ruta "catalogo/{marca}/{modelo}", // URL con parámetros new { controller = "Automovil", action = "Index" } // Parametros por defecto );

***Restricciones.*** Las restricciones permiten filtrar valores sobre las rutas actuando sobre parámetros u otros valores de manera que si no cumplen con la restricción se descarta la ruta. Se pueden definir de dos maneras, o bien mediante expresiones regulares o bien con una clase que implemente IRouteConstraint.

Mediante expresiones regulares:

 routes.MapRoute( "Default", // Nombre de la ruta "{controller}/{action}/{id}", // URL con parámetros new { controller = "Home", action = "Index", id = UrlParameter.Optional } // Parametros por defecto, new { id = @"\d{1,5}" } //Restricciones );

La expresión filtra la ruta de manera que los valores del parámetro id han de contener obligatoriamente entre 1 y 5 dígitos

Mediante una clase:

public class AutomovilRouteConstraint : IRouteConstraint { private readonly IAutomovilRepository automovilRepository = new AutomovilRepository(); public bool Match(HttpContextBase httpContext, Route route, string parameterName, RouteValueDictionary values, RouteDirection routeDirection) { if (!values.ContainsKey(parameterName)) return false; var automovil = (string)values[parameterName]; return slugRepository.Exists(automovil); } }

En el código anterior se filtra la existencia de un automóvil en un repositorio de automóviles:

routes.MapRoute("Automoviles", "{automovil}", new { controller = "Automovil", action = "View" }, new { slug = new AutomovilRouteConstraint() });

***Espacios de nombres de controladores.*** Para evitar conflictos entre controladores con el mismo nombre se puede especificar el espacio de nombre donde se han de localizar los mismos:

 routes.MapRoute( "Default", // Nombre de la ruta "{controller}/{action}/{id}", // URL con parámetros new { controller = "Home", action = "Index", id = UrlParameter.Optional } // Parametros por defecto, new[] { "MiAplicacion.Controllers" } );

Una de las cosas más importantes a recordar es que la tabla de rutas se procesa secuencialmente según la hemos definido en nuestro código. Por tanto es fácil cometer errores en la definición de la misma y que una petición que tenemos previsto procesar con una plantilla acabe procesandose con una anterior que también encaja. Ojo con esto!!



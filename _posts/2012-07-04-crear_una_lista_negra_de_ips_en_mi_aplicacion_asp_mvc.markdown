---
layout: post
title: Crear una lista negra de Ip´s en mi aplicación ASP MVC
date: '2012-07-04 08:43:08'
tags:
- ip-blacklist
- seguridad
- asp-mvc-filters
---


En este post vamos a crear un filtro ASP MVC que puede usarse con cualquier acción, controlador o como filtro global y que permitirá rechazar direcciones Ip que no queremos que accedan a nuestro contenido.

Un filtro de ips es simplemente un mecanismo que analiza las direcciones ip entrantes y las desvía o bien las acepta en función de lo que se pretenda hacer.

Los mecanismos basados en **lista negra** (black list) lo que hacen es restringir el acceso a las ip que hayamos incluido en la lista (fichero, base de datos …) de ips. Por el contrario los mecanismo de **lista blanca** (white lists) permiten el acceso sólo a las direcciones ip que están en dicho repositorio.

Los filtros de ips nos pueden ayudar con la seguridad de nuestro sitio web. En este post vamos a ver como implementar un filtro de ip basado en lista negra.

Para crear esta lista negra vamos a hacer lo siguiente:

1. Creamos un fichero llamado **IPBlackList.txt** en alguna carpeta de nuestro servidor para almacenar la lista de ipso

2. Creamos una **clase repositorio** que acceda al fichero y se traiga la lista de ips a memoria
3. Creamos un **filtro ASP MVC** que impida el acceso al servidor a las direcciones ips que se encuentren en la lista

4. Añadimos el filtro a la colección de **filtros globales** de nuestra aplicación para que se aplique el filtro sobre todas las acciones de nuestro sistema de rutas



### Creamos un fichero

En primer lugar vamos a crear un fichero en alguna carpeta de nuestro servidor. Por ejemplo en ***/root/files/blacklist/ipblacklist.txt.***

Almacenamos las direcciones ip que no nos interesa que accedan al servidor separadas por “;”.

### **Definimos una nueva clase Repositorio que lea el fichero**

Creamos una clase repositorio para leer el fichero

```language-javascript
namespace MyApp.Helpers
{
    public class BlackListRepository
    {
        public static IQueryable<string> Ips {get; set;}

        public static void GetAllIpsInBlackList(HttpServerUtility server)
        {
            string reader = File.ReadAllText(server.MapPath("~/files/blacklist/ipblacklist.txt"));
            Ips = reader.Split(new char[] { ';' }).AsQueryable();
        }
    }
}
```

Esta clase contiene simplemente un método que retorna una lista de ips que se almacena en memoria para que sea accesible entre peticiones y sólo se cargue una vez (static).

### Creamos el filtro ASP MVC

A continuación añadimos una nueva clase que será nuestro filtro ASP MVC

```language-javascript
namespace MyApp.Filters
{
    public class IPHostValidationAttribute : ActionFilterAttribute
    {
        public override void OnActionExecuting(ActionExecutingContext filterContext)
        {
            string userIP = filterContext.HttpContext.Request.UserHostAddress;
            bool anyIpInBlackList = BlackListRepository.Ips.Any(x => x == userIP);
            if (anyIpInBlackList)
            {
                throw new Exception("Acceso denegado");
            }
            base.OnActionExecuting(filterContext);
        }
    }
}
```

El filtro es muy sencillo. Simplemente comprueba en nuestra clase estática si la dirección ip se encuentra en el fichero y en ese caso lanza una excepción.

### Hacemos que el filtro sea global

Por último, vamos a manipular el ***Global.asax*** para que el filtro sea global y se aplique a todos los controladores y acciones

Registramos el filtro como global

```language-javascript
filters.Add(new IPHostValidationAttribute());
```

En *<span class="nf">Application_Start</span><span class="p">()</span>* añadimos la siguiente linea para leer el fichero y almacenar su contenido en memoria

```language-javascript
BlackListRepository.GetAllIpsInBlackList(this.Server);
```

Con esto tendríamos un filtro de ip preparado y aplicándose en todas las acciones y controladores de nuestra aplicación web.

Hasta pronto!!



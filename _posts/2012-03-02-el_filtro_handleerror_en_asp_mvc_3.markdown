---
layout: post
title: El filtro [HandleError] en ASP MVC 3
date: '2012-03-02 02:28:28'
tags:
- asp mvc
categories:
- .NET
---


El filtro [HandleError] pemite gestionar las excepciones producidas en la ejecución de las acciones de un controlador en una aplicación ASP MVC

El atributo **[HandleError]** ([*HandleErrorAttributte*](http://msdn.microsoft.com/es-es/library/system.web.mvc.handleerrorattribute.aspx "Representa un atributo que se utiliza para controlar una excepción producida por un método de acción")) permite capturar las excepciones que se produzcan en las acciones de nuestros controladores y en caso de producirse, enviar al usuario a una página de error según se haya definido en los parámetros del atributo.

Si no defino ningún parámetro entonces se aplicará sobre cualquier excepción que se produzca y derivará al usuario a la vista por defecto definida en ***/Views/Shared*** con nombre ***Error.cshtml.***

Por ejemplo, si tengo está acción que lanza una excepción cualquiera

```c
[HandleError] public ActionResult LanzarError() { throw new Exception(); }
```

Como he decorado la acción *LanzarError()* con el atributo **[HandleError]** y en el interior de la acción estoy lanzando una excepción, la ejecución de la acción derivará al usuario a la vista de error por defecto, la que hemos comentado arriba.

El atributo **[HandleError]** admite varios parámetros que permiten indicar el tipo de excepción a controlar, el tipo de vista a utilizar o bien el orden de verificación del error.

En el siguiente ejemplo vamos a controlar algunas excepciones que se enviarán a vistas distintas e indicaremos también el orden en el que se han de verificar los errores

```c
[HandleError(ExceptionType=typeof(MembershipCreateUserException), View="ErrorCreacionUsuario", Order=10)]
[HandleError(ExceptionType=typeof(MembershipPasswordException), View="ErrorPassword", Order=20)]
[HandleError(ExceptionType=typeof(ArgumentNullException), View="ErrorArgumento", Order=30)]
public ActionResult LanzarError(string param1) { 
    //Cuerpo de la acción 
}
```

Como podemos observar, se pueden añadir múltiples atributos controlando las diferentes excepciones que se puedan producir y derivando las mismas a diferentes vistas. El parámetro *Order* indica el orden de comprobación.

El atributo **[HandleError]** se puede también utilizar a nivel de controlador. La sintaxis es la misma y lo que hará es controlar las excepciones de cualquiera de las acciones del controlador.

Cuando mezclamos atributos a nivel de controlador y a nivel de acción es cuando el parámetro *Order* adquiere más importancia ya que si no lo establezco, se procesarán primero las excepciones especificadas a nivel de controlador y después las establecidas a nivel de acción. Si he puesto una excepción genérica a nivel de controlador, siempre se procesará primero que cualquiera de las establecidas a nivel de acción.

Recalcar también que nada de esto funciona si no establezco en mi **Web.config customError** a **“on”**. Si no pongo esta línea, las excepciones provocarán el típico pantallazo de error del IIS. Este pantallazo ya nos interesará cuando estemos desarrollando la aplicación, pero no será así con la aplicación en producción

<system.web><customerrors mode="on"></customerrors></system.web>

Por último, comentar que desde la salida de **ASP MVC 3**, en la plantilla por defecto se incluye el atributo [HandleError] como un [**filtro global**](../../../Post/GetPostByCode/filtros_globales_ASP_MVC_3 "Los filtros globales"). Los filtros globales se definen en el Global.asax

```c
public static void RegisterGlobalFilters(GlobalFilterCollection filters) 
{ 
    filters.Add(new HandleErrorAttribute()); 
}
```

Los filtros globales definidos en *RegisterGlobalFilters()* se aplican sobre todas las acciones de todos los controladores de la aplicación ASP MVC. Esto quiere decir que simplemente estableciendo **customErrors** a **“on”**, cuando se produzca una excepción, nos derivará a la vista por defecto “Error” hayamos o no especificado el aributo **[HandleError]** en la acción o controlador. Esto es mucho más cómodo ya que antes los controladores y acciones tenían que marcarse con infinidad de atributos **[HandleError].**

Por tanto incluiremos los atributos que nos enviarán a vistas especiales con errores específicos que querramos manejar ….

Hasta pronto!!



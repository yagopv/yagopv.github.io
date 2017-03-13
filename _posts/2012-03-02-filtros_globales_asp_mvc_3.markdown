---
layout: post
title: Los filtros globales
date: '2012-03-02 11:44:30'
tags:
- asp mvc
categories:
- .NET
---


Los filtros globales evitan que añadamos a los controladores y acciones, multitud de atributos para realizar tarea comunes de una manera repetitiva.

Con la llegada de ASP MVC3, se introdujo un nuevo concepto, los **filtros globales**. Hasta entonces, la única manera de aplicar un filtro a todas las acciones de todos los controladores de ASP MVC era repitiendo el atributo a nivel de contralador sobre todos los controladores de mi aplicación.

Ahora, en ASP MVC 3, aparece el concepto de **filtro global**, que se establece una única vez en el fichero global.asax.cs y que se aplicará sobre todas las acciones de todos los controladores.

El ejemplo más claro de esto es el atributo **[HandleError]**, que se incluye como filtro global en la plantilla por defecto de una aplicación ASP MVC 3. En el pasado era necesario aplicar este filtro en multitud de sitios. Ahora, estableciéndolo como global, la gestión de excepciones es automática y sólo se han de añadir aquellas excepciones más específicas que se quieran controlar.

Si miramos el código de una archivo **global.asax.cs** de la plantilla por defecto vemos, entre otras cosas, el siguiente código

```c
public static void RegisterGlobalFilters(GlobalFilterCollection filters) {
  filters.Add(new HandleErrorAttribute()); 
}
protected void Application_Start() {
  RegisterGlobalFilters(GlobalFilters.Filters);
}
```

Lo que se hace en el método *RegisterGlobalFilters()* es registrar el atributo *[HandleErrorAttribute]*
como global, añadiéndolo a la colección de filtros globales *[Filters]* del objeto estático *[GlobalFilters]*.

En resumen, los filtros globales son un método eficaz de economizar código y de evitar errores de codificación

Hasta pronto!!



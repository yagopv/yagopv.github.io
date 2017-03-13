---
layout: post
title: El filtro [Authorize]
date: '2012-03-08 02:57:05'
tags:
- asp mvc
categories:
- .NET
---


Este filtro permite establecer restricciones de acceso a ciertas acciones de los controladores en caso de que los usuarios no tengan permiso para ejecutarlas.

El filtro [**[Authorize]**](http://msdn.microsoft.com/es-es/library/system.web.mvc.authorizeattribute.aspx "Representa un atributo que se utiliza para restringir el acceso por llamadores a un método de acción") es de gran importancia en cualquier aplicación **ASP MVC**. Nos pemitirá establecer las validaciones de acceso en nuestros controladores a partir de los permisos que tenga el usuario que está intentando acceder.

Puede aplicarse tanto a nivel de controlador cómo en acciones concretas. Si se aplica a nivel de controlador, el filtro se aplica sobre todas las acciones que contiene.

Vamos a ver unos ejemplos de uso …

```c
[Authorize] 
public ActionResult Accion1() { 
  return View(); 
} 

[Authorize(Roles="Admin, Premium")] 
public ActionResult Accion2() { 
  return View(); 
} 

[Authorize(Users="admin., admin2")] 
public ActionResult Accion3() { 
  return View(); 
} 

public ActionResult Accion4() { 
  return View(); 
}
```

***Accion1***, estaría restringida a los usuarios que hayan iniciado sesión.

***Accion2***, sólo la podrán ejecutar aquellos usuarios que tengan los roles Admin o Premium.

***Accion3***, está restringida los usuarios con nombre de usuario admin. o admin2.

***Acción4*** no está decorada con el atributo **[Authorize]** permitiendo por tanto el acceso a cualquier usuario, sea anónimo o no.

Las respuesta generada por este filtro en caso de estar permitido el acceso a la acción es un código HTTP 401 redirigiendo al usuario a la página indicada en el **web.config** en cado de haber sido configurado.

<authentication mode="Forms"><forms loginurl="~/Account/LogOn"></forms></authentication>

Hasta pronto!!



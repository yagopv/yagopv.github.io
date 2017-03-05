---
layout: post
title: El filtro [ChildActionOnly]
date: '2012-03-09 01:39:36'
tags:
- asp-mvc-filters
---


El filtro que vamos a comentar en este post permite restringir el acceso a ciertas url de nuestr dominio para que no puedan se llamadas “desde fuera” de nuestra aplicación.

El atributo [[ChildActionOnly]](http://msdn.microsoft.com/en-us/library/system.web.mvc.childactiononlyattribute.aspx "El  filtro ChildActionOnly restringe el acceso a ciertas url") restringe el acceso a las url marcadas con este atributo desde fuera de nuestro código.

Si creamos un controlador con una acción cualquiera, la decoramos con este atributo y ejecutamos nuestra aplicación, y a continuación tecleamos en el navegador la url de la acción nos derivará a una página de error

[ChildActionOnly] public ActionResult ChildAction() { return PartialView(); }

El código anterior provoca que si se produce una petición hacia la accion *ChildAccion* directamente desde eel exterior de nuestra aplicación se genere una excepción prohibiendo el acceso a los recursos. Este atributo permite establecer por tanto un mecanismo de control de acceso sobre ciertas partes de nuestra aplicación.

Por tanto, la única manera de acceder a los recursos que devuelven las acciones decoradas con **[ChildActionOly] **es mediante los helpers siguientes

```
<p> <a title="Invoca un método de acción secundario y presenta el resultado alineado en la vista primaria" href="http://msdn.microsoft.com/es-es/library/system.web.mvc.html.childactionextensions.renderaction.aspx" target="_blank">@Html.RenderAction(...)</a></p>
<p> <a title="Invoca una child action y devuelve el resultado como un HTML string" href="http://msdn.microsoft.com/en-us/library/system.web.mvc.html.childactionextensions.action.aspx" target="_blank">@Html.Action(...)</a></p>
```

Estos helpers renderizan vistas parciales en el navegador cliente. La diferencia entre ambos es que **RenderAction** renderiza directamente sobre el canal de salida (**Response**), lo que es más eficiente en caso de devolver gran cantidad de HTML y **Action** devuelve un string con el resultado. En este [post](http://msdn.microsoft.com/es-es/library/ee839451.aspx "Crear una vista de ASP.NET MVC llamando a varias acciones") podemos ver algo más acerca de la diferencia entre ambos.

Hasta pronto!!



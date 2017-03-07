---
layout: post
title: Uso de atributos data-* con Html Helpers en ASP MVC
date: '2012-04-04 03:47:46'
tags:
- asp-mvc-helpers
---


Al intentar añadir un atributo data-* a un helper ASP MVC se produce un error de compilación. En este post se indica como librarse de este error y el porqué se produce.

El otro día estaba intentando ajaxsificar un sitio web y necesitaba añadir atributo a mis links del tipo ***data-ajax=”true”.***

Mi sorpresa fue que cuando intentaba añadirlos al objeto ***htmlattributes*** de *RouteLink() *no me lo permitía indicando un error de compilación ¿Porqué?

Los guiones no se pueden usar como parte del nombre de una variable en .NET por lo que el compilador nos avisa de que no podemos. ¿Cómo lo hacemos entonces? Para los que sean vagos como yo y no les apetezca cambiar los RouteLink() por <a></a> que sepan que si declaro el atributo con guión bajo ***data_ajax=”true”*** se renderizará correctamente como ***data-ajax=”true”***

******Hasta pronto!!



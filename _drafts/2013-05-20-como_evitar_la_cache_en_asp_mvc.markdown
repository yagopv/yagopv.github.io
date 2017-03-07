---
layout: post
title: Como evitar la cache en ASP MVC
date: '2013-05-20 05:49:21'
tags:
- asp-mvc-filters
---


En ciertas ocasiones, necesitaremos asegurar que las peticiones no se sirvan desde la cache.

Vamos a ver una forma de evitar la cache mediante el uso de un atributo.

<span style="font-size: 1em; line-height: 1.6em;">Hay ocasiones en las que podemos necesitar que una vista no se sirva nunca desde la caché debido a cualquier razón como por ejemplo que se ejecuten scripts inline en una vista retornada mediante AJAX o similar.</span>

<span style="font-size: 1em; line-height: 1.6em;">Si queremos asegurar esto, podemos decorar la acción, controlador o incluso la aplicación entera con un atributo que indique que no quiero que se almacenen datos en la cache.</span>

Este filtro lo podemos implementar de la siguiente forma

{% highlight javascript %}
public class NoCacheAttribute : ActionFilterAttribute
{
    public override void OnResultExecuting(ResultExecutingContext filterContext)
    {
        filterContext.HttpContext.Response.Cache.SetExpires(DateTime.UtcNow.AddDays(-1));
        filterContext.HttpContext.Response.Cache.SetValidUntilExpires(false);
        filterContext.HttpContext.Response.Cache.SetRevalidation(HttpCacheRevalidation.AllCaches);
        filterContext.HttpContext.Response.Cache.SetCacheability(HttpCacheability.NoCache);
        filterContext.HttpContext.Response.Cache.SetNoStore();
 
        base.OnResultExecuting(filterContext);
    }
}
{% endhighlight %}

Y con esto ya estaría el “problema” resuelto

<span style="font-size: 1em; line-height: 1.6em;">Hasta pronto!!</span>



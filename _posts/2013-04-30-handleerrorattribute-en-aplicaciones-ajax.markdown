---
layout: post
title: HandleErrorAttribute en aplicaciones ajax
date: '2013-04-30 03:13:38'
tags:
- ajax
- asp-mvc-filters
---


Vamos a ver como variar el comportamiento del filtro HandleErrorAttribute para que sea usable en aplicaciones basadas en interacciones AJAX.

Cuando queremos que las interacciones y navegación de nuestra aplicación sea principalmente AJAX, rápidamente nos vamos a encontrar con el problema de que al producirse una excepción en el servidor, se retorna la vista de error por defecto pero no se hace nada con ella. Con que se retorne la vista de error por defecto quiero decir que podemos consultar con Firebug o similar que efectivamente se está pasando el html de la vista de error, pero al ser una interacción ajax, no se gestiona qué hacer con esta respuesta.

El problema por tanto es que nuestro cliente no hace nada con la respuesta y por tanto el usuario no percibe que se haya producido ningún error. Podríamos usar esta respuesta para inyectar el html resultante, o parte, en el DOM, pero tal vez lo ideal sería retornar JSON con el mensaje de error y el código HTTP correspondiente.

Una forma de hacer frente a este problema es extender el filtro de error que nos ofrece el framework <span>por defecto.</span>

La mejor forma que he encontrado para hacerlo es [ir al código fuente de ASP MVC y buscar el archivo HandleErrorAttribute.cs](http://aspnetwebstack.codeplex.com/SourceControl/latest#src/System.Web.Mvc/HandleErrorAttribute.cs "HandleError"). A partir de este código, trato de derivarlo de la siguiente forma en un nuevo *CustomHandleErrorAttributte* realizando una pequeña modificación:

{% highlight javascript %}
public class CustomHandleErrorAttribute : HandleErrorAttribute
{
    public override void OnException(ExceptionContext filterContext)
    {	
        if (filterContext == null)
        {
            throw new ArgumentNullException("filterContext");
        }
		
        if (filterContext.IsChildAction)
        {
            return;
        }

        if (filterContext.ExceptionHandled || !filterContext.HttpContext.IsCustomErrorEnabled)
        {
            return;
        }

        Exception exception = filterContext.Exception;

        if (new HttpException(null, exception).GetHttpCode() != 500)
        {
            return;
        }

        if (!ExceptionType.IsInstanceOfType(exception))
        {
            return;
        }

        // Si la petición es AJAX => JSON. Si no lo es => Vista por defecto.
        if (filterContext.HttpContext.Request.Headers["X-Requested-With"] == "XMLHttpRequest")
        {
            filterContext.Result = new JsonResult 
            { 
                JsonRequestBehavior = JsonRequestBehavior.AllowGet, 
                Data = new 
                { 
                    error = true,
                    message = filterContext.Exception.Message
                } 
            };
        }
        else
		{
            string controllerName = (string)filterContext.RouteData.Values["controller"];
            string actionName = (string)filterContext.RouteData.Values["action"];
            HandleErrorInfo model = new HandleErrorInfo(filterContext.Exception, controllerName, actionName);
            filterContext.Result = new ViewResult
            {
                ViewName = View,
                MasterName = Master,
                ViewData = new ViewDataDictionary<HandleErrorInfo>(model),
                TempData = filterContext.Controller.TempData
            };
		}
        
		filterContext.ExceptionHandled = true;
        filterContext.HttpContext.Response.Clear();
		filterContext.HttpContext.Response.StatusCode = 500;
		
        filterContext.HttpContext.Response.TrySkipIisCustomErrors = true;		
    }
}
{% endhighlight %}

<span style="font-size: 1em; line-height: 1.6em;">El punto clave que se ha cambiado respecto al original es el momento en el que se retorna la vista de error. en lugar de lo que hace el filtro por defecto, se comprobará si la petición realizada es una petición AJAX y en caso afirmativo retornar un **JsonResult** en lugar de la vista de error.</span>

{% highlight javascript %}
// Si la petición es AJAX => JSON. Si no lo es => Vista por defecto
if (filterContext.HttpContext.Request.Headers["X-Requested-With"] == "XMLHttpRequest")
{
    filterContext.Result = new JsonResult 
    { 
        JsonRequestBehavior = JsonRequestBehavior.AllowGet, 
        Data = new { 
                    error = true,
                    message = filterContext.Exception.Message
                } 
    };
}
{% endhighlight %}

Como se puede ver, lo que estamos haciendo es pasar el mensaje de excepción en el resultado JSON y un flag *error = true* que podemos gestionar desde el cliente con un poco de javascript y así mostrar el error de forma correcta.

Es también importante reseñar que el filtro por defecto sólo gestiona errores 500 …

{% highlight javascript %}
// Si no es HTTP 500 (por ejemplo, si alguien lanza un  HTTP 404 desde una acción), ignórarlo
if (new HttpException(null, exception).GetHttpCode() != 500)
{
      return;
}
{% endhighlight %}

… por lo que si nos ponemos a disparar excepciones HTTP … etc, esto no va a funcionar. Sí que funcionará si explícitamente añado los códigos que quiero gestionar a mayores del 500.

También comentar que aún habiendo customizado los códigos a gestionar, si trato de hacerlo con el código HTTP 401 (Unathorized), ASP MVC se lo va a saltar a la torera y retornará un HTTP 200 ya que es el comportamiento por defecto si tengo **FormsAuthentication** habilitada. Una forma de evitar esto es usar HTTP 403 (Forbidden) en lugar del 401.

Hasta pronto!!



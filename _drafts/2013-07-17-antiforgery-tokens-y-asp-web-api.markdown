---
layout: post
title: AntiForgery tokens y ASP web API
date: '2013-07-17 05:19:44'
tags:
- web-api
---


En ASP MVC la gestión del mecanismo de protección contra ataque CSRF es muy sencillo de utilizar. En la Web API no tanto.

En las aplicaciones ASP MVC, el mecanismo de protección contra ataques CSRF (Cross Site Request Forgery) es muy fácil de utilizar. Únicamente hemos de incluir en los formularios un HTML helper que genera el token …

```c
@Html.AntiForgeryToken()
```

<span style="font-size: 1em; line-height: 1.6em;">… y a continuación decorar el método que gestiona el POST del formulario con:</span>

```c
[ValidateAntiForgeryToken] public ActionResult MiAccion() { }
```

En la web api la cosa no es tan sencilla. Aquí no hay HTML helpers ni atributos predefinidos. Además el uso que se le dará al api REST será principalmente AJAX. Por tanto no nos queda otra que definir un filtro que valide la inclusión del token y además enviar dicho token desde el cliente Javascript. Una forma de implementar el filtro sería esta:</span>

```c
[AttributeUsage(AttributeTargets.Method | AttributeTargets.Class, AllowMultiple = false, Inherited = true)]
public class AntiForgeryTokenAttribute : System.Web.Http.Filters.ActionFilterAttribute
{

    public override void OnActionExecuting(System.Web.Http.Controllers.HttpActionContext actionContext)
    {
		//Comprobamos que el token se envía en la petición
        if (!actionContext.Request.Headers.Any(z => z.Key.Equals(AntiForgeryConfig.CookieName, StringComparison.OrdinalIgnoreCase)))
        {
            actionContext.Response =
                actionContext.Request.CreateResponse(HttpStatusCode.ExpectationFailed);
            actionContext.Response.ReasonPhrase = "Missing token";
            return;
        }

		//Extraemos el token
        var headerToken = actionContext
            .Request
            .Headers
            .Where(z => z.Key.Equals(AntiForgeryConfig.CookieName, StringComparison.OrdinalIgnoreCase))
            .Select(z => z.Value)
            .SelectMany(z => z)
            .FirstOrDefault();

		// Extraemos la cookie asociada
        var cookieToken = actionContext
            .Request
            .Headers
            .GetCookies()
            .Select(c => c[AntiForgeryConfig.CookieName])
            .FirstOrDefault();

        // Comprobamos si existen tanto la cookie como el token
        if (cookieToken == null || headerToken == null)
        {
            actionContext.Response =
                actionContext.Request.CreateResponse(HttpStatusCode.ExpectationFailed);
            actionContext.Response.ReasonPhrase = "Missing token null";
            return;
        }

        // Validamos
        try
        {
            AntiForgery.Validate(cookieToken.Value, headerToken);
        }
        catch
        {
            actionContext.Response =
                actionContext.Request.CreateResponse(HttpStatusCode.ExpectationFailed);
            actionContext.Response.ReasonPhrase = "Invalid token";
            return;
        }

        base.OnActionExecuting(actionContext);
    }
}
```

Listo, ahora obviamente hemos de enviar el token desde nuestro cliente Javascript. Aquí podríamos seguir diversas estrategias dependiendo de nuestro tipo de cliente. Por ejemplo, si estamos desarrollando una SPA cuya página principal es una vista MVC, podemos incluir el token mediante el helper @Html.AntiForgeryToken() y posteriormente incluirlo en todas las peticiones que lo necesiten accediento al valor del tag `input` generado por el helper.

Hay un problema con esta estrategia y es que la generación del token tiene en cuenta el usuario autenticado para mayor seguridad, por lo que me he encontrado con un problema cuando hago logout y otra vez login con un nuevo usuario. El token deja de ser válido a no ser que refresque la ventana entera del navegador y se obtenga uno nuevo para el nuevo usuario. Si no tengo implementado ningún mecanismo de autenticación pues nada, esta estrategia es perfectamente válida.

La otra estrategia sería solicitar el token directamente mediante una llamada ajax e incluirlo en las peticiones que se necesite. Esta operación la repetiremos en cada cambio de usuario que se produzca. Por ejemplo, creamos un función que obtenga el token y lo incluya en cada petición.

```c
function addAntiForgeryTokenToAjaxRequests() {
	$.get("api/getantiforgerytokens").then(function (token) {				
		$(document).ajaxSend(function (event, request, options) {
			if (options.hasContent) {	                
				request.setRequestHeader("__RequestVerificationToken", token);
			}
		});
	});
}
```

Esta función la llamaremos con cada cambio de estado de autenticación para recuperar el nuevo token asociado al nuevo usuario autenticado.

El método de la Web API encargado de obtener el token sería:

```c
[HttpGet]
[AllowAnonymous]
public string GetAntiForgeryTokens()
{
    string cookieToken = "", formToken = "";
    AntiForgery.GetTokens(null, out cookieToken, out formToken);
    HttpContext.Current.Response.Cookies[AntiForgeryConfig.CookieName].Value = cookieToken;
    return formToken;
}
```
Otro detalle importante, es que en cada cambio de usuario, debido a que el mecanismo de autenticación puede tardar más en establecer el nuevo **Principal** de lo que tardamos nosotros en hacer el cambio de usuario, hace necesario que aseguremos que el cambio en el estado de autenticación se ha producido antes de obtener el nuevo token.

```c
// Login
IPrincipal principal = new GenericPrincipal(new GenericIdentity("username"), null);
Thread.CurrentPrincipal = principal;
HttpContext.Current.User = principal;  

// Logout
Thread.CurrentPrincipal = null;
HttpContext.Current.User = null;
```
Bueno, pues hasta aquí este post … Ahora a ver si funciona

Hasta pronto!!



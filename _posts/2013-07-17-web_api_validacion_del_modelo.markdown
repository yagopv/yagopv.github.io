---
layout: post
title: Web API. Validación del modelo
date: '2013-07-17 03:17:05'
tags:
- web-api
---


Vamos a ver un filtro que nos permitirá validar el modelo del mismo modo que lo solemos hacer en ASP MVC.

En ASP MVC la validación de nuestros modelos viene facilitada en gran medida por la gestión que podemos hacer en los controladores del ***ModelState***

```language-javascript
[HttpPost]
public ActionResult SaveModel(MyModel model)
{
    if(ModelState.IsValid)
    {
        // Guardar en la BBDD
        return View("ModelSaved");
    }
    return View();
}
```

<span style="font-size: 1em; line-height: 1.6em;">Si el ModelState es inválido se retornará la misma vista y se mostrarán los errores de validación que estemos gestionando en la misma mediante los HTML Helpers correspondientes.</span>

<span style="font-size: 1em; line-height: 1.6em;">En la Web API la cosa no es tan sencilla y tendremos que trabajar un poco más. Lo normal es desarrollar un filtro con el que decorar nuestros métodos y retornar los errores de validación que se hayan producido. El filtro es muy sencillo y nos valdría simplemente con algo así:</span>

```language-javascript
public class ModelValidationAttribute : ActionFilterAttribute
{
    public override void OnActionExecuting(HttpActionContext actionContext)
    {
        if (actionContext.ModelState.IsValid == false)
        {
            // Devuelve los errores de validación
            var errors = new Dictionary<string, IEnumerable<string>>();
            foreach (KeyValuePair<string, ModelState> keyValue in actionContext.ModelState)
            {
                errors[keyValue.Key] = keyValue.Value.Errors.Select(e => e.ErrorMessage);
            }
 
            actionContext.Response =
                actionContext.Request.CreateResponse(HttpStatusCode.BadRequest, errors);
        }
    }
}
```

<span style="font-size: 1em; line-height: 1.6em;">Ahora tendremos que decorar nuestros métodos o incluso el contralador de la web api al completo con este filtro para que se aplique de forma correcta.</span>

<span style="font-size: 1em; line-height: 1.6em;">Por tanto, cuando hagamos una petición ajax desde nuestro cliente sobre la web api, ya podremos gestionar dichos errores de validación mediante, <span>por ejemplo, </span>algo así …</span><span style="font-size: 1em; line-height: 1.6em;"></span>

```language-javascript
$.post("api/", credential)
	.done(function (result) {
		// Todo ha ido bien
	})
	.fail(function (errors) {
		// Se han producido errores de validación y se ha retornado un 400. Bad request con los errores
             if (errors.responseText != "") {
                 $.each($.parseJSON(errors.responseText), function (key, value) {
                    console.log(value);
                 });
             } else {
                   console.log(errors.statusText);
             }			
	});
```

<span style="font-size: 1em; line-height: 1.6em;">Listo, con esto ya tenemos la misma funcionalidad que en ASP MVC</span>

<span style="font-size: 1em; line-height: 1.6em;">Hasta pronto!!</span>



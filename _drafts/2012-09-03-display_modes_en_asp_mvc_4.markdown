---
layout: post
title: Display Modes en ASP MVC 4
date: '2012-09-03 07:20:16'
---


Una de las características más interesantes que llega con ASP MVC 4 es la facilidad que ofrece para desarrollar aplicaciones móviles en paralelo a las de escritorio.

Vamos a ver cómo se puede conseguir mediante el uso de los Display Modes.

En ASP MVC 4 puedo crear vistas distintas para diferentes dispositivos. Es decir, puedo crear vistas por ejemplo para tablets y otras para móviles. O puedo incluso trabajar con mayor granularidad y crear vistas para teléfonos Android, otras para iPhone / iPod, … etc.

Cada una de las vistas, la diferenciaremos de las otras mediante la inclusión de una palabra o Display Mode en el nombre. Este Display Mode hemos de configurarlo y además indicar cómo reconocer el tipo de dispositivos que se tiene como objetivo.

### Reconociendo dispositivos

Supongamos que en nuestra aplicación queremos crear vistas personalizables para los tres tipos de dispositivos comunes, escritorio, tablets y móviles. Hemos de crear dos Display Modes nuevos a los que llamaremos **Tablet** y **Movil** (El escritorio cogerá las vistas por defecto). Para ello debemos modificar ***DisplayModeProvider*** y añadir los dos nuevos modos de visualización, además de indicar cómo reconocerlos una vez se recibe una petición.

Vamos a usar el ***UserAgent***para realizar esta distinción. En el método *Application_Start()* añadimos el siguiente código

```javascript
using System.Web.WebPages;

protected void Application_Start() { 

	AreaRegistration.RegisterAllAreas(); 
	FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters); 
	RouteConfig.RegisterRoutes(RouteTable.Routes); 
	BundleConfig.RegisterBundles(BundleTable.Bundles); 
	DisplayModeProvider.Instance.Modes.Insert(0, 
		new DefaultDisplayMode("Phone") 
		{
			ContextCondition = (context => ( 
			(context.GetOverriddenUserAgent() != null) &amp;&amp; 
			( 
				(context.GetOverriddenUserAgent().IndexOf("iPhone", 
					StringComparison.OrdinalIgnoreCase) >= 0) || 
				(context.GetOverriddenUserAgent().IndexOf("iPod", 
					StringComparison.OrdinalIgnoreCase) >= 0) || 
				(context.GetOverriddenUserAgent().IndexOf("Droid", 
					StringComparison.OrdinalIgnoreCase) >= 0) || 
				(context.GetOverriddenUserAgent().IndexOf("Blackberry",
					StringComparison.OrdinalIgnoreCase) >= 0) || 
				(context.GetOverriddenUserAgent() .StartsWith("Blackberry", 
					StringComparison.OrdinalIgnoreCase)) 
				) 
			)) 
		}); 
		
	DisplayModeProvider.Instance.Modes.Insert(0, 
		new DefaultDisplayMode("Tablet") 
		{ 
			ContextCondition = (context => ( 
			(context.GetOverriddenUserAgent() != null) &amp;&amp; 
			( 
				(context.GetOverriddenUserAgent().IndexOf("iPad", 
					StringComparison.OrdinalIgnoreCase) >= 0) || 
				(context.GetOverriddenUserAgent().IndexOf("Playbook", 
					StringComparison.OrdinalIgnoreCase) >= 0) || 
				(context.GetOverriddenUserAgent() .IndexOf("Transformer", 
					StringComparison.OrdinalIgnoreCase) >= 0) || 
				(context.GetOverriddenUserAgent().IndexOf("Xoom", 
					StringComparison.OrdinalIgnoreCase) >= 0) 
				) 
			)) 
		}); 
}
```

Hemos añadido dos nuevas entradas en ***DisplayModeProvider*** que se corresponden con los modos de visualización para teléfonos móviles y tablets. Simplemente con esto estamos indicando cómo se debe formatear una respuesta ante una petición. Si alguna de las condiciones evaluadas se cumple, se inserta el nombre del Display Mode en la vista a retornar.

El código anterior usa el UserAgent para evaluar de qué dispositivo viene la petición. Con este código de ejemplo se cubre la mayor parte de los dispositivos del mercado aunque si se quiere una precisión mayor se debería recurrir a librerías especializadas como [51Degrees](http://51degrees.mobi/ "51Degrees").

Cuando llega una petición del tipo …

```
/Aplicacion/Controlador/
```

… si el dispositivo de entrada es un PC se retorna la vista …

```
/Views/Controlador/Index.cshtml
```

… si el dispositivo es un móvil …

```
/Views/Controlador/Index.Phone.cshtml
```

… y si fuera un tablet …

```
/Views/Controlador/Index.Tablet.cshtml
```

De esta forma podemos crear vistas únicas para cada dispositivo de manera muy sencilla. Por supuesto podemos incluir en nivel de detalle que queramos al modificar el ***DisplayModeProvider*** pudiendo crear la variedad de Display Modes que necesitemos.

Hasta pronto !!


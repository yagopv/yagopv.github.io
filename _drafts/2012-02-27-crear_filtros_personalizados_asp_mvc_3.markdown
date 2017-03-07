---
layout: post
title: Creación de filtros personalizados en ASP MVC 3
date: '2012-02-27 13:34:18'
tags:
- asp-mvc-filters
---


Los filtros en ASP MVC permiten ejecutar código antes, después o bien antes y después de que se invoque a una acción en uno de nuestros controladores.

El Framework proporciona una serie de filtros por defecto, pero como todo en ASP MVC, se permite fácilmente extender una serie de componentes para fabricar nuestros propios filtros.

Los filtros en ASP MVC 3, son atributos que se aplican sobre los controladores o sobre las acciones de estos y que se encargan de ejecutar código antes y/o después de la ejecución de la acción del controlador.

Existen un buen número de filtros que el Framework nos proporciona por defecto, pero además, tenemos disponible la capacidad de crear nuestros propios filtros extendiendo una interfaz que se corresponde con uno de los 4 tipos de filtros que el Framework permite implementar. Estos cuatro tipos son los siguientes

<div class="x-content-band border-top border-left border-right border-bottom" id="x-content-band-3" style="background-color: #f1f0ff;"><div class="x-container max width">***Filtros de autorización**. Son ejecutados antes de las acciones. Implementan la interfaz[**IAuthorizationFilter**](http://msdn.microsoft.com/es-es/library/system.web.mvc.iauthorizationfilter%28v=vs.98%29.aspx "Define los métodos necesarios para un filtro de autorización")*

***Filtros de acción**: Se ejecutan antes y después de invocar una acción. Implementan la interfaz [**IActionFilter**](http://msdn.microsoft.com/es-es/library/system.web.mvc.iactionfilter.onactionexecuting%28v=vs.98%29.aspx "Se llama antes de que se ejecute un método de acción.")*

***Filtros de resultados:** Se ejecutan antes y después de ejecutar un ActionResult que devuelve algo desde una acción. Implementan[**IResultFilter**](http://msdn.microsoft.com/es-es/library/system.web.mvc.iactionfilter.onactionexecuting%28v=vs.98%29.aspx "Se llama antes de que se ejecute un método de acción")*

***Filtros de error**. Se ejecutan cuando se produce una excepción al ejecutar una acción, en el retorno de un resultado o bien en alguno de los filtros de dicha acción. Implementan la interfaz [**IExceptionFilter**](http://msdn.microsoft.com/es-es/library/system.web.mvc.iexceptionfilter.onexception%28v=vs.98%29.aspx "Filtro llamado cuando se produce una excepción")*

</div></div>Para crear un filtro personalizado podemos hacer dos cosas:

1) Heredar de la clase abstracta [*System.Web.Mvc.FilterAttribute*](http://msdn.microsoft.com/es-es/library/system.web.mvc.filterattribute%28v=vs.98%29.aspx "Representa la clase base de los atributos de acción y filtro de resultados") e implementar uno o varios de los interfaces anteriores dependiendo de en qué momento queremos que nuestro código se ejecute y tomar el control de la acción.

2) Heredar de alguno de los filtros que ya existen en el Framework y sobreescribir sus métodos

Como ejemplo de lo comentado hasta ahora vamos a ver cómo implentariamos un útil filtro que lo que hace es comprimir los resultados que devuelven las acciones mediante **gzip** o **deflate**. Es un filtro que se puede usar en la mayoría de acciones y que disminuye el tamaño de la respuesta considerablemente.

 public class EnableCompressionAttribute : ActionFilterAttribute { const CompressionMode compress = CompressionMode.Compress; public override void OnActionExecuting(ActionExecutingContext filterContext) { HttpRequestBase request = filterContext.HttpContext.Request; HttpResponseBase response = filterContext.HttpContext.Response; string acceptEncoding = request.Headers["Accept-Encoding"]; if (acceptEncoding == null) return; if (acceptEncoding.ToLower().Contains("gzip")) { response.Filter = new GZipStream(response.Filter, compress); response.AppendHeader("Content-Encoding", "gzip"); } else if (acceptEncoding.ToLower().Contains("deflate")) { response.Filter = new DeflateStream(response.Filter, compress); response.AppendHeader("Content-Encoding", "deflate"); } } }

Este filtro es un ejemplo de herencia de uno de los filtros ya existentes que lo que hace es comprimir la respuesta con gzip o deflate si es que se acepta esta codificación por parte del navegador cliente.

Hasta aquí este resumen de las posibilidades que nos ofrece ASP MVC 3 en cuanto a la definición de filtros para nuestras accciones.

Hasta luego!!



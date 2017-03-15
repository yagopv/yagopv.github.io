---
layout: post
title: Enviando datos comprimidos al cliente desde una aplicación ASP MVC
date: '2012-03-22 04:49:53'
tags:
- asp mvc
categories:
- .NET
---

En este post vamos a crear un filtro personalizado que permite comprimir la respuesta (Response) que se envía desde el servidor al cliente.

En **ASP MVC** existe la posibilidad de ejecutar código antes y después del procesamiento de las acciones de los controladores.

A este código ejecutable, se conoce como Filtro y se suelen utilizar para efectuar tareas comunes y repetitivas como pueden ser las de validación de datos o generación de logs.

Hace unos meses, cuando estaba entretenido montando este blog, me encontré con el problema de que en algunos casos estaba enviando en mis **Response** tamaños demasiado grandes para el tipo de servicio que tenía contratado, un hosting compartido en USA (barato, pero muy lento), así que en lugar de contratar un mejor servicio, decidí intentar enviar tamaños de **Response** menores para ver si los tiempos de carga de página se volvían aceptables.

Sin duda, lo que mejor funcionó, aparte de la minificación, combinación …etc. de scripts, fue la implementación de un filtro de acción que devuelve los contenidos comprimidos con **gzip** o **deflate.**

La mayoría de navegadores actuales aceptan este tipo de codificación en las respuestas desde el servidor y realmente el tamaño se reduce considerablemente y la carga de página se efectúa de manera mucho más rápida y efectiva.

Vamos a ver la implementación que utilicé

 public class EnableCompressionAttribute : ActionFilterAttribute { const CompressionMode compress = CompressionMode.Compress; public override void OnActionExecuting(ActionExecutingContext filterContext) { HttpRequestBase request = filterContext.HttpContext.Request; HttpResponseBase response = filterContext.HttpContext.Response; string acceptEncoding = request.Headers["Accept-Encoding"]; if (acceptEncoding == null) return; if (acceptEncoding.ToLower().Contains("gzip")) { response.Filter = new GZipStream(response.Filter, compress); response.AppendHeader("Content-Encoding", "gzip"); } else if (acceptEncoding.ToLower().Contains("deflate")) { response.Filter = new DeflateStream(response.Filter, compress); response.AppendHeader("Content-Encoding", "deflate"); } } }

Esta sencilla implementación, genera un filtro de acción ***EnableCompression*** que hereda de ***ActionFilterAttributte***.

Básicamente, lo que hace es lo siguiente:

1) Sobreescribe ***OnActionExecuting()*** .Por tanto, el filtro se procesa antes que la acción del controlador

2) Comprueba la cabecera ***“Accept-Encoding”*** que envía el navegador cliente

string acceptEncoding = request.Headers["Accept-Encoding"];

3) Si la cabecera es null no hace nada

4) En caso contrario, comprueba en primer lugar si acepta el tipo de compresión ***“gzip”*** para en ese caso comprimir mediante ***GZipStream()*** la respuesta

 if (acceptEncoding.ToLower().Contains("gzip")) { response.Filter = new GZipStream(response.Filter, compress); response.AppendHeader("Content-Encoding", "gzip"); }

5) Si no acepta ***“gzip”*** lo intenta con ***“deflate”*** , y en caso de aceptar este tipo comprime la respuesta mediante ***DeflateStream()***

 else if (acceptEncoding.ToLower().Contains("deflate")) { response.Filter = new DeflateStream(response.Filter, compress); response.AppendHeader("Content-Encoding", "deflate"); }

Una vez compilada la clase que contiene este fitro se usaría así en nuestros controladores

[EnableCompression] public ActionResult Index() { return View() }

Con este simple filtro de acción, los tamaños de las respuesta se redujeron considerablemente.

El tamaño se redujo de 30.6Kb a 6.1Kb. Cuando devuelve la respuesta comprimida se incluye el header *Content-Encoding* ***gzip***.

Hasta pronto!!



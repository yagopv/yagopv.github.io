---
layout: post
title: Validación remota usando el atributo [Remote] y ajax
date: '2012-02-28 15:25:55'
tags:
- asp-mvc-filters
---


En ASP MVC existe la posibilidad de realizar una validación remota de un campo que estemos actualizando en un formulario web.

Esta posibilidad es muy útil en ciertas circunstancias y permite la realización de tareas complejas de validación de un modo asíncrono.

Para efectuar validación sobre propiedades del modelo se dispone de una serie de atributos predefinidos que se alojan en la librería *[System.ComponentModel.DataAnnotations. ](http://msdn.microsoft.com/es-es/library/system.componentmodel.dataannotations.aspx "El espacio de nombres System.ComponentModel.DataAnnotations proporciona clases de atributos que se utilizan para definir los metadatos para los controles Data Dynamic y ASP.NET MVC")*

A mayores de los atributos comunes, en ASP MVC 3 se incluye el atributo **[[Remote]](http://msdn.microsoft.com/es-es/library/gg508808%28v=vs.98%29.aspx "Validación remota con el atributo [Remote]")** que proporciona la capacidad de efectuar una validación desde el cliente a través de ajax sin enviar todo el formulario web. Este atributo ejecutará una acción en el lado del servidor cuando se produzca un cambio en el dato que ha sido marcado con este atributo y que retorna si la validación ha sido superada con éxito o no.

Supongamos que tenemos una clase como la siguiente

public class Cliente { [Required] public int IdCliente {get; set;} [Remote("ValidarDNI", "Clientes")] public string DNI {get; set;) }

Al decorar con el atributo** [Remote]** la propiedad DNI, hacemos que al modificar el valor en el cliente se invoque a la acción *ValidarDNI* del controlador **Clientes** para comprobar si el DNI introducido es correcto o no.

Esta acción recibe como parámetro el valor de DNI en el momento de la invocación y devolverá tres posible valores codificados en [**Json**](http://es.wikipedia.org/wiki/JSON "Javascript Object Notation"), *true*, *false* o un texto.

En el caso de retornar *true*, querrá decir que la validación es correcta. Si retorna *false* la validación no ha sido correcta y el error mostrado debería estar codificado en el parámetro **ErrorMessage** del atributo** [Remote] **si queremos propagarlo al cliente. Por último, si se retorna un texto quiere decir que la validación no ha sido correcta y además el texto retornado se mostrará al usuario que esté introduciendo los datos en ese momento si se ha preparado el formulario para ello.

Una posible implementación de esta acción sería la siguiente

public class ClientesController : Controller { public ActionResult ValidarDNI(string dni) { if (Services.ValidarDocumento(dni)) { return Json(true, JsonRequestBehavior.AllowGet); } else { return Json(false, JsonRequestBehavior.AllowGet); } } }

Como se puede apreciar, se recibe el valor introducido, el DNI, y se llama a un método que lo que hará es validar dicho DNI (*Services.ValidarDocumento(string **dni**)*)

En caso de que el método devuelva *true*, se devuelve *true* codificado en Json, en caso contrario, se devuelve *false *codificado en Json y la validación no habría tenido éxito.

Es importante recalcar que esta acción sólo se invoca en el cliente cuando se está introduciendo el valor del campo y sin enviar el formulario. No se invoca de nuevo al realizar el envío de los datos del formulario por lo que es importante validar de nuevo estos datos en el servidor una vez se haya hecho el submit definitivo

 Hasta luego!!



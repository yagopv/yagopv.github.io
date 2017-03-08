---
layout: post
title: Cómo crear un filtro anti-spam para tu blog usando Akismet y ASP MVC
date: '2012-07-09 06:43:25'
tags:
- asp-mvc-filters
- akismet
---


Si tienes algo de experiencia escribiendo en un blog, seguro que te has encontrado con los molestos spammers que se dedican a rastrear nuestras páginas en busca de foros en los que insertar su publicidad …

Existen múltiples soluciones que intentan evitar en la medida de lo posible esta situación. Una de ellas y que además está ampliamente extendida es Akismet, una enorme base de datos en la que se almacena información que nos permite distinguir qué es spam y qué no con un porcentaje de acierto bastante aceptable.

Los filtros anti-spam son un mecanismo totalmente imprescindible en las páginas web que permiten la introducción de comentarios por parte de los usuarios, sobre todo si se permiten los comentarios de manera anónima. En este artículo vamos a ver cómo crear un filtro antispam usando el, tal vez, más extendido filtro anti spam … Akismet.

[Akismet](http://akismet.com/ "Akismet") se usa de manera generalizada en [WordPress](http://wordpress.com/ "Wordpress"), de hecho, existe un plugin para los blogs creados mediante esta plataforma que permite mantener una bandeja de entrada de mensajes que han sido marcados como spam por parte de Akismet. Este plugin, permite a los administradores de un sitio web de WordPress marcar los mensajes como válidos o bien dejarlos como spam.

Pero … ¿Qué es Akismet?. Akismet es una gran base de datos construida de manera social mediante las interacciones de los usuarios a través del API del producto. Esta base de datos almacena información de miles de blogs que se protegen usando tanto la información generada por ellos mismos como la generada por los demás.

Si usamos WordPress u otros CMS que se aprovechan de Akismet la solución nos viene dada, pero cuando creamos una solución propia … tendremos que arreglarnoslas para interactuar con la base de datos a través del API.

Si finalmente opto por este producto como filtro anti-spam, hay que tener en cuenta que no se debería montar ninguna solución personalizada sin incluir un mecanismo de interpretación de **falsos positivos** y de **spam que haya podido ser pasado por alto**.

En este artículo vamos a tratar de crear un filtro anti-spam que use [el API de Akismet](http://akismet.com/development/api/ "Akismet API") para validar si los mensajes recibidos son correctos o bien sospechosos y, como casi siempre en este blog, vamos a usar ASP MVC … :).

Si revisamos las páginas de soporte de Akismet, podemos ver que existen [múltiples librerías](http://akismet.com/development/ "Akismet librerías y plugins") que usan el API del producto y que nos evitan tener que crear una implementación propia de las llamadas. Vamos a usar la opción recomendada por Akismet para .NET y que se puede descargar [desde codeplex](http://www.codeplex.com/wikipage?ProjectName=AkismetApi "Joel .NET").

El proyecto que nos estamos descargando, contiene principalmente dos clases en el mismo archivo (*Akismet.cs)*, ***Akismet y AkismetComment***, que deberemos o bien incorporar el código a nuestra propia solución o compilar el proyecto y referenciar la *dll*

***Akismet*** ofrece a través del API principalmente cuatro operaciones que estarían reflejadas en los siguientes métodos de la librería que nos acabamos de descargar:

<p><em><strong>VerifyKey(). </strong></em>Para verificar que la <strong>clave de API</strong> que se está usando es válida y por tanto se pueden realizar llamadas<strong></strong><em><strong><br /></strong></em></p>
<p><em><strong>CommentCheck</strong></em>(). Para determinar dados una serie de parámetros si un comentario es spam o no</p>
<p><em><strong>SubmitHam()</strong></em>. Cuando tengo un falso positivo puedo hacer una llamada usando este método y convertirlo en correcto</p>
<p><em><strong>SubmitSpam(). </strong></em>Cuando algún comentario se ha pasado por alto como spam puedo usar este método para avisar a Akismet que se trata de spam y que en la siguiente ocasión lo trate bien.</p>

***AkismetComment*** representa la entidad que identifica el comentario usado por Akismet****** para almacenar en su base de datos.

Una vez incorporado este componente, lo que vamos a hacer es crear un filtro **ASP MVC** que podamos aplicar en las acciones “conflictivas”. El código de nuestro filtro será algo así:

```javascript
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.Mvc;
using System.Web.Helpers;
using Joel.Net;

namespace MyApp.Filters
{
    public class AkismetFilterAttribute : ActionFilterAttribute
    {
	// Propiedades públicas para aplicar directamente desde cada acción. Cada una se corresponderá con los campos de nuestro modelo de datos que se han de buscar en el contexto de la petición
	// AuthorField => El campo que representa al autor del comentario
        public string AuthorField { get; set; }

	// EmailField => El campo que representa al email del autor
        public string EmailField { get; set; }

	// WebsiteField => El campo que representa al sitio web del autor
        public string WebsiteField { get; set; }

	// CommentField => El campo que representa al mensaje
        public string CommentField { get; set; }

        public override void OnActionExecuting(ActionExecutingContext filterContext)
        {
            // Si no tengo registrada la clave del API entonces el filtro no se procesa
            if (String.IsNullOrEmpty(Resources.Configuracion.AkismetAPIKey))
            {
                return;
            }

            // Si el usuario está autenticado, voy a considerar que el comentario es correcto siempre
            if (User.IsAuthenticated)
            {
                return;
            }

            //Crear una nueva instancia de la API de Akismet y verificar que la clave del API es válida
            Akismet api = new Akismet(Resources.Configuracion.AkismetAPIKey, filterContext.HttpContext.Request.Url.AbsoluteUri , filterContext.HttpContext.Request.UserAgent);

            if (!api.VerifyKey())
            {
                filterContext.Controller.ViewData.ModelState.AddModelError("akismetkey", "clave de Akismet incorrecta");
                return;
            }

            //A continuación creamos una instancia de AkismetComment, y lo rellenamos con los valores obtenidos del contexto de la petición
            AkismetComment akismetComment = new AkismetComment
            {
                Blog = filterContext.HttpContext.Request.Url.Scheme + "://" + filterContext.HttpContext.Request.Url.Host,
                UserIp = filterContext.HttpContext.Request.UserHostAddress,
                UserAgent = filterContext.HttpContext.Request.UserAgent,
                CommentContent = filterContext.HttpContext.Request.Unvalidated()[this.CommentField],
                CommentType = "comment",
                CommentAuthor = filterContext.HttpContext.Request[this.AuthorField],
                CommentAuthorEmail = filterContext.HttpContext.Request[this.EmailField],
                CommentAuthorUrl = filterContext.HttpContext.Request[this.WebsiteField]
            };

            //Comprobamos por último si Akismet categoriza el comentario como spam y actuamos en consecuencia. Devuelve true si es spam.
            if (api.CommentCheck(akismetComment))
            {
                filterContext.Controller.ViewData.ModelState.AddModelError("isspam", "Akismet a categorizado el comentario como spam");
            }

            base.OnActionExecuting(filterContext);
        }
    }
}
```

Necesitamos una **clave del API** para poder trabajar con Akismet . Para obtenerla, la tenemos que [solicitar en la propia web del producto](https://akismet.com/signup/ "API key"). Hasta hace poco tiempo Akismet era totalmente gratuito pero lo han modificado. De todas maneras para páginas personales sigue siendo gratuito y para profesionales el coste no es muy elevado para lo que ofrece.

Una vez obtenida la clave, la almaceno en un archivo de recursos de manera que si está a blancos no se usa el filtro. Esto cada uno lo hará como quiera y la almacenará donde prefiera.

Si la clave está informada, se valida en primer lugar que sea correcta mediante *VerifyKey()* y a continuación se mira si el contenido es spam con *CommentCheck(akismetComment)*. En ningún caso estamos retornando aquí un error *http, Exception* … etc. sino que lo que estamos haciendo es añadir un error al ***ModelState*** y será desde la propia acción donde procesaremos el mismo y gestionaremos la información. Esto por supuesto cada uno hará lo que prefiera y en muchos casos querrán cortar directamente aquí el proceso.

En este caso, dejo que continúe el flujo de la petición porque me interesa almacenar el comentario en nuestra propia base de datos, ya sea spam o no, y así poder revisarlo posteriormente para comprobar si Akismet se ha equivocado al categorizarlo.

Cuando un comentario se marca como spam se registra en la base de datos de Akismet. Además, este registro se hace a nivel de url ya que le hemos pasado la dirección de nuestra página en la propiedad ***Blog*** de ***AkismetComment***.

Almacenar los comentarios marcados como spam en nuestra base de datos es una decisión personal, pero como comentaba al principio del post, Akismet recomienda e indica que no deberían crearse soluciones personales sin ofrecer esta funcionalidad. El filtro no es perfecto y tened por seguro que se equivocará unas cuantas veces.

Bueno, pues una vez creado el filtro ASP MVC podemos aplicarlo sobre nuestras acciones de la siguiente forma

[AkismetFilter(AuthorField = "Username", EmailField = "Email", WebsiteField = "Web", CommentField = "Message")]
public ActionResult AddComment(Comment comment)

Según hemos indicado en la anotación de nuestro atributo personalizado, los campos que habrá que rastrear en el contexto de la petición serán ***Username, Email, Web y Message*** para efectuar el enlace en el filtro con el objeto ***AkismetComment***.

En el interior de la acción haré la gestión necesaria para tratar los comentarios que nuestro ***AkismetFilterAttribute*** haya categorizado como spam

```javascript
if (ModelState["isspam"] != null)
{
    // Lo que sea que quiero hacer con los comentarios que sean spam ... 
    // Por ejemplo, marcar en mi tabla de comentarios el comentario como spam mediante una columna de tipo bool IsSpam
}
```

Hasta aquí la implementación del filtro de Akismet. Ahora tendríamos que gestionar lo que hacer con los comentarios marcados como válidos y que no lo son o bien como spam y que sean válidos (falsos positivos), porque haber seguro que los habrá. Aquí ya cada uno lo hará como crea conveniente, el punto común será que se han de llamar a los métodos ***SubmitSpam()*** y ***SubmitHam()*** de la librería que nos hemos descargado para informar a Akismet y que actualice en su base de datos esta información.

Por ejemplo, si hemos decidido almacenar el comentario en nuestra base de datos junto al resto y diferenciándo los correctos de los incorrectos mediante una columna *IsSpam*, podríamos crear una acción como la siguiente para modificar la marca de spam de nuestra entidad Comentario y que de esta manera se muestre o no en el foro

```javascript
[Authorize]
public JsonResult ChangeSpamMark(int id)
{
    // Recuperar el comentario de la base de datos mediante un método de la capa de servicios de nuestra aplicacion
    Comment comment = CommentServices.FindComment(id);

    //Verificar clave del API
    Akismet api = new Akismet(Resources.Configuracion.Akismet_API_key, Request.Url.AbsoluteUri, HttpContext.Request.UserAgent);

    if (!api.VerifyKey())
    {
        return Json(new { result = "error", text = "Clave Akismet incorrecta" });
    }

    //Crear una instancia de AkismetComment 
    AkismetComment akismetComment = new AkismetComment
    {
        Blog = Request.Url.Scheme + "://" + Request.Url.Host,
        UserIp = comment.Ip,
        UserAgent = comment.UserAgent,
        CommentContent = comment.Message,
        CommentType = "comment",
        CommentAuthor = comment.Username,
        CommentAuthorEmail = comment.Email,
        CommentAuthorUrl = comment.Web
    };
           
    if (comment.IsSpam)
    {
        // Si es spam, lo convertimos en válido avisando a Akismet para que no marque más este tipo de comentarios como spam y además marcamos nuestro campo como que no es spam
        comment.IsSpam = false;
        api.SubmitHam(akismetComment);
    }
    else
    {
        // Hacemos lo contratrio que en el caso anterior
        comment.IsSpam = true;
        api.SubmitSpam(akismetComment);
    }

        //Guardamos el comentario con IsSpam acorde con nuestras pretensiones para que se muestre o no en los foros
        BlogServices.SaveComment(comment);

        return Json(new { result = "ok" });
    }
}
```

Podríamos llamar a esta acción cada vez que pulsemos por ejemplo un botón asociado a cada comentario desde el panel de control de las páginas de administración en nuestro sitio web.

Con esto, ya tendríamos una guía básica para crear un sistema anti-spam customizado en nuestro blog personal …

Hasta pronto!!


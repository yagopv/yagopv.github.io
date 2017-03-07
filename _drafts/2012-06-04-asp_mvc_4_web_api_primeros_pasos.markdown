---
layout: post
title: ASP MVC 4 Web API. Primeros pasos
date: '2012-06-04 10:58:49'
tags:
- rest
---


En este post vamos a ver cómo podemos crear una sencilla Web API con las clásicas operaciones de búsqueda, creación, modificación y baja de recursos (CRUD).

En un [post anterior](../../../Post/GetPostByCode/asp_mvc_4_web_api_que_es "ASP MVC 4 Web API. ¿Qué es?") vimos qué era y para qué sirve la nueva **Web API** que llega con el lanzamiento de **ASP MVC 4**. En este nuevo post vamos a crear una sencilla API que nos permita gestionar las típicas operaciones de recuperación, alta, baja y modificación de datos (CRUD).

La API que vamos a desarrollar ofrecerá un controlador específico para simular la gestión de mensajes en un sistema de comentarios simulado.


## Comenzamos

Vamos a abrir Visual Studio, yo tengo instalada la versión 2012 RC que ya contiene **ASP MVC 4** instalado por defecto. También es posible por supuesto seguir el ejemplo con **Visual Studio 2010** pero previamente habrá que descargar e instalar [ASP MVC 4](http://www.asp.net/web-api "Web API") de la página web del producto.

Vamos a crear un nuevo proyecto **ASP MVC 4** y vamos a llamarle *TestApi*.

[![ef5595f7-e6e6-447a-b36c-f41974729c84_Nuevo Proyecto](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/ef5595f7-e6e6-447a-b36c-f41974729c84_Nuevo-Proyecto-300x204.jpg)](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/ef5595f7-e6e6-447a-b36c-f41974729c84_Nuevo-Proyecto.jpg)

Entre las opciones de tipos de proyecto que se nos ofrecen al seleccionar **ASP MVC 4** vamos a escoger **Web API.**

[![e7ea5adb-c7fe-46c5-80f9-f3c678243f4b_Web api](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/e7ea5adb-c7fe-46c5-80f9-f3c678243f4b_Web-api-300x271.jpg)](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/e7ea5adb-c7fe-46c5-80f9-f3c678243f4b_Web-api.jpg)



La estructura de proyecto que se genera es similar a la de cualquier proyecto **ASP MVC**...

[![e4e41549-15e5-4a0d-8c62-a70d0ab1b885_Files](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/e4e41549-15e5-4a0d-8c62-a70d0ab1b885_Files-180x300.jpg)](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/e4e41549-15e5-4a0d-8c62-a70d0ab1b885_Files.jpg)

… con la salvedad de que se habrá añadido un *ValuesController* que será un controlador que hereda de *ApiController* y que por tanto puede actuar como interfaz *REST*.

También es interesante comentar que en esta última revisión del proyecto **ASP MVC 4** de Visual Studio han movido la configuración de las rutas desde el *global.asax* a la clase *RouteConfig.cs* en la carpeta *App_Start* y por tanto es aquí donde encontraremos la definición de la ruta por defecto de nuestra api.

```language-javascript
routes.MapHttpRoute(
    name: "DefaultApi",
    routeTemplate: "api/{controller}/{id}",
    defaults: new { id = RouteParameter.Optional }
);
```

Según las [notas](http://www.asp.net/whitepapers/mvc4-release-notes "ASP MVC 4 documentation") de los cambios desde **ASP MVC 4 Beta** a **ASP MVC 4 RC** este “movimiento” de aspectos de configuración de la aplicación se ha hecho en varios puntos.

```
The configuration logic For MVC applications has been moved from Global.asax.cs to a set of static classes in the App_Start directory. Routes are registered in RouteConfig.cs. Global MVC filters are registered in FilterConfig.cs. Bundling and minification configuration now lives in BundleConfig.cs.
```

## Creamos el modelo

En primer lugar vamos a crear nuestro modelo de comentarios, por lo que agregaremos una nueva clase *Comment* que representará a dicha Entidad en el interior de la carpeta *Models*

```language-javascript
    public class Comment
    {
        public int CommentId { get; set; }
        public string Title { get; set; }
        public string Message { get; set; }
        public string Username { get; set; }
    }
```


## Añadimos un Repositorio

A continuación vamos a crear un Repositorio que gestione los datos en memoria para poder probar los métodos de nuestra API sin base de datos. Lo añadiremos creando también una nueva clase en la carpeta *Models* que se llame *CommentRepository*

```language-javascript
    public class CommentRepository
    {
        List Comments = new List()   {
                  new Comment { CommentId=1, Title = "Comment 1", Message = "Message Comment 1", Username = "user1" },
                  new Comment { CommentId=2, Title = "Comment 2", Message = "Message Comment 2", Username = "user1" },
                  new Comment { CommentId=3, Title = "Comment 3", Message = "Message Comment 3", Username = "user2" },
                  new Comment { CommentId=4, Title = "Comment 4", Message = "Message Comment 4", Username = "user2" },
                  new Comment { CommentId=5, Title = "Comment 5", Message = "Message Comment 5", Username = "user1" }
        };

        public IEnumerable GetAllComments()
        {
            return Comments;
        }

        public Comment GetCommentByCommentId(int CommentId)
        {
            Comment Comment = Comments.Where(p =&gt; p.CommentId == CommentId).FirstOrDefault();
            return Comment ?? null;

        }

        public void AddComment(Comment Comment)
        {
            Comments.Add(Comment);
        }

        public bool DeleteComment(int CommentId)
        {
            Comment Comment = Comments.Where(p =&gt; p.CommentId == CommentId).FirstOrDefault();
            if (Comment == null)
            {
                return false;
            }
            Comments.Remove(Comment);
            return true;
        }

        public bool UpdateComment(Comment Comment)
        {
            Comment repoComment = Comments.Where(p =&gt; p.CommentId == Comment.CommentId).FirstOrDefault();
            if (repoComment == null)
            {
                return false;
            }
            repoComment.Title= Comment.Title;
            repoComment.Message = Comment.Message;
            return true;
        }
}
```

Como podemos ver, hemos añadido una serie de métodos para buscar, añadir, modificar y borrar objetos de una lista de comentarios que se almacenará en memoria.


## Creando el API

Una vez tenemos una Entidad y un Repositorio que trabaje contra ella, vamos a crear la capa de servicios que expondremos al exterior y que es en definitiva lo que queremos mostrar en este post.

Para ello eliminamos *ValuesController* de la carpeta *Controllers* y añadimos un nuevo controlador llamado *CommentApiController*.

En las opciones disponibles que nos da Visual Studio para la creación de controladores vamos a indicar que es un controlador de tipo API vacío.

[![245ffe44-defd-4ff6-a0fd-8278e78eec90_Empty API Controller](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/245ffe44-defd-4ff6-a0fd-8278e78eec90_Empty-API-Controller-300x192.jpg)](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/245ffe44-defd-4ff6-a0fd-8278e78eec90_Empty-API-Controller.jpg)

Decíamos en el [anterior post](../../../Post/GetPostByCode/asp_mvc_4_web_api_que_es "Wep API Qué es?") que los controladores de la **Web API** heredarían ahora de *ApiController* en lugar de *Controller* por lo que la definición de la clase controladora queda así

```language-javascript
public class CommentApiController : ApiController { }
```

En el interior de la clase controladora vamos a crear una instancia de mi clase Repositorio para acceder a las operaciones contra el modelo de datos. La hacemos *static* para mantener el contenido de la lista entre peticiones y poder hacer las pruebas.

```language-javascript
static CommentRepository commentrepository = new CommentRepository();
```

Lógicamente en una aplicación real probablemente querríamos trabajar con interfaces e inyectar dependencias en lugar de crear instanciar del Repositorio aquí.

Ahora hemos de añadir los métodos de acción necesarios para buscar, crear, modificar y borrar comentarios.

Empecemos por añadir los métodos de búsqueda

```language-javascript
        // GET /api/commentapi
        public IEnumerable Get()
        {
            return commentrepository.GetAllComments();
        }

        // GET /api/commentapi/5
        public Comment Get(int id)
        {
            Comment Comment = commentrepository.GetCommentByCommentId(id);
            if (Comment == null)
            {
                var response = new HttpResponseMessage(HttpStatusCode.NotFound);
                throw new HttpResponseException(response);
            }
            return Comment;
        }
```

He añadido dos métodos para gestionar las peticiones de tipo GET. A destacar que los dos métodos se llaman *Get(),* pero también podía haberlos llamado de cualquier otra forma siempre y cuando comenzasen por GetXXXX como por ejemplo *GetComment()* y *GetAllComments()* y el resultado seguiría siendo el mismo porque a lo que se está accediendo (recordemos que estamos creando un servicio web REST y no un controlador al uso) es al recurso que recupera el verbo GET y no a la acción concreta.

También podía haberlos llamado *FindComment()* y *FindAllComments()* y haber incluido en atributo *[HttpGet]* en la cabecera del método. De esta forma todo seguiría funcionando de la misma manera que en los casos anteriores.

También comentar que el primer método está retornando todos los comentarios. Sin embargo en el segundo se introduce el objeto *HttpResponseMessage* que representa al mensaje de respuesta que usaremos en muchos casos para retornar códigos HTTP y objetos. En este caso lo estamos usando para lanzar una excepción del tipo *HttpResponseException* en el caso de que no se encuentre el comentario y así enviar de vuelta al clliente un código de respuesta 404, Not Found (*HttpStatusCode.NotFound*).

Este es el patrón que vamos a seguir en el resto de los casos para añadir, modificar y borrar comentarios con la salvedad de los códigos de respuesta que iremos variando según convenga.

Ahora vamos a añadir un método que gestione las peticiones de tipo POST

```language-javascript
// POST /api/testapi
public HttpResponseMessage Post(Comment Comment)
{
    commentrepository.AddComment(Comment);
    var response = Request.CreateResponse<Comment>(HttpStatusCode.Created, Comment);
    string uri = Url.Link("DefaultApi", new { CommentId = Comment.CommentId }); 
    response.Headers.Location = new Uri(uri); 
    return response;
}
```

En este método estamos llamando a *AddComment()* de nuestro Repositorio para crear el comentario y a continuación devolvemos un código 201, *HttpStatusCode.Created,* y la ubicación del nuevo comentario según se indica que se debe hacer en la especificación [HTTP 1.1](http://www.w3.org/Protocols/rfc2616/rfc2616.html "El estándar HTTP 1.1") para las peticiones de tipo POST.

```
If a resource has been created on the origin server, the response SHOULD be 201 (Created) and contain an entity which describes the status of the request and refers to the new resource, and a Location header (see section 14.30).
```

Ahora añadamos un recurso para responder a peticines de tipo PUT (modificación de datos)

```language-javascript
        // PUT /api/testapi/
        public HttpResponseMessage Put(Comment comment)
        {
            var updated = commentrepository.UpdateComment(comment);
            if (!updated)
            {
                var response = new HttpResponseMessage(HttpStatusCode.NotFound);
                throw new HttpResponseException(response);
            }
            return new HttpResponseMessage(HttpStatusCode.NoContent);
        }
```

Como en es caso de POST, el resto de implementaciones deberían retornar códigos de respuesta que sigan la documentación del estándar *HTTP* por lo que en nuestro caso, cuando no se localiza el recurso para PUT retornamos un código de respuesta que identifique el problema y cuando lo modifica correctamente un 204 (NoContent).

```
The PUT method requests that the enclosed entity be stored under the supplied Request-URI. 

If the Request-URI refers to an already existing resource, the enclosed entity SHOULD be considered as a modified version of the one residing on the origin server. 

If the Request-URI does not point to an existing resource, and that URI is capable of being defined as a new resource by the requesting user agent, the origin server can create the resource with that URI. 

If a new resource is created, the origin server MUST inform the user agent via the 201 (Created) response. 

If an existing resource is modified, either the 200 (OK) or 204 (No Content) response codes SHOULD be sent to indicate successful completion of the request. 

If the resource could not be created or modified with the Request-URI, an appropriate error response SHOULD be given that reflects the nature of the problem. 

The recipient of the entity MUST NOT ignore any Content-* (e.g. Content-Range) headers that it does not understand or implement and MUST return a 501 (Not Implemented) response in such cases.
```

Por último, añadimos el borrado de un recurso

```language-javascript
        // DELETE /api/testapi/5
        public HttpResponseMessage Delete(int id)
        {
	    var deleted = commentrepository.DeleteComment(id);
            if (!deleted)
            {
                var response = new HttpResponseMessage(HttpStatusCode.NotFound);
                throw new HttpResponseException(response);
            }		
			return new HttpResponseMessage(HttpStatusCode.NoContent); 			            
        }
    }
```

Para DELETE seguimos el mismo patrón que en el caso de PUT

```
A successful response SHOULD be 200 (OK) if the response includes an entity describing the status, 202 (Accepted) if the action has not yet been enacted, or 204 (No Content) if the action has been enacted but the response does not include an entity.
```


## Probando nuestra API

Para probar nuestra api podemos ejecutar la aplicación y usar la consola de FireBug para introducir instrucciones ajax.

Antes de nada comentar que en un principo me era imposible realizar las pruebas para PUT y DELETE y el servidor retornaba siempre un 404 NotFound, pero buceando un poco por Internet encontré que añadiendo esto al Web.config

```
<system.webServer>
    <modules runAllManagedModulesForAllRequests="true">  
    <remove name="WebDAVModule"/> </modules> </system.webServer>
```

Se solucionaba el problema ya que el modulo WebDAV estaba bloqueando las peticiones de dichos tipos. Por lo que si te ves en esta situación ya sabes ;).

Para comenzar las pruebas, por ejemplo, a continuación se puede el resultado de la ejecución de un POST en la consola de Firebug mediante *$.post(). *La petición tiene como consecuencia un 201 Created. Tal y como tenemos definido en nuestra API.

[![7f69fe11-7e32-4225-858d-a7117a57d688_post_1_created](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/7f69fe11-7e32-4225-858d-a7117a57d688_post_1_created-300x105.jpg)](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/7f69fe11-7e32-4225-858d-a7117a57d688_post_1_created.jpg)

Al hacer *GET* de los contenidos que existen en nuestro Repositorio, se ejecuta el primer método *Get()* ya que la petición no lleva ningún parámetro y se recuperan por tanto todos los comentarios, incluido el añadido en el paso anterior como se puede comprobar a continuación.

[![fc22d4ba-9891-49e3-aad7-3dbf2c95675c_getall_2_ok](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/fc22d4ba-9891-49e3-aad7-3dbf2c95675c_getall_2_ok-300x107.jpg)](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/fc22d4ba-9891-49e3-aad7-3dbf2c95675c_getall_2_ok.jpg)

Si hago un GET del comentario 5, se devuelven los datos del comentario que solicito

[![95d4e85b-bf27-4408-adf9-dab5bb4cb57c_getbyid_3_ok](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/95d4e85b-bf27-4408-adf9-dab5bb4cb57c_getbyid_3_ok-300x106.jpg)](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/95d4e85b-bf27-4408-adf9-dab5bb4cb57c_getbyid_3_ok.jpg)

En el siguiente pantallazo se ejecuta una petición GET hacia un comentario que no existe y se controla el fallo mediante fail() para mostrar en la consola el texto “fallo”. Si comprobamos la definición en el API del método *Get(id)*, podemos ver que retornaba un 404 NotFound cuando no encontraba un recurso.

[![b2db1e77-c28d-4b9c-a5c6-05628c706e9a_getbyid_4_fail](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/b2db1e77-c28d-4b9c-a5c6-05628c706e9a_getbyid_4_fail-300x107.jpg)](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/b2db1e77-c28d-4b9c-a5c6-05628c706e9a_getbyid_4_fail.jpg)

Ahora modificamos un recurso mediante *PUT* y si la petición tiene éxito un *GET* para recuperar el recurso modificado y comprobar si la ha ido bien. Se puede ver que el *PUT* retorna 204 NoContent y el *GET* recupera el comentario con el texto modificado.

[![c2733b06-7436-40af-92ef-cd477a8ff3e4_put_5_ok](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/c2733b06-7436-40af-92ef-cd477a8ff3e4_put_5_ok-300x113.jpg)](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/c2733b06-7436-40af-92ef-cd477a8ff3e4_put_5_ok.jpg)

Por último probamos el *DELETE*. Borramos el comentario 5 y a continuación lo recuperamos para comprobar que no lo encuentra y retorna un 404 NotFound

[![5c3b3222-3fdf-4e66-9693-aa97baec27fd_delete_6_notfound](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/5c3b3222-3fdf-4e66-9693-aa97baec27fd_delete_6_notfound-300x104.jpg)](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/5c3b3222-3fdf-4e66-9693-aa97baec27fd_delete_6_notfound.jpg)

Y hasta aquí las pruebas… por supuesto si sometemos esta API de ejemplo a stress seguro que se derrumba  pero es un punto de partida.

La aplicación de ejemplo la dejo en [este enlace](https://github.com/yagopv/TestAPI "Aplicación de ejemplo de Web API") de Github para quien la quiera descargar

Hasta pronto!!

---
layout: post
title: El filtro [AllowAnonymous]
date: '2012-05-31 02:50:11'
tags:
- seguridad
- asp-mvc-filters
---


AllowAnonymous es un nuevo filtro que llega junto a la cuarta revisión de ASP MVC para facilitar la implementación de la seguridad en aplicaciones web construidas con este framework.

Como su propio nombre indica el objetivo de este filtro es permitir el **acceso anónimo** a las acciones de un controlador.

En versiones anteriores de **ASP MVC** usábamos el filtro *[Authorize]* para restringir el acceso a controladores o acciones de nuestro sitio web. *[Authorize]* se puede aplicar a nivel de controlador, a nivel de acción o incluso, desde la versión 3 de **ASP MVC**, como filtro global codificado en el *global.asax,* con el objetivo de securizar la aplicación completa.

El problema del atributo* [Authorize]* es que, en el caso de que tengamos controladores con acciones que han de protegerse y otras en las que no es necesario, tendremos que tener mucho cuidado de colocar el atributo en donde sea necesario y además si añadimos acciones hemos de acordarnos de si hemos de añadirlo o no.

En el caso de incluir el filtro como global en el *global.asax* y de esta forma hacer que la aplicación sea completamente privada el problema es que precisamente cuando arranque el sitio web tengo que estar autenticado!!!

En fin, *[Authorize]* es de gran utilidad pero con la ayuda del nuevo filtro *[AllowAnonymous]* veremos que nos facilita las cosas sobre todo en ciertos escenarios.

Supongamos, como comentábamos anteriormente, que quiero hacer que mi aplicación sea **completamente privada**, que no se pueda acceder a ningún recurso si no se está autenticado.

La manera más lógica sería incluir el filtro* [Authorize]* en el *global.asax* como filtro global y así no tener que estar recordando continuamente que tengo que añadirlo a mis acciones o a los controladores cada vez que añado funcionalidades a mi aplicación. Lo haría de esta forma

```language-javascript
RegisterGlobalFilters(GlobalFilterCollection filters) {
  filters.Add(new HandleErrorAttribute());
  filters.Add(new System.Web.Mvc.AuthorizeAttribute());
}
```
```
Comentar que hay que añadir el ensamblado por delante del nombre del filtro  porque ya existe en System.Web.Http otro AuthorizeFilter para la Web API.
```

Una vez hecho esto y securizada mi aplicación, añadiré el atributo *[AllowAnonymous]* a las acciones que lo necesiten, es decir, *Login()*, *Register()*, …etc . Con esto ya tendría lo que estaba buscando y como se puede ver no me he de preocupar de añadir el filtro cada vez que añado un nuevo controlador o acción ya que *[Authorize]* definido de forma global ya bloquea el acceso a todos los recursos a los que explícitamente no permito el acceso.

Si miramos la plantilla de aplicación de **ASP MVC 4** que viene con la nueva versión de **Visual Studio 2011**, precisamente se utiliza esta estrategia con el controlador *AccountController*.

```language-javascript
    [Authorize]
    public class AccountController : Controller
    {
        [AllowAnonymous]
        public ActionResult Login()
        {
             ...
        }

        [AllowAnonymous]
        [HttpPost]
        public JsonResult JsonLogin(LoginModel model, string returnUrl)
        {
             ...
        }

        [AllowAnonymous]
        [HttpPost]
        public ActionResult Login(LoginModel model, string returnUrl)
        {
             ...
        }

        public ActionResult LogOff()
        {
             ...
        }

        [AllowAnonymous]
        public ActionResult Register()
        {
             ...
        }

        [AllowAnonymous]
        [HttpPost]
        public ActionResult JsonRegister(RegisterModel model)
        {
             ...
        }

        [AllowAnonymous]
        [HttpPost]
        public ActionResult Register(RegisterModel model)
        {
             ...
        }

        public ActionResult ChangePassword()
        {
             ...
        }

        [HttpPost]
        public ActionResult ChangePassword(ChangePasswordModel model)
        {
             ...        
        }

        public ActionResult ChangePasswordSuccess()
        {
             ...
        }
    }
```


En este escenario en el que se securiza la aplicación al completo se ven claramente las ventajas de *[AllowAnonymous], *pero bien es cierto que se puede usar en cualquier otro con el objetivo simplemente de negar al filtro * [Authorize] *que**se estará aplicando en un nivel superior.

Hasta pronto!!

***MODIFICADO 28/06/2012***

La implementación de [AllowAnonymous] en ASP MVC 4 es curiosa … En realidad el filtro es una clase vacía

```language-javascript
namespace System.Web.Mvc
{
    /// <summary>
    /// Actions and controllers with the AllowAnonymous attribute are skipped by the Authorize attribute
    /// on authorization. See AccountController.cs in the project template for an example.
    /// </summary>
    [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, AllowMultiple = false, Inherited = true)]
    public sealed class AllowAnonymousAttribute : Attribute
    {
    }
}
```

La validación real de si se han de permitir accesos no autenticados está en el propio filtro [Authorize] en el interior del método OnAuthorization()

```language-javascript
public virtual void OnAuthorization(AuthorizationContext filterContext)
        {
            . . .

            bool skipAuthorization = filterContext.ActionDescriptor.IsDefined(typeof(AllowAnonymousAttribute), inherit: true)
                                     || filterContext.ActionDescriptor.ControllerDescriptor.IsDefined(typeof(AllowAnonymousAttribute), inherit: true);

            if (skipAuthorization)
            {
                return;
            }

            . . .
        }
```

Como se puede ver, lo que se está haciendo es mirar si el controlador o la acción tienen el filtro [AllowAnonymous] definido, y en caso afirmativo no sigue adelante …


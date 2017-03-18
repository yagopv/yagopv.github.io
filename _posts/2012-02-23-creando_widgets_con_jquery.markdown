---
layout: post
title: Creando widgets con jQuery
date: '2012-02-23 14:27:40'
tags:
- jquery
- jquery ui
categories:
- Frontend Development
---

jQuery UI nos proporciona una forma ágil y sencilla de construir componentes asociables a elementos de DOM mediante la factoría de widgets.

La factoría de widgets nos da una serie de funcionalidades que si tuviésemos que escribir por nosotros mismos cuando creamos un plugin nos llevaría cierto tiempo y esfuerzo.


## ¿Qué son los widgets de jquery ui?

Jquery UI nos proporciona una serie de componentes para crear de manera rápida y fácil elementos web ampliamente utilizados tales como acordeones, tabs, cajas de texto con funciones de autocompletado, botones, efectos, etc.

Un widget, según la librería Jquery UI es un objeto asociado a elementos de una página web que proporciona servicios para la gestión del ciclo de vida, estado, herencia, aspecto y comunicaciones con otros widgets u objetos de javascript.

Un widget no es más que un plugin jquery. Parece que no son similares en su definición pero realmente se están aplicando las mismas normas y sobre los mismos objetos que cuando se define un plugin corriente. En muchas ocasiones la definición de un plugin es más que suficiente pero si realmente necesitamos aplicar comportamiento o gestión de estado a objetos individuales, además de gestionar su ciclo de vida, entonces un widget es realmente de más utilidad ya que nos ofrece por defecto una serie de características que, de otra forma, sobre un plugin normal tendríamos que implementar a mano.

Todo el que conoce jquery sabe normalmente como utilizar un widget. Se puede decir que se trata de un plugin especializado que ofrece por defecto mecanismos de gestión de estado, exposición de métodos, mezcla de opciones de usuario con opciones por defecto y asociación de datos con sus elementos.

La creación se lleva a cabo invocando a la factoría de widgets (**$.widget**), una vez hecho esto se crea un método en el jquery prototype, **$.fn**, de la misma manera que se haría si estuviesemos creando un plugin. Este método es la interfaz primaria para aplicar widgets a elementos.


## Definiendo un widget

Las dependencias para la creación de widgets se pueden resolver con referencias a scripts presentes en algún [CDN](http://www.asp.net/ajaxlibrary/cdn.ashx "Content Delivery Network") para jquery y jquery UI. Los widgets se definen en su propio archivo .js. La manera de escribirlos, utilizando cierres parecidos a los de los plugins, sería de la siguiente forma

```javascript
 (function($) {
   $.widget('ns.form()', {
     // definición del widget
   });
 }(jQuery));
```

Como se puede apreciar, el argumento pasado es el propio plugin de jquery ($) que nos permite usarlo en el interior del componente.

La llamada a $.widget hace que el widget esté disponible para su uso.

El primer argumento *ns.form* es una suma del namespace (*ns*) y el nombre (*form*). El nombre se guardará en el prototype de jquery ($.fn). El segundo argumento es el *widget prototype* que es un objeto que define el comportamiento del widget y que se almacenará en el objeto jquery, en este caso bajo *$.ns.form().*


## Usando un Widget

Una vez se ha definido el componente queda listo para usarse con los elementos del DOM. Para invocarlo se hace exactamente de la misma forma que con otros plugins jquery, usando selectores sobre conjuntos de elementos del DOM. En el siguiente ejemplo podemos ver como se puede aplicar nuestro widget sobre todos los elmentos de tipo <form>

```javascript
$('form').form();
```

Cuando se aplica sobre un elemento, se está realmente creando una instancia de dicho widget y se está almacenando en el elemento del DOM.


## Gestionando el ciclo de vida

### *Invocando al widget*

Cuando se instancia un widget por primera vez sobre un elemento, se invoca la función **_create**. Los métodos precedidos por guión bajo se consideran privados por convención y sólo se invocan desde el interior del widget. A continuación se muestra el código de creación del widget **form** y que se encargará de enriquecer los formularios que se seleccionan mediante jquery. Vamos a escribir todo el código necesario y posteriormente vamos verlo paso a paso.

```javascript
(function($) {
  $.widget("ns.form", {
    widgetEventPrefix: "f",
    options: {
      tmargin: '10px',
      viewinfo: $.noop,
      hideinfo: $.noop
    },
    _create: function() {
      var object = this;
      var form = this.element;
      var inputs = form.find("input ,textarea");

      form.find("fieldset").addClass("ui-widget-content ui-corner-all");
      form.find("legend").addClass("ui-widget-header ui-corner-all");
      form.addClass("ui-widget");

      $.each(inputs, function() {
        $(this).wrap("<div></div>");
        $(this).parent().css("margin-top", object.options.tmargin);
        if ($(this).is(":checkbox") || $(this).is(":radio")) {
          $(this).wrap("<label />");
        }
        if ($(this).is(":checkbox"))
          object._checkboxes(this);
        else if ($(this).is("input[type='text']") || $(this).is("textarea") || $(this).is("input[type='password']"))
          object._textelements(this);

        $(this)
          .bind('mouseenter', function(event) {
            object._trigger('viewinfo', event, {
              cajadetexto: $(this)
            });
          })
          .bind('mouseleave', function(event) {
            object._trigger('hideinfo', event, {
              cajadetexto: $(this)
            });
          });
      });
    },

    _textelements: function(element) {
      $(element).bind({
        focusin: function() {
          $(this).toggleClass('ui-state-focus');
        },
        focusout: function() {
          $(this).toggleClass('ui-state-focus');
        }
      });
    },

    _checkboxes: function(element) {
      $(element).parent("label").after("<span />");
      var parent = $(element).parent("label").next();
      $(element).addClass("ui-helper-hidden");
      parent.css({
        width: 16,
        height: 16,
        display: "block"
      });
      parent.wrap("<span class='ui-state-default ui-corner-all' style='display:inline-block;width:16px;height:16px;margin-right:5px;'/>");
      parent.parent().addClass('hover');
      if (element.checked) {
        parent.addClass("ui-icon ui-icon-check");
      }
      parent.parent("span").click(function(event) {
        $(this).toggleClass("ui-state-active");
        parent.toggleClass("ui-icon ui-icon-check");
        $(element).click();
      });
    },

    _setOption: function(name, value) {
      if (name === 'tmargin') {
        this.element.find("input,textarea").parent().css("margin-top", value);
      }
      $.Widget.prototype._setOption.apply(this, arguments);
    },

    changeBorderColor: function(color) {
      var $fieldset = this.element.find("fieldset");
      $fieldset.css("border-color", color);
      $.ns.form.showMessage("llamada a método estático desde el interior del widget");

    },

    destroy: function() {
      // if using jQuery UI 1.8.x
      $.Widget.prototype.destroy.call(this);
      // if using jQuery UI 1.9.x
      //this._destroy();
    }
  });

  $.extend($.ns.form, {
    showMessage: function(message) {
      alert(message);
    }
  });

})(jQuery);
```

El widget **form**, mediante la llamada a la función **_create** añade clases de jquery ui a los elementos del formulario. Después recorre cada uno de los elementos, y en caso de ser de tipo text o checkbox llama a los métodos privados _checkboxes y _textelements que se encargarán a su vez de enriquecer este tipo de elementos.

*Creacion*

```javascript
_create: function() {
    var object = this;
    var form = this.element;
    var inputs = form.find("input ,textarea");

    form.find("fieldset").addClass("ui-widget-content ui-corner-all");
    form.find("legend").addClass("ui-widget-header ui-corner-all");
    form.addClass("ui-widget");

    $.each(inputs, function() {
    $(this).wrap("<div></div>");
    $(this).parent().css("margin-top", object.options.tmargin);
    if ($(this).is(":checkbox") || $(this).is(":radio")) {
        $(this).wrap("<label />");
    }
    if ($(this).is(":checkbox"))
        object._checkboxes(this);
    else if ($(this).is("input[type='text']") || $(this).is("textarea") || $(this).is("input[type='password']"))
        object._textelements(this);

    $(this)
        .bind('mouseenter', function(event) {
        object._trigger('viewinfo', event, {
            cajadetexto: $(this)
        });
        })
        .bind('mouseleave', function(event) {
        object._trigger('hideinfo', event, {
            cajadetexto: $(this)
        });
        });
    });
}
```

*Métodos privados*

```javascript
_textelements: function(element) {
    $(element).bind({
        focusin: function() {
        $(this).toggleClass('ui-state-focus');
        },
        focusout: function() {
        $(this).toggleClass('ui-state-focus');
        }
    });
    },

    _checkboxes: function(element) {
    $(element).parent("label").after("<span />");
    var parent = $(element).parent("label").next();
    $(element).addClass("ui-helper-hidden");
    parent.css({
        width: 16,
        height: 16,
        display: "block"
    });
    parent.wrap("<span class='ui-state-default ui-corner-all' style='display:inline-block;width:16px;height:16px;margin-right:5px;'/>");
    parent.parent().addClass('hover');
    if (element.checked) {
        parent.addClass("ui-icon ui-icon-check");
    }
    parent.parent("span").click(function(event) {
        $(this).toggleClass("ui-state-active");
        parent.toggleClass("ui-icon ui-icon-check");
        $(element).click();
    });
    },
```

Es importante tener en cuenta la línea siguiente del método _create

```javascript
var object = this;
````

Se suele hacer para capturar la referencia al widget debido a que en el interior de los handlers que se definan posteriormente como en el caso de **$.each(..),** la referencia a **this** ya no será el widget, sino el elemento sobre el que se está aplicando la función. De esta manera siempre mantenemos la referencia para usarlo en el interior de los handlers como se puede apreciar a continuación

```javascript
object._checkboxes(this);
````

El método de creación se suele usar para añadir clases a los elementos que seleccionados y así darles estilo, crear elementos nuevos en el DOM o invocar a otros componentes y plugins.

### *Inicialización*

Se usa el método **_init**. Este método se llama justo después del método **_create** y cada vez que el componenete es invocado desde código. El método **_create** se ejecuta una vez y el método **_init**, N veces.

Es el sitio adecuado para introducir código de inicio más complejo, aunque puede no implementarse y de hecho no es necesario en muchos casos. De hecho en nuestro ejemplo no lo haremos.

### *“Destrucción”*

Para desasociar un widget de un elemento se usa el método **destroy**. El objetivo de este método es dejar el elemento sobre el que se instancio el widget exactamente igual que antes de que se aplicase. Por tanto lo normal es eliminar clases y elementos del DOM que se hayan añadido previamente.

Si no se proporciona un método **destroy** en la implementación, se llamará al método **destroy** base de la factoría de widgets de jquery ui. Esta implementación base se encargará de eliminar la asociación con el elemento y de eliminar el espacio de nombres y eventos asociados con el objeto jquery

```javascript
destroy: function() {
// si estás usando jQuery UI 1.8.x
  $.Widget.prototype.destroy.call(this);
  // si estás usando jQuery UI 1.9.x
  //this._destroy();
}
```

En nuestro ejemplo, se llama simplemente al método base. Realmente, como se ha expuesto en el párrafo anterior, no es necesario ya que se llamaría por defecto pero creo que es una buena práctica incluirlo. Faltaría eliminar todo lo que hemos añadido, clases css, etc, pero se ha omitido por brevedad.


## Definiendo opciones

Es posible pasar parámetros a un widget. Estos parámetros suelen ser valores o funciones que se puedan usar en el interior al estilo de los delegados o Expressions en C#.

En el momento de la creación, las opciones que se pasen como parámetros se mezclan automáticamente con las opciones por defecto. Además, la factoría de widgets soporta notificaciones de cambio en dichas opciones cuando los valores se modifican desde código. Si estuviésemos creando un plugin en lugar de un widget esta “fontanería” habría que gestionarla por nuestra cuenta.

Las opciones se definen en la propiedad **options**

```javascript
options: {
  tmargin: '10px',
  viewinfo: $.noop,
  hideinfo: $.noop
},
```

Se pueden sobreescribir estas opciones desde código cada vez que invocamos al widget

```javascript
$(document).ready(function() {
  $("form").form({
    tmargin: "20px"
  });
});
```

Como vemos, estamos sobreescribiendo la propiedad **tmargin**.

Para leer las opciones se accede a la propiedad **options** directamente como se puede observar a continuación

```javascript
$(this).parent().css("margin-top", object.options.tmargin);
````

Para leer valores, es correcto realizarlo de esta forma, para escribir sin embargo deberiamos usar el método **option**

```javascript
this.option("tmargin","30px");
````

Este método se llama con un argumento cuando se quiere leer el valor y con dos cuando se quiere asignar el valor. Modificar directamente el valor sin usar el método, hace que la factoría de widgets no detecte el cambio en el valor y no se lancen los métodos apropiados para la gestión de este cambio.


## Gestionando los cambios en los valores

Cuando se detecta un cambio en las opciones se llama al método privado **_setOption**. Sólo se llama a este método, como acabamos de ver, cuando se asigna la propiedad por medio del método **option**.

El código del método **_setOption** lo escribimos en el interior del widget definiendo lo que se ha de hacer cuando se detecte la modificación. Por ejemplo, vamos a modificar el margin-top de las cajas que contienen los diferentes elementos

```javascript
_setOption: function(name, value) {
    if (name === 'tmargin') {
    this.element.find("input,textarea").parent().css("margin-top", value);
    }
    $.Widget.prototype._setOption.apply(this, arguments);
},
```

y para modificar el valor desde el exterior

```javascript
$("form").form({tmargin:$("#valordistancia").val()});
````

Normalmente necesitaremos más código que este, por lo que lo mejor en estos casos es llamar a un método privado del widget que hayamos definido en el mismo.

La última linea llama al método base de **_setOption** que se encargará de asignar el nuevo valor.

Comentar que este método **_setOption** lógicamente no se llama cuando se instancia el widget por primera vez.


## Desactivando un widget

Es posible desactivar un widget estableciendo la opción **disabled** que por defecto estara a *false*

```javascript
$(selector).form('option', 'disabled');
```


## Pasando funciones como opciones

Se pueden pasar funciones como opciones de manera que se puedan usar en el interior. Este mecanismo es de gran ayuda para desacoplar el widget que estamos creando de su entorno. Por ejemplo, podríamos hacer que cuando pasemos el ratón por encima de una caja de texto se mostrase un popup con información acerca del campo a introducir. Para ello definimos las funciones **viewinfo** y **hideinfo** en las **options** de nuestro componente

```javascript
options: {
    tmargin: '10px',
    viewinfo: $.noop,
    hideinfo: $.noop
},
```

Se definen como cualquier otra opción. El uso de **$.noop** puede sustituirse por *null*, pero usando **$.noop** nos evitamos validar que la función sea *null* antes de llamarla.

Para llamar a las funciones que pasemos como parámetros usaremos **_trigger**

```javascript
$(this)
  .bind('mouseenter', function() {
    object._trigger('viewinfo');
  })
  .bind('mouseleave', function() {
    object._trigger('hideinfo');
  });
```

… y las pasamos desde código externo cuando lo invocamos por primera vez …

```javascript
$("form").form({
  tmargin: "20px",
  viewinfo: function(event, ui) {
    $("body").css("background", "blue");
  },
  hideinfo: function(event, ui) {
    $("body").css("background", "white");
  }
});
```

Como se puede ver en el ejemplo, se están pasando dos funciones que hacen que se modifique el color de fondo de la página a azul cuando ponemos el raton encima de alguna caja de texto o textarea.


## El método widget

La interfaz de un widget consiste en un único método, el método  **widget.**

Este método es la **interfaz pública**. La acción que se desencadene a raiz de la llamada al método depende de los parámetros que se le pasen al mismo. Este método se encarga de crear y inicializar el componente. Además también invocará a los **métodos públicos, leerá y asignará propiedades y leerá y asignará opciones**.

### *Métodos públicos*

Los métodos públicos se definen en el *widget prototype* como se puede apreciar en el widget **form** que estamos definiendo

```javascript
changeBorderColor: function(color) {
  var $fieldset = this.element.find("fieldset");
  $fieldset.css("border-color", color);
  $.ns.form.showMessage("llamada a método estático desde el interior del widget");
},
```

*changeBorderColor* es un método público que se llamará desde cualquier script externo y que se encarga de modificar el color de *border* del formulario con el pasado como parámetro. Los widgets han de ser creados antes de invocar a sus métodos públicos.

Este método se podría invocar de la siguiente manera

```javascript
$("form").form("changeBorderColor", "red");
````

El método **option **visto anteriormente es un ejemplo de método público. Cuando se le pasa un argumento, el método devuelve el valor de la opción. Cuando se le pasan dos argumentos, se asigna la opción especificada en el primer argumento con el valor pasado como segundo argumento.

Cuando se llama al método **option** desde un script externo se pasa el nombre del método en primer lugar, la opción a modificar como segundo argumento, y el valor a asignar por último.

### *Reusando instancias*

Cada vez que se invoca al método **widget**, se produce un nuevo acceso al DOM para recuperar el objeto mediante el selector que estamos aplicando. Hay formas más eficientes de hacer este trabajo ya que no es necesario recuperar el objeto jquery cada vez.

Una de estas formas de mejorar la eficiencia en las llamadas es simplemente asignar una variable en la primera llamada al método y a partir de aquí las subsecuentes llamada se harán sobre esta variable. La primera llamada hará una query sobre el DOM, pero el resto no.

```javascript
var form = $('form').form();
// selector sobre el DOM
form.form("changeBorderColor","red");
// no accede al DOM
```

**Usando pseudo selectores**

A veces puede darse la situación en la que no disponemos de acceso a la instancia del widget. Es posible usar pseudo selectores para recuperar todas las instancias.

En nuestro ejemplo se haría así

```javascript
var forminstances = $(':ui-form');
forminstances.form("changeBorderColor","red");
````

Con este código se seleccionan todas las instancias del widget y se modifica el color del borde a rojo.

```
":" + espacio de nombres + "-" +  nombre del widget
```

### *Métodos privados*

Los métodos y propiedades privadas sólo se pueden invocar o utilizar en el interior del widget. Usar este tipo de métodos es una buena idea ya que incrementan la legibilidad del código.

El nombre de este tipo de métodos privadas comienza por convención con un guión bajo seguido por el nombre del método. La forma de acceder a ellos es a través de **this**

Hay que recalcar que los métodos privados son privados por convención. Es decir, si no se usa el widget de manera correcta para acceder a sus miembros públicos, entonces estos métodos aún son accesibles desde el exterior.

### *Propiedades*

Las propiedades son privadas por defecto. No necesitan de convenciones ya que no se pueden acceder desde el exterior

```javascript
$.widget('ui.form', {
    propiedad1: null,   // Sólo se puede acceder mediante this.propiedad1
    _propiedad2: null  // No es necesario el "_"
});
```

Como cada elemento seleccionado contiene su propia instancia, **propiedad1** puede ser diferente en cada una de las instancias. Existe una manera de compartir el valor entre todas, a través de **miembros estáticos.**

### *Miembros estáticos*

Para definir una variable que pueda ser compartida entre todas las instacias de un widget se define sobre el método **widget** por convención

Si se necesita acceder a miembros estáticos desde el exterior, pueden añadirse fácilmente después de la definición del widget mediante **$.extend**.

Esté método se usa para mezclar el objeto pasado como segundo argumento en el objeto pasado como primer argumento. Por tanto se añadirán los métodos definidos con **$.extend** al prototype

```javascript
$.extend($.ns.form, {
  showMessage: function(message) {
    alert(message);
  }
});
````

Una vez echo esto, el método es accesible para cualquier miembro del widget y además también se puede acceder desde fuera.

```javascript
$.ns.form.showMessage("textodelmensaje");
````

## Eventos

Los métodos son una manera de comunicación entre widgets. jQuery soporta y extiende el modelo de eventos del DOM y proporciona la habilidad de lanzar y gestionar eventos que no están definidos en el DOM

### *Lanzando eventos*

Los widgets lanzan eventos mediante el uso del método **_trigger**. El primer argumento del método **_trigger** es el nombre del evento que se está lanzando. Si el evento se origina en el DOM, como segundo argumento se puede pasar el evento originado en el DOM. Como tercer argumento se pasará cualquier tipo de objeto de datos. Tanto este tercer argumento como el segundo son opcionales

```javascript
$(this)
  .bind('mouseenter', function(event) {
    object._trigger('viewinfo', event, {
      cajadetexto: $(this)
    });
  })
```

Como se puede ver en el código de nuestro ejemplo, **form** está lanzando un evento “viewinfo” cuando se produce el evento mouseenter sobre un elemento de tipo input (está en el interior de $.each por lo tanto *this* se refiere a los elementos de tipo input).

El evento “viewinfo” que se está lanzando se define desde el exterior, como habíamos visto, y se pasa como opción. Al lanzar el evento, le estoy pasando **event** que es el **mouseenter** que provocó el lanzamiento de “viewinfo” y además el **input** sobre el que se lanzó.

### *Manejadores de eventos (handlers)*

La forma de enlazar eventos en un widget es la misma que en cualquier manejador de eventos de jquery. La única diferencia es que el nombre del evento que se lanza está influenciado por el nombre del widget.

Si quiero hacer un **bind** desde el exterior tengo que tener en cuenta que el nombre del evento estará precedido por el nombre del widget.

```javascript
$('form').bind('formviewinfo', function(event, data) {
    // código del evento
});
````

El evento “viewinfo” estará precedido por **form**.

Esta aproximación es perfectamente válida y proporciona la misma funcionalidad que cuando suscribo el evento en el inicializador del widget

```javascript
$('form').bind('formviewinfo', function(event, data) {
    // código del evento
});
````


Si quiero modificar el nombre que precede al del evento puedo modificar la propiedad **widgetEventPrefix** . Por defecto el valor es el nombre del widget, pero puedo modificarlo de la siguiente forma

```javascript
$.widget('ns.form', {

    widgetEventPrefix: 'f',

    ...
```

Ahora el nombre del evento lanzado variaría

```javascript
$('form').bind('fviewinfo', function(event, data) {
  // código del evento
});
```

Cuando se definen opciones mediante funciones como en el caso de viewinfo y hideinfo el método **_trigger** del widget base invocará directamente a la función que le corresponda cuando lo utilizemos.

De esta forma se pueden definir directamente lo manejadores de eventos en la creación del widget como en el caso de nuestro **form**.

Hasta aquí hemos llegado… espero haber aclarado un poco cómo se puede hacer la definición de widget y la exccelente funcionalidad que nos ofrecen.

Hasta luego!!



---
layout: post
title: Como crear una aplicación ASP MVC de una sóla página mediante ajax (parte 1)
date: '2012-04-16 13:36:13'
tags:
- spa
---


En este artículo vamos a ver cómo podemos hacer que una aplicación ASP MVC se comporte como una aplicación de escritorio, de manera que se cargue una página inicialmente y el resto de las recargas (o casi todas) se produzcan via ajax insertando el contenido en alguna sección que hayamos designado para tal efecto.

Con la salida de Visual Studio 2011, se incluye una plantilla para crear aplicaciones de una sóla página mediante una serie de librerías que hacen todo el trabajo por nosotros como **upshot**, **knockout** … y usando la nueva [ Web API](http://www.asp.net/web-api "Web Api") que nos proporciona servicios tipo **REST** con un desarrollo muy similar al de cualquier controlador **ASP MVC** pero heredando de la clase [DbDataController](http://msdn.microsoft.com/en-us/library/hh833861%28v=vs.108%29.aspx "La clase DbDataController"). Esta herencia acopla bastante el controlador con el ORM de acceso a datos pero bueno, eso es otra historia.

La cuestión es que revisando esta plantilla, que está incluida de forma experimental, me parece que se está haciendo demasiada magia y la verdad es que me parece que es un poco complicada de seguir y saber qué esta pasando detrás del telón.

He intentado crear una solución un poco más “casera” basándome [en este excelente post](http://geeks.ms/blogs/etomas/archive/2011/10/08/aplicaciones-quot-de-una-sola-p-225-gina-con-html5-y-asp-net-mvc.aspx "Aplicaciones de una sóla página") de *Eduard Tomás i Avellana.*

La variación que me he permitido la licencia de implementar es mediante la creación de un **widget** de **jquery ui** que se incluya en la página maestra y que haga el “trabajo sucio” de realizar las recargas de página.

Al widget le he llamado **spalinks**, que no es mandar links a un tratamiento termal, sino hacer que los links de nuestras páginas se *“ajaxifiquen”* y por tanto funcionen en modo *“single page application”.*

El widget lo que hace es buscar links en el elemento DOM sobre el que lo hayamos instanciado y que hayan sido marcados con el atributo **data-ajax=”true”** y realizar un *bind* de su evento *click* para que cuando se pulse sobre el mismo se haga una carga vía ajax del contenido recuperado del servidor en una sección objetivo que hayamos pasado como opción.

De paso, he creado una [aplicación ASP MVC](http://mvcspaapplication.apphb.com/ "Aplicacion de ejemplo") en Visual Studio 2011 (MVC 4) que utiliza como frontend [Bootstrap](http://twitter.github.com/bootstrap/ "Bootstrap") para probarlo.

El post lo he dividido en dos partes … en esta primera veremos la implementación del widget y en [la siguiente parte](../../../Post/GetPostByCode/como_crear_una_aplicacion_ASP_MVC_de_una_s%C3%B3la_p%C3%A1gina_mediante_ajax_parte_2 "Como crear una aplicacion ajax ASP MVC. Parte 2") cómo lo usaríamos sobre una aplicación ASP MVC

El código del widget será el siguiente:

```javascript
(function ($, window) {
    $.widget("ui.spalinks", {
        //Options passed to the widget
        //target: the DOM element selected to be the container of the ajax updates
        //beforeajax: function that executes before the ajax request happens
        //afterajax: function that executes after the ajax request happens
        //useloading: when true a ajax loader indicator will be showed
        //loadingdefaulttext: The text to be displayed within the indicator container
        //loadingdefaultclass: If don´t like the default style you can define a class and pass it to the widget
        options: {
            target: $.noop,
            beforeajax: $.noop,
            afterajax: $.noop,
            useloading: false,
            loadingdefaulttext: "Loading ...",
            loadingdefaultclass: ""
        },
        //Widget creation. Executes only once
        _create: function () {
            var self = this;
            var source = this.element;
            self._bindlinks(source);
            $(window).bind('popstate', function (evt) {
                self._dopopstate(evt.originalEvent.state);
            });
        },
        //private method
        //param source: A link to be bound
        _bindlinks: function (source) {
            var self = this;
            source.find("a[data-ajax]").live("click", function (event) {
                self._trigger("beforeajax");
                if (self.options.useloading == true) {
                    self._addLoading();
                }
                self._ajaxload($(this).attr("href"), true);
                return false;
            });
        },
        //private method
        //param url: The url to pick de html data for refreshing the target container
        //param add: Boolean indicating if it is necessary to add a new url to the history object
        _ajaxload: function (url, add) {
            var self = this;
            if (add) {
                history.pushState({
                    uri: url
                }, '', url);
            }
            $(this.options.target).load(url, function () {
                self._trigger("afterajax");
                if (self.options.useloading == true) {
                    self._removeLoading();
                }
            });
        },
        //private method
        //param data: The data object containing the uri for doing pop when pressing back
        //button in browser. The event is being bound in _create
        _dopopstate: function (data) {
            if (data != null) {
                this._ajaxload(data.uri, false);
            }
        },
        //private method
        //Add the loader to the DOM element
        _addLoading: function () {
            var self = this;
            var loading;
            if (self.options.loadingdefaultclass != "") {
                loading = $('<div id="spalinks_loader style="display:none"></div>')
                    .addClass(self.options.loadingdefaultclass)
                    .append($("<p></p>").text(self.options.loadingdefaulttext));
            } else {
                loading = $('<div id="spalinks_loader" style="display:none; position: fixed; top: 0; left: 0; z-index: 5000; font-size: 150%; width:100%;"></div>')
                    .append($('<p style="margin: 0 auto; text-align:center; background-color: #7BB33D; color: white; font-weight:bold; font-size:15px; padding: 6px; width:22%; -moz-border-radius: 0 0 5px 5px; border-radius: 0 0 5px 5px; -moz-box-shadow: 0 1px 15px #7BB33D; -webkit-box-shadow: 0 1px 15px #7BB33D; box-shadow: 0 1px 15px #7BB33D;"></p>').text(this.options.loadingdefaulttext));
            }
            $(this.element).append(loading);
            $("#spalinks_loader").slideDown();
        },
        //Private method
        //Remove the loader from the DOM
        _removeLoading: function () {
            $("#spalinks_loader").slideUp(function () {
                $(this).remove();
            });
        },
        //Generic method of the widget factory to set options from outside the widget
        _setOption: function (name, value) {
            $.Widget.prototype._setOption.apply(this, arguments);
        },
        //Destroy widget instance
        destroy: function () {
            // if using jQuery UI 1.8.x
            $.Widget.prototype.destroy.call(this);
            // if using jQuery UI 1.9.x
            //this._destroy();
        }
    });
})(jQuery, window);
```

Si no sabes como crear widget con jquery, puedes echarle un ojo a la [documentación del producto](http://wiki.jqueryui.com/w/page/12138135/Widget%20factory "jquery widget factory"), o bien leer [este post](../../../Post/GetPostByCode/creando_widgets_con_jquery "Creando widget con jquery") escrito en esta misma web.

Hemos definido una serie de opciones para iniciar el widget:

<p><strong>target</strong>: El objetivo en el cual se realizará la carga de los contenidos que nos devuelva la petición ajax</p>
<p><strong>beforeajax</strong>: Una función que se ejecutará antes de la petición al servidor</p>
<p><strong>afterajax</strong>: Una función que se ejecutará después de la respuesta del servidor y una vez los contenidos se hayan cargado</p>
<p><strong>useloading: </strong>Si es <em>true </em>se mostrará un indicador hasta que se complete la carga de contenidos<strong></strong></p>
<p><strong>loadingdefaulttext: </strong>El texto que se mostrará en el contenedor del indicador<strong></strong></p>
<p><strong>loadingdefaultclass: </strong>Si no nos gustan los estilos aplicados al indicador, podemos definir una clase que lo maquete</p>

Sólo el **target** es obligatorio porque si no el widget no sabe donde dejar los contenidos recuperados. El resto de opciones son opcionales. Por defecto no se mostrará el indicador de cargando.

En el [siguiente post](../../../Post/GetPostByCode/como_crear_una_aplicacion_ASP_MVC_de_una_s%C3%B3la_p%C3%A1gina_mediante_ajax_parte_2 "Como crear una aplicacion ajax ASP MVC. Parte 2") veremos como usarlo

Hasta pronto!!


---
layout: post
title: Creando una página web usando KnockoutJs
date: '2012-06-06 09:15:02'
tags:
  - knockout
categories:
  - Frontend
---

knockout es una pequeña gran librería javascript que nos proporciona capacidades de binding entre objetos javascript y los elementos HTML del DOM aplicando el Patrón MVVM.

La librería [knockout](http://knockoutjs.com) nos facilita la creación de ricas interfaces de usuario permitiendo el enlace de un modelo de datos con una vista a través de un viewmodel.

El modelo de datos lo representarán normalmente datos almacenados en una base de datos. El viewmodel, será una representación del modelo de datos que trabajará con una vista determinada. Este viewmodel, además de almacenar los datos, contendrá las operaciones que se pueden hacer a través de los componentes de la interfaz de usuario (respuesta a eventos como click, submit de formularios, … etc.).

**Knockout** no es la única librería que puede hacer este tipo de cosas, pero es de la que vamos a hablar aquí porque además está incluída por defecto en las plantillas de proyectos **ASP MVC 4**, con lo cual se convierte en oficial para los desarrolladores .NET.

La mejor manera de ver qué se puede hacer con **knockout** es a través de un ejemplo. El ejemplo que vamos a crear será bastante sencillo y trabajará con un objeto en memoria conteniendo los datos.


## Comenzamos

La mini-página que vamos a crear simula una página web en la que se permite consultar los diferentes coches que tienen en venta en una serie de concesionarios. La página mostrará los datos del concesionario y los modelos en venta.

No hace falta Visual Studio ni nada parecido, simplemente es necesario un editor de texto y un navegador para ver el resultado.

Vamos a crear en una carpeta cualquiera vacía, dos subcarpetas en su interior para incluir las dependencias necesarias y para almacenar las hojas de estilos. Las vamos a llamar ***js*** y ***css***.


## Creando el viewmodel

En primer lugar, en la carpeta ***js***vamos a crear el viewmodel que se encargará de representar el modelo de datos subyacente y que además recuperará los datos de test de la memoria.

Por tanto creamos un archivo de script llamado *carsViewModel.js*

Vamos a añadir una clase *carsViewModel* al nuevo archivo que representará el viewmodel que vamos a usar para asociar a la vista que crearemos a continuación

```javascript
function carsViewModel() {
  var self = this;
}
```

Ahora vamos a añadir una serie de propiedades a dicha clase

```javascript
self.id = ko.observable();
self.concesionario = ko.observable();
self.cif = ko.observable();
self.coches = ko.observableArray();
```

El truco para el enlace de datos está en las funciones *observable()* y *observableArray()* que se encargan de comprobar los cambios que se puedan producir en el modelo de datos para propagarlos a la interfaz de usuario. Por tanto, definiremos aquí todas las propiedades que necesitemos, en este caso un ***id*** para identificar al concesionario, que a su vez tendrá un nombre (***concesionario***) y un ***cif***. Además añadimos una propiedad ***coches*** que será un array en el que almacenará una lista de coches.

Definimos a continuación una función que tendrá como responsabilidad mostrar los datos de un concesionario en concreto.

```javascript
self.mostrarconcesionario = function(id) {
  var model = getModel(id);
  self.id(model.id);
  self.concesionario(model.concesionario);
  self.cif(model.cif);
  self.coches(model.coches);
};
```

En el cuerpo de la función estamos recuperando el modelo llamando a *getModel(id)* siendo ***id*** el identificador del concesionario. Una vez recuperado asignamos a las propiedades del viewmodel los valores obtenidos.

La recuperación de datos que hace *getModel()* normalmente se hará contra una base de datos, pero nosotros vamos a hacerlo obteniendo los datos de un objeto en memoria. La implementación de *getModel()* se hace fuera del viewmodel y quedaría así.

```javascript
function getModel(id) {
  if (id == "1") {
    var object = {
      id: "1",
      concesionario: "Automoviles Marte S.A.",
      cif: "A12345678",
      coches: [{
          marca: "Mercedes",
          modelo: "Clase A",
          colores: [{
            color: "white"
          }, {
            color: "yellow"
          }, {
            color: "green"
          }]
        },
        {
          marca: "Audi",
          modelo: "A4",
          colores: [{
            color: "red"
          }, {
            color: "black"
          }, {
            color: "green"
          }]
        },
        {
          marca: "Citroen",
          modelo: "C5",
          colores: [{
            color: "red"
          }, {
            color: "yellow"
          }, {
            color: "blue"
          }]
        },
        {
          marca: "Citroen",
          modelo: "C5",
          colores: [{
            color: "red"
          }, {
            color: "yellow"
          }, {
            color: "blue"
          }]
        },
        {
          marca: "Mercedes",
          modelo: "SLK",
          colores: [{
            color: "red"
          }, {
            color: "yellow"
          }, {
            color: "blue"
          }]
        },
        {
          marca: "Kia",
          modelo: "Sportage",
          colores: [{
            color: "red"
          }, {
            color: "yellow"
          }, {
            color: "blue"
          }]
        },
        {
          marca: "Ford",
          modelo: "Ka",
          colores: [{
            color: "red"
          }, {
            color: "yellow"
          }, {
            color: "blue"
          }]
        },
        {
          marca: "Renault",
          modelo: "Clio",
          colores: [{
            color: "red"
          }, {
            color: "yellow"
          }, {
            color: "blue"
          }]
        },
        {
          marca: "Ford",
          modelo: "Focus",
          colores: [{
            color: "red"
          }, {
            color: "black"
          }, {
            color: "green"
          }]
        },
        {
          marca: "Citroen",
          modelo: "Xsara",
          colores: [{
            color: "red"
          }, {
            color: "yellow"
          }, {
            color: "blue"
          }]
        },
        {
          marca: "Audi",
          modelo: "TT",
          colores: [{
            color: "red"
          }, {
            color: "yellow"
          }, {
            color: "blue"
          }]
        },
        {
          marca: "Lamborgini",
          modelo: "Diablo",
          colores: [{
            color: "red"
          }, {
            color: "black"
          }, {
            color: "green"
          }]
        },
        {
          marca: "Seat",
          modelo: "600",
          colores: [{
            color: "green"
          }, {
            color: "black"
          }, {
            color: "yellow"
          }]
        },
        {
          marca: "Fiat",
          modelo: "500",
          colores: [{
            color: "red"
          }, {
            color: "orange"
          }, {
            color: "green"
          }]
        }
      ]
    }
  } else if (id == "2") {
    var object = {
      id: "2",
      concesionario: "Automoviles Jupiter S.A.",
      cif: "B33345678",
      coches: [{
          marca: "Kia",
          modelo: "Picanto",
          colores: [{
            color: "red"
          }, {
            color: "orange"
          }, {
            color: "green"
          }]
        },
        {
          marca: "Mazda",
          modelo: "X5",
          colores: [{
            color: "black"
          }, {
            color: "yellow"
          }, {
            color: "green"
          }]
        },
        {
          marca: "Hyundai",
          modelo: "Accent",
          colores: [{
            color: "red"
          }, {
            color: "yellow"
          }, {
            color: "black"
          }]
        }
      ]
    }
  } else if (id == "3") {
    var object = {
      id: "3",
      concesionario: "Automoviles Saturno S.A.",
      cif: "A12444678",
      coches: [{
          marca: "Toyota",
          modelo: "Prius",
          colores: [{
            color: "blue"
          }, {
            color: "yellow"
          }, {
            color: "green"
          }]
        },
        {
          marca: "Audi",
          modelo: "A3",
          colores: [{
            color: "red"
          }, {
            color: "grey"
          }, {
            color: "green"
          }]
        },
        {
          marca: "Ferrari",
          modelo: "F40",
          colores: [{
            color: "red"
          }, {
            color: "blue"
          }, {
            color: "green"
          }]
        }
      ]
    }
  }
  return object;
}
```

Esta función no hace nada especial. Lo único a comentar es que pese a que coches es un *observableArray(),* podemos pasarle un array con subarrays (colores) y no hay ningún problema, **knockout** lo mapeará correctamente y podremos acceder a los subarray de *colores* desde la vista.

También querremos añadir y eliminar coches por lo que crearemos un par de funciones para hacerlo.

```javascript
self.removeCoche = function(data) {
  self.coches.remove(data); 
};
```

Simplemente elimina un objeto (*data*) del *observableArray()*

```javascript
self.addCoche = function(data) {
  var serializedForm = $(data).serializeArray();
  var newcar = [];
  $.each(serializedForm,
    function() {
      if (this.name != "color") {
        newcar[this.name] = this.value;
      } else {
        if (!newcar["colores"]) {
          newcar["colores"] = [];
        }

        newcar["colores"].push(new Color(this.value));
      }
    });
  self.coches.push(new Coche(newcar.marca, newcar.modelo, newcar.colores));
};
```

Un poco más compleja porque vamos a hacer un *POST *ficticio mediante el *submit* de un formulario que colocaremos en la vista y por tanto la función *addCoche()* recibirá dicho *form* como parámetro (*data*). Lo que hace la función es serializar el formulario (*serializeArray*) y recorrer el objeto *JSON* generado para obtener otro objeto con el formato correcto y así añadir al array de coches.

Como observación, se están instanciando dos clases para crear objetos de tipo ***Coche***y ***Color***por lo que habrá que definirlas. La definición se hace también fuera del viewmodel.

```javascript
function Color(color) {
  this.color = color;
}

function Coche(marca, modelo, colores) {
  this.marca = marca;
  this.modelo = modelo;
  this.colores = colores;
}
```

Por último, en el viewmodel, se llamará a *mostrarconcesionario()* para mostrar uno por defecto al cargar la página, por ejemplo, el “1”.

```javascript
self.mostrarconcesionario("1");
```

Con esto ya tenemos el viewmodel listo así que pasamos a la vista

## Creando una vista para el viewmodel

En primer lugar vamos a añadir el esqueleto de la página

```html
<!DOCTYPE html>
<meta charset=utf-8>
<html>

<head>
  <meta charset="utf-8" />
  <title>Prueba knockout</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <script src="js/jquery.js"></script>
  <script src='js/knockout2.0.0.js'></script>
  <script src='js/carsViewModel.js'></script>
</head>

<body>
  <header>
    <h1>Concesionarios</h1>
    <nav>
      <a id="1" href="#">Concesionario 1</a>
      <a id="2" href="#">Concesionario 2</a>
      <a id="3" href="#">Concesionario 3</a>
    </nav>
  </header>
  <section id="content">
    <section id="datos-concesionario">
    </section>
    <section id="coches">
    </section>
  </section>
  <section class="reset-float"></section>
  <footer>
    <p>&amp;copy; Company 2012</p>
  </footer>
</body>

</html>
```

Nada especial hasta aquí. Hemos incluido los scripts de *jquery.js*, *knockout.js* y *carsViewModel.js*. **jquery** y **knockout** tendremos que descargar las últimas versiones de las páginas del producto ([jquery](http://docs.jquery.com/Downloading_jQuery "Descarga de jquery"), [knockout](http://knockoutjs.com/documentation/installation.html "Descarga de knockout")) y añadirlas a la carpeta ***js*** (o referenciarlas de un CDN). El viewmodel *carsVieWModel.js* ya lo tengo en dicha carpeta.

El contenido de la página lo divido en un *header* que contiene la navegación entre los concesionarios, siendo el atributo *id* el identificador del concesionario.

A continuación una sección *section* identificada por *datos-concesionario* para mostrar los datos básicos de cada concesionario seguida por otra sección *coches* para mostrar el array de coches de cada concesionario.

Por último añadimos un footer que no sirve para nada :).

## Enlazando los datos del concesionario

En la sección *datos-concesionario* vamos a añadir unos párrafos cuyo contenido estar á enlazado con algunas de las propiedades de nuestro viewmodel. Se hace de la siguiente forma

```html
<p data-bind="text:id"></p>
<p data-bind="text:concesionario"></p>
<p data-bind="text:cif"></p>                          
```

Se usa el atributo de *HTML5 data* para establecer el enlace (también conocido como *binding*). En primer lugar se indica el tipo de enlace ([*text*](http://knockoutjs.com/documentation/text-binding.html "Enlace text")) y a continuación la propiedad a enlazar (*id, concesionario, cif*). Hay bastantes tipos de enlaces predefinidos. Además podemos crear enlaces personalizados. Para información acerca de las posibilidades de binding y tipos por defecto que trae el script os remito a la [documentación del producto](http://knockoutjs.com/documentation/introduction.html "Documentación Knockout") que es muy buena.

Debajo de los párrafos vamos a añadir un formulario para añádir coches al concesionario

```html
<div>
  <form data-bind="submit:addCoche">
    <label style="display:block" for="marca">Marca</label>
    <input type="text" name="marca" />
    <label style="display:block" for="modelo">Modelo</label>
    <input type="text" name="modelo" />
    <div>
      <label for="color">yellow</label>
      <input type="checkbox" name="color" value="yellow" />
      <label for="color">red</label>
      <input type="checkbox" name="color" value="red" />
      <label for="color">green</label>
      <input type="checkbox" name="color" value="green" />
      <label for="color">blue</label>
      <input type="checkbox" name="color" value="blue" />
      <label for="color">grey</label>
      <input type="checkbox" name="color" value="grey" />
      <label for="color">orange</label>
      <input type="checkbox" name="color" value="orange" />
      <label for="color">black</label>
      <input type="checkbox" name="color" value="black" />
    </div>
    <input type="submit" value="Añadir Coche" />
    <form>
</div>
```

Aquí la novedad es que aplicamos el tipo de enlace [*submit*](http://knockoutjs.com/documentation/submit-binding.html "Enlace submit"), que gestionará la ejecución del evento submit del formulario e invocará a la función *addCoche()* que definimos en el viewmodel. Esto debería añadir un coche nuevo al viewmodel y como vamos a enlazarlo a continuación en la sección *coches*, debería aparecer un nuevo coche representando al viewmodel de manera automática cada vez que hagamos un *submit* al formulario.

La magia de knockout está en que cualquier modificación en los datos del viewmodel se reflejará en los datos enlazados.


## Enlazando los coches

En la sección *coches* vamos a añadir el siguiente código

```html
<div id="contenedor-coches" data-bind="foreach:coches">
  <div class="datos-coche">
    <h2 data-bind="text:marca"></h2>
    <p data-bind="text:modelo"></p>
    <p><a class="btn" href="#">Ver detalle</a></p>
    <div data-bind="foreach:colores">
      <span class="color" data-bind="style: { backgroundColor: color } "></span>
    </div>
    <a href="#" data-bind="click:$parent.removeCoche">Eliminar</a>
  </div>
</div>
<div class="reset-float"></div>
```

En este código hay un par de novedades. La primera es el enlace [*foreach*](http://knockoutjs.com/documentation/foreach-binding.html "Enlace foreach"), que itera sobre el array de coches almacenado en el viewmodel. Más abajo hay otro *foreach* que itera sobre los colores del array de colores de cada coche.

Observad que sólo se añaden una vez los elementos HTML y es **knockout** el que se encarga de generar para cada objeto del viewmodel una representación visual dentro de la sección sobre la que se aplica ya que es precisamente lo que estamos indicando que debe hacer a través del binding *foreach*.

También podemos comprobar que estamos untilizando un enlace de tipo [*style*](http://knockoutjs.com/documentation/style-binding.html "Enlace style") que lo que hace es rellenar los *<span>* (*background-color*) con el color almacenado en el viewmodel.

También hemos incluido un link para eliminar el coche del array de coches del viewmodel. Hemos usado el binding [*click*](http://knockoutjs.com/documentation/click-binding.html "Enlace click") que de manera similar a *submit* enlaza el evento *click* del componente visual con la función *removeCoche()*.

Lo más destacado de este tipo de enlace es el uso de *$parent* que indica que la función está definida **en el padre** del objeto coches, es decir la raiz del viewmodel. Precisamente también tenemos un *$root* que hace referencia **a la raíz** del viewmodel y que podemos usar indistintamente en este caso concreto ya que estamos referenciando lo mismo. También hay un *$data* que indica que se debe buscar en el **contexto de binding sobre el que se trabaja**. Hay algunas otras variables de las que podemos obtener información [aquí](http://knockoutjs.com/documentation/binding-context.html "Los contextos de binding").


## Aplicando el viewmodel sobre la vista

Por último, hay que enlazar todos estos componentes con el viewmodel creado para que se muestren los datos. Eso lo haremos incluyendo un script en el que aplicaremos el modelo de datos. Lo añadiremos al final después del resto de scripts.

```javascript
< script type = "text/javascript" >
  $(document).ready(function() {
    ko.applyBindings(new carsViewModel());
    $("nav a").click(function(e) {
      viewmodel.mostrarconcesionario($(this).attr("id"));
      return false;
    });
  }); <
/script>
```

En este script estamos instanciando nuestro viewmodel y aplicando los datos recuperados sobre los enlaces que hemos hecho en la vista mediante *ko.applyBindings(viewmodel)*.

Además modificamos el concesionario mostrado enlazando el evento *click* de los links de la barra de navegación para que ejecute la función *mostrarconcesionario().*

```javascript
$("nav a").click(function(e) {
    viewmodel.mostrarconcesionario($(this).attr("id"));
    return false;
});
```

## Añadiendo estilos

Vamos a añadir una hoja de estilos sencilla para darle un poco de forma a la aplicación. Creamos una hoja *mystyles.css* en la carpeta **css** e incluimos el siguiente código

```css
body {
  padding: 3%;
}

header {
  width: 100%;
  height: 100px;
  position: relative;
}

header h1 {
  float: left;
  width: 50%;
}

header nav {
  float: left;
  width: 50%;
  position: absolute;
  bottom: 0;
}

#content {
  margin-top: 5%;
}

#datos-concesionario {
  float: left;
  width: 25%;
}

#coches {
  float: left;
  width: 75%;
  border: 2px solid #888888;
  -webkit-box-sizing: border-box;
  -moz-box-sizing: border-box;
  box-sizing: border-box;
  -moz-border-radius: 5px;
  border-radius: 5px;
  -moz-box-shadow: 10px 10px 5px #888;
  -webkit-box-shadow: 10px 10px 5px #888;
  box-shadow: 10px 10px 5px #888;
  padding: 2%;
}

#contenedor-coches {
  width: 100%;
}

.datos-coche {
  width: 25%;
  float: left;
}

.reset-float {
  clear: left;
}

.color {
  width: 15px;
  height: 10px;
  margin-left: 2px;
  border: 1px solid #888888;
  display: inline-block;
}
```

con estos estilos, es suficiente …

## ¿Y se puede simplificar un poquito?

Qué os parece si os digo que el viewmodel anterior se puede simplificar hasta quedar la siguiente forma

```javascript
var self = this;
self.mostrarconcesionario = function(id) {
    ko.mapping.fromJS(getModel(id), {}, self);
};
self.removeCoche = function(data) {
    self.coches.remove(data);
};
self.addCoche = function(data) {
    var serializedForm = $(data).serializeArray();
    var newcar = [];
    $.each(serializedForm, function() {
    if (this.name != "color") {
        newcar[this.name] = this.value;
    } else {
        if (!newcar["colores"]) {
        newcar["colores"] = [];
        }
        newcar["colores"].push(new Color(this.value));
    }
    });
    self.coches.push(new Coche(newcar.marca, newcar.modelo, newcar.colores));
};
self.mostrarconcesionario("1");
```


Pues también funciona. La magia aquí la pone …

```javascript
ko.mapping.fromJS(getModel(id),{},self);
```

… que genera un viewmodel mapeando el *JSON* recuperado por *getModel()* que contiene las mismas propiedades que habiamos definido y nos ahorra la creación de las propiedades y su asignación.

Cuando hay un gran número de propiedades a mapear nos puede ahorrar bastante trabajo y errores.

Para que ko tenga acceso a la función *mapping*() hemos de añadir el [plugin correspondiente](http://knockoutjs.com/documentation/plugins-mapping.html "Mapping plugin"). Lo guardaremos en la carpeta ***js*** y lo añadiremos a la cabecera de nuestra página.


## Resumen

Hemos visto como crear una aplicación con **knockout** enlazada a datos y que responde bien a los eventos de usuario, con un código sencillo y limpio. **Knockout** permite hacer muchas más cosas que las vistas aquí. En la documentación del producto está todo muy bien explicado y hay una [serie de tutoriales](http://learn.knockoutjs.com/ "Titoriales Knockout") interactivos que son un excelente recurso de formación. Os invito a probarlos!!.

Por último comentar que he compartido el código de este ejemplo en un [repositorio de Github](https://github.com/yagopv/DemoKnockout "Repositorio DemoKnockout").

Hasta pronto!!



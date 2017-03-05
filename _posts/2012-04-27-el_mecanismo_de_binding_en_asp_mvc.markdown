---
layout: post
title: El mecanismo de binding en ASP MVC
date: '2012-04-27 04:50:48'
tags:
- binding
---


Las capacidades de binding de ASP MVC conforman un potente mecanismo mediante el cual, de manera automática, se obtienen valores para los parámetros de las acciones que se ejecutan buscándolos en la petición que llega del cliente.

En este post vamos a ver cómo funciona el **mecanismo de binding** de ASP MVC por defecto y cómo podemos crear nuestros propios ***binders*** personalizados.  


El ***binding*** es un proceso que trata de emparejar los valores y parámetros enviados desde el cliente con los parámetros de las acciones que se ejecutan en el servidor.

Una vez que el sistema de rutas selecciona una acción para procesar, se analizan los nombres de los parámetros de la acción y el mecanismo de binding trata de encontrar parejas para ellos en los campos de formularios, parámetros de ruta, parámetros en la querystring o bien archivos adjuntos. El orden que se sigue en la búsqueda de estas parejas es el indicado.


## **Binding de acciones con parámetros simples**

En primer lugar vamos a ver cómo sería el binding más sencillo. Esto es, el binding de acciones con parámetros simples.

Una acción con parámetros simples es aquella cuyos tipos **no son objetos complejos** formados por una serie de propiedades. Los parámetros simples serán típicamente parámetros de tipo *int, bool, string*, …etc.

Una acción con parámetros simples podría ser la siguiente:

```language-javascript
public ActionResult RecuperaProducto(int id,  bool recuperacategoriaasociada)
{
      return View(Services.RecuperaProducto(id, recuperacategoriaasociada))
}
```

Esta acción recibe dos parámetros simples, ***id*** y ***recuperacategoriaasociada*** que representarían por ejemplo el identificador del producto a recuperar y un *bool* indicando si se recupera la categoría asociada con el producto o no.

Por tanto se podrían hacer peticiones del siguiente tipo para ejecutar esta acción

```
GET /Producto/RecuperaProducto?id=2&recuperacategoriaasociada=true<
```

y el mecanismo de binding se encargaría de pasar el valor **2** al parámetro ***id*** y *true* a ***recuperacategoriaasociada***.

En este caso hemos pasado los parámetros por **querystring**, pero como comentaba anteriormente, esta no era la única forma de hacerlo, ya que además, los podíamos pasar como campos de formularios, parámetros de ruta o archivos adjuntos. El binding se haría igualmente y de la misma forma si los pasamos por qualquiera de estos otros métodos.

Tal y como está definida nuestra acción de ejemplo *RecuperaProducto()*, si omitimos alguno de los parámetros se produciría un error que nos indicaría que no se permiten nulos en los parámetros definidos. La solución es sencilla, lo que podemos hacer es definir los parámetros de manera que puedan aceptar nulos o bien inicializarlos con un valor por defecto.

```language-javascript
public ActionResult RecuperaProducto(int id=1, bool? recuperacategoriaasociada)
```

Tal y como hemos definido ahora la acción, el parámetro ***id*** recuperaría siempre el producto con ***id = 1*** en el caso de que no se enviara ninguno en la petición y además ***recuperacategoriaasociada*** puede no estar incluido en la petición ya que lo hemos definido como *nullable* mediante *bool?*.

También existe otra posibilidad, utilizar el atributo *DefaultParameterValue* aunque el resultado sería el mismo que utilizando la asignación de un valor por defecto como veíamos en el ejemplo anterior en el caso del parámetro ***id.***

```language-javascript
public ActionResult RecuperaProducto([DefaultParameterValue(1)]int id, bool? recuperacategoriaasociada)
```

El mecanismo de binding está haciendo un gran trabajo por nosotros y como se puede apreciar incluso está convirtiendo los valores a los tipos de los parámetros de manera automática ya que en la petición del cliente son todo cadenas de texto.

Por supuesto, los valores se podrían obtener directamente de la petición (accediendo a los objetos Request, RouteData, etc.) pero esto haría que tuviésemos que hacer un trabajo extra, repetitivo y propenso a errores que el mecanismo de binding nos está precisamente evitando.


## **Binding de acciones con parámetros complejos**

Además de la capacidad de enlazar parámetros simples, también podemos hacerlo con objetos.

```language-javascript
public JsonResult GuardarProducto(Producto producto)
{
            Services.GuardarProducto(producto);
            return Json(true);
}
```

Cuyo parámetro es un objeto de la clase

```language-javascript
public class Producto
{
    public  int  Id { get; set; }
    public  string  Nombre { get; set; }
    public  string  Descripcion { get; set; }
}
```

En este caso, el sistema de binding analiza el tipo del parámetro, *Producto*, y recorre sus propiedades (*Id, Nombre y Descripción*) asignándole valores en caso de encontrarlos en (como venimos comentando desde el inicio y en el mismo orden) los campos de formularios, parámetros de ruta, parámetros de la petición o archivos adjuntos. 

Cuando encuentra una coincidencia en alguno de los nombres, instancia un objeto del tipo del parámetro y añade los valores encontrados a las propiedades del mismo. Si no encuentra nada, el parámetro estará a *null.*

El siguiente formulario haría una petición contra nuestra acción *GuardarProducto* proporcionandole los valores necesarios para que el sistema de binding instancie un objeto de tipo *Producto *y haga el enlace de manera automática.

```language-javascript
<form method="post" action="Productos/GuardarProducto">
    <label for="nombre">Nombre:</label>
    <input  type="text" name="nombre"  />
    <label for="descripcion">Descripcion:</label>
    <input  type="text" name="descripcion"  />
    <input  type="hidden" name="id" value="5"  />
    <input type="submit" value="Guardar" />
</form>
```

En este ejemplo hemos creado un formulario pero el paso de valores se podría hacer hecho mediante parámetros de ruta o en un querystring, es indiferente.


## Binding de acciones con parámetros complejos que contienen propiedades complejas

Por supuesto también se permitirá el enlace de objetos un poco más complicados y que puedan contener otros objetos en su interior. Añadamos a nuestra clase de ejemplo *Producto, una clase de *Caracteristicas*.

```language-javascript
public class Producto
{
    public  int  Id { get; set; }
    public  string  Nombre { get; set; }
    public  string  Descripcion { get; set; }
    public Caracteristicas Caracteristicas {get; set;}
}

public class Caracteristicas
{
    public  string  LugarFabricacion { get; set; }
    public  int   AnhoFabricacion { get; set; }
    public  int  Peso { get; set; } 
}
```

En este caso, el sistema de binding tratará igualmente de enlazar las propiedades de *Caracteristicas* buscando por convención en la petición los parámetros *Caracteristicas.LugarFabricacion*,  *Caracteristicas.AnhoFabricacion* y *Caracteristicas.Peso*. En caso de encontrar alguno, instanciará un objeto de tipo *Caracteristicas* y rellenará sus propiedades con los valores obtenidos.

Vamos a modificar el formulario de ejemplo que habíamos usado para el caso anterior y añadimos las *Caracteristicas* del *Producto*

```language-javascript
<form method="post" action="Productos/GuardarProducto">
    <label for="nombre">Nombre:</label>
    <input  type="text" name="nombre"  />
    <label for="descripcion">Descripcion:</label>
    <input  type="text" name="descripcion"  />
    <input  type="hidden" name="id" value="5"  />
    <label for="caracteristicas.LugarFabricacion">Lugar de fabricacion:</label>
    <input  type="text" name="Caracteristicas.LugarFabricacion"  />
    <label for="caracteristicas.AnhoFabricacion">Año de fabricacion:</label>
    <input  type="text" name="Caracteristicas.AnhoFabricacion" />
    <label for="caracteristicas.Peso">Peso:</label>
    <input  type="text" name="Caracteristicas.Peso"  />
    <input type="submit" value="Guardar" />
</form>
```

Añadimos las *Caracteristicas* del *Producto* de manera que al enviar el formulario se instanciará un objeto Producto y un objeto *Caracteristicas* y enlazará sus propiedades con las que lleguen en la petición.


## **Binding de acciones con parámetros de tipo lista**

Además de las opciones anteriores también es posible enlazar automáticamente listas de objetos, ya sean listas genéricas `List<T>` o arrays

Por ejemplo, si queremos guardar en lugar de un *Producto*, una `List<Producto>`, tendríamos una accion de este tipo

```language-javascript
public JsonResult GuardarProductos(List<Producto> productos)
{
            Services.GuardarProductos(productos);
            return Json(true);
}
```

En este caso, podríamos desde el cliente y usando un poco más de código enlazar automáticamente los productos que se envíen en la petición para que se cree una `List<producto>` de forma automática.

El formulario sería algo así

```language-javascript
<form method="post" action="Productos/GuardarProductos">
    <input  type="text" name="producto[0].nombre"  />
    <input  type="text" name="producto[0].descripcion"  />
    <input  type="text" name="producto[1].nombre"  />
    <input  type="text" name="producto[1].descripcion"  />
    <input  type="text" name="producto[2].nombre"  />
    <input  type="text" name="producto[3].descripcion"  />
...
    <input  type="text" name="producto[n].nombre"  />
    <input  type="text" name="producto[n].descripcion"  />
    <input type="submit" value="Guardar" />
</form>
```

Con este sencillo código, estaríamos indicando al sistema de binding que tiene que instanciar una *
`List<Producto>` y además **N** *Producto* con los que rellenarla de datos.

Además de listas genéricas, comentaba que también se pueden a enlazar **arrays**.

Es muy típico querer enlazar una lista de *checkboxes* de manera que sólo se envíen al servidor aquellas que se hayan activado. Por ejemplo, podríamos querer enlazar mediante un formulario una serie de productos que ha seleccionado un usuario entre todos los posibles.

<form method="post" action="Productos/SeleccionaProductos">
<input type="checkbox" name="idproducto" value="1" />
<input type="checkbox" name="idproducto" value="2" />
<input type="checkbox" name="idproducto" value="3" />
<input type="checkbox" name="idproducto" value="4" />
<input type="checkbox" name="idproducto" value="5" />
...
<input type="checkbox" name="idproducto" value="n" />
<input  type="hidden" name="idusuario" value="1"  />
<input type="submit" value="Guardar Seleccion" />
</form>

Este formulario lo podríamos enlazar de manera sencilla mediante la siguiente accion

```language-javascript
public JsonResult GuardarProductos(int[] idproducto, int  idusuario)
{
            Services.EnlazaProductos(idproducto, idusuario );
            return Json(true);
}
```

El array de enteros *idproducto* contendrá los *id* de cada producto que se haya marcado en el formulario y que están representados por el *value* de cada `<input>`.

Este es un caso especial para enlazar *checkboxes*, pero si lo que quiero es enlazar cualquier otro tipo de `<input>` como por ejemplo un *string[]* frutas, lo único que hay que hacer es incluir los `<input>` de type *text* cuyo *name* sea siempre frutas. Con esto tendría todas las frutas en la acción del servidor que se encargara de procesar la petición. Esto por supuesto lo puedo hacer mediante un formulario o concatenando en un querystring las *frutas*.


## **Binding de archivos**

Otro tipo de datos que se puede enlazar automáticamente y que es de mucha utilidad son ficheros.

Dado un formulario para subir un fichero

```language-javascript
<form method="post" action="Productos/GuardaFichero" enctype="multipart/form-data">
           <input  type="file" name="ficheroaguardar" />
           <input type="submit" value="Guardar Archivo" />
</form>
```

Podemos enlazarlo y salvarlo de manera sencilla mediante una acción como esta

```language-javascript
public ActionResult GuardarFichero(HttpPostedFileBase ficheroaguardar)
{
    Ficheroaguardar.SaveAs(rutadondequieroguardarlo);
}
```

El fichero a de ser un parámetro de tipo *HttpPostedFileBase* y el nombre del parámetro, como siempre, el mismo que el name en el formulario. Después para guardarlo en el sistema de archivos del servidor, uso el método *SaveAs* de *HttpPostedFileBase* y le paso la ruta donde quiero almacenarlo.


## **Binding personalizado**

En los párrafos anteriores hemos repasado las posibilidades que nos ofrece ASP MVC para enlazar de manera automática nuestros parámetros en las peticiones al servidor con nuestros parámetros en las acciones de los controladores.

A partir de aquí vamos a ver las posibilidades que el Framework nos da para enlazar todo esto de una manera un poco más customizada. Como siempre, ASP MVC nos deja personalizar a nuestro gusto una característica más del Framework como es el sistema de binding.

### ***El Atributo [Bind]***

El atributo Bind se puede aplicar sobre los parámetros de una acción para modificar el comportamiento por defecto del sistema de binding. Este atributo acepta una lista de opciones entre las que se encuentran

***Preffix***

Con *Prefix* puedo indicar al mecanismo de binding que los nombres de los parámetros se encuentran en la petición prefijados con un nombre concreto.

Por ejemplo podríamos modificar nuestro primer ejemplo del post para indicar que

```language-javascript
public JsonResult GuardarProducto([Bind(Preffix="prd")]Producto producto)
{
            Services.GuardarProducto(producto);
            return Json(true);
}
```

Estaríamos indicando que el producto se encontrará en la petición con sus propiedades prefijadas con ***prd***. Por tanto el formulario sería como algo como

```language-javascript
<form method="post" action="Productos/GuardarProducto">
    <label for="prd.nombre">Nombre:</label>
    <input  type="text" name="prd.nombre"  />
    <label for="prd.descripcion">Descripcion:</label>
    <input  type="text" name="prd.descripcion"  />
    <input  type="hidden" name="prd.id" value="5"  />
    <input type="submit" value="Guardar" />
</form>
```

***Include***

Permite definir las propiedades que se quieren enlazar. Con este parámetro del atributo *Bind* puedo evitar que se enlacen propiedades que no estoy interesado en tratar y que podrían encontrarse en el contexto de la petición. Sería por ejemplo típico excluir las claves de las propiedades que se quieren enlazar como mecanismo de seguridad ante posibles ataques. Si quiesiéramos excluir en el ejemplo anterior la propiedad *id*, lo haríamos incluyendo el resto de propiedades

```language-javascript
public JsonResult GuardarProducto([Bind(Include="Nombre, Descripcion")]Producto producto)
{
            Services.GuardarProducto(producto);
            return Json(true);
}
```

Con esta sintaxis en la acción *GuardarProducto* no se enlazaría la propiedad *id* aunque se estuviese enviando.

***Exclude***<

Como contrapunto a *Include* tenemos *Exclude*, que evita el enlace de las propiedades que se indiquen como parámetro. Por tanto otra manera de excluir la propiedad *id* sería

```language-javascript
public JsonResult GuardarProducto([Bind(Exclude="Id")]Producto producto)
{
            Services.GuardarProducto(producto);
            return Json(true);
}
```

### ***Creación de Binders personalizados***

También es posible personalizar el binding, y ahora ya proporcionando control total, mediante la creación de una clase que herede de *DefaultModelBinder*

Por ejemplo, podríamos incluir un campo en nuestra clase *Producto* que concatenase el resto de campos y los separase con “;”. Vamos a llamar a la nueva propiedad *ProductoCompleto*

```language-javascript
public class Producto
{
    public  int  Id { get; set; }
    public  string  Nombre { get; set; }
    public  string  Descripcion { get; set; }
    public string ProductoCompleto { get; set; }
}
```

La clase que haría el bind personalizado sería algo como lo siguiente

```language-javascript
public class ProductoBinder : DefaultModelBinder
{
    public override object BindModel(ControllerContext controllercontext, ModelBindingContext bindingContext)
   {
           //Lanzamos el binder por defecto para que se enlacen las propiedades que se encuentran en el contexto de la peticion
            Producto producto = (Producto)base.BindModel(controllercontext, bindingcontext);
 
try {
            // Obtenemos el Value Provider que se encarga de extraer los valores de la petición
            IValueProvider valueprovider = bindingcontext.ValueProvider;

           //Concatenamos los valores que se han enlazado en la nueva propiedad
           producto.ProductoCompleto = valueprovider.GetValue("id") + ";" +
           producto.ProductoCompleto = valueprovider.GetValue("nombre") + ";" +
           producto.ProductoCompleto = valueprovider.GetValue("descripcion");
 
            //retornamos el producto ya con todas las propiedades informadas
}
Catch (Exception ex)
{
            producto.ProductoCompleto = null;
}
            return producto          
   }
}
```

En los comentarios del snippet se explica paso a paso lo que se hace para obtener la propiedad *ProductoCompleto*. Básicamente hay que sobreescribir el método *BindModel()* que se encarga de ejecutar el bind por defecto, extraer los valores, concatenarlos y devolver el producto para que la accion del controlador lo procese.

Ahora usaremos la nueva clase en la acción de *GuardarProducto,*

public JsonResult GuardarProducto([ModelBinder(typeof(ProductoBinder))]Producto producto)
{
            Services.GuardarProducto(producto);
            return Json(true);
}



también podríamos usarla a nivel de entidad,

```language-javascript
[ModelBinder(typeof(ProductoBinder))]
public class Producto
{
    public  int  Id { get; set; }
    public  string  Nombre { get; set; }
    public  string  Descripcion { get; set; }
    public string ProductoCompleto { get; set; }
}
```

o bien a nivel de aplicación en el *Global_asax,*

```language-javascript
protected void Application_Start()
{
   ...
            ModelBinders.Binders.Add(typeof(Producto), new ProductoBinder());
   ...
}
```

dependiendo del alcance que quiero que tenga el binder personalizado


## **Control del binding**

Hasta ahora hemos visto todas las posibilidades que hay para enlazar parámetros. Como último caso de uso vamos a ver cómo controlar cuándo se enlazan dichos parámetros y cuales.

Si eliminamos los parámetros del método de acción, obviamente no se realizará ningún enlace de los mismos. En el cuerpo del método puedo usar dos métodos definidos a nivel de controlador *TryUpdateModel* y *UpdateModel* que me permiten hacer el bind de forma explícita. ¿Por qué estaría interesado en hacer esto? Bien, uno de los casos más comunes es la necesidad de recuperar los datos de la base de datos antes de hacer el enlace para así actualizar sólo aquellas propiedades que me interesa modificar.

La única diferencia entre los métodos *TryUpdateModel* y *UpdateModel* reside en que *TryUpdateModel* devuelve *true* o *false* dependiendo de si el enlace ha acabado con éxito o no y *UpdateModel* lanza una excepción si se encuentra con un error en el enlace.

A ambos métodos se les pueden pasar una serie de parámetros que permiten hacer lo mismo que vimos hasta ahora para personalizar el binding, pasándole propiedades a incluir, a excluir … etc.

```language-javascript
public JsonResult GuardarProducto(int idproducto, FormCollection propiedades)
{
            Producto producto = Services.ObtenerProducto(idproducto);
            var propiedadesaincluir = new[] {"Nombre","Descripcion" };
            if (TryUpdateModel(producto,propiedadesaincluir,propiedades)
             {
                  Services.GuardarProducto(producto);
}
            return Json(true);
}
```

Con el código anterior estoy recuperando el producto de la base de datos y estoy actualizando las propiedades *Nombre* y *Descripcion* del producto recuperado según indico en la lista de inclusión propiedades a incluir y mediante los datos contenidos en* propiedades.


## Resumen

Hemos visto las posibilidades que nos ofrece el mecanismo de binding de ASP MVC. Podemos enlazar de forma automática multitud de tipos del Framework, tipos primitivos, objetos de creados por nosotros, listas, arrays. También podemos personalizar la forma en que se realiza el enlace con atributos ya definidos o creando los nuestros. Por último hemos visto que también podemos controlar cuándo se hace el binding.

Hasta pronto!!


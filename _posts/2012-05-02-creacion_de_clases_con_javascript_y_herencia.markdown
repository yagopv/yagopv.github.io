---
layout: post
title: Creación de clases con javascript y herencia
date: '2012-05-02 09:50:52'
tags:
- poo
---


En javascript es posible crear clases como en cualquier otro lenguaje orientado a objetos pero la forma de hacerlo difiere en gran medida del resto de las plataformas orientadas a objetos como .NET o Java.

## Creación de clases

En javascript es posible crear clases como en cualquier otro lenguaje orientado a objetos pero la forma de hacerlo, difiere en gran medida del resto de las plataformas orientadas a objetos como .NET o Java.

Además de la creación de clases con sus propiedades y métodos también se puede utilizar la herencia entre dichas clases aunque de una forma muy distinta que en otros lenguajes orientados a objetos a los que estemos acostumbrados.

Las funciones javascript, entre otras muchas características, se pueden instanciar. Por esta razón son el candidato perfecto para crear una clase.

Podemos crear una clase **Animal** de la siguiente forma

{% highlight javascript %}
function Animal() { }
{% endhighlight %}

Lo primero en lo que pensamos cuando definimos una clase en cualquier lenguaje orientado a objetos es en cual será el constructor de la clase. Este es otro punto más en el que la definición de una clase en javascript difiere de lo acostumbrado en cualquier otra plataforma, ya que no crearemos un método como constructor, sino que lo que haremos es pasarle una serie de parámetros a la función y asignarlos inmediatamente después en el cuerpo de la misma.

{% highlight javascript %}
function Animal(nombre, especie, numerodepatas, tienecola) { 
    //Propiedades
    this.Nombre = nombre;
    this.Especie = especie;
    this.NumeroDePatas = numerodepatas;
    this.TieneCola = tienecola;
}
{% endhighlight %}

En el código anterior vemos que hemos pasado una serie de parámetros a la función y hemos asignado sus valores a propiedades del objeto ***this***, ***this*** en este ámbito hace referencia a nuestra función y ¿qué deciamos que era nuestra función?, pues será nuestra clase, por lo tanto al asignar los parámetros utilizando ***this.Propiedad*** estamos asignando valores a las propiedades de la clase **Animal**.

Cuando instanciamos una función, se ejecuta todo el código que hay en su interior exceptuando otras funciones contenidas, que sólo se ejecutarán si las llamamos de forma explícita. Por tanto si instanciamos a nuestra clase **Animal.**

{% highlight javascript %}
var animal = new Animal("Tigre","Felino", 4, true);
{% endhighlight %}

lo que en el fondo estamos haciendo es ejecutar el contenido de la clase **Animal** exceptuando otras funciones contenidas en su interior. Por tanto **hemos creado un constructor para la clase**.

Podemos también crear variables privadas para dicha clase, que sólo sean accesibles desde esta. Vamos a añadir una variable *_nombreCompleto* y otra *_color*

{% highlight javascript %}
function Animal(nombre, especie, numerodepatas, tienecola) {
    //Variables privadas 
    var _nombreCompleto; 
    var _color; 
    // Propiedades 
    this.Nombre = nombre; 
    this.Especie = especie; 
    this.NumeroDePatas = numerodepatas; 
    this.TieneCola = tienecola; 
}
{% endhighlight %}

De manera que las variables definidas en el interior de la clase sólo son accesibles desde el interior. Con esto tengo definido un atributo que puedo usar para operaciones internas de la clase. Vemos que lo he definido con **“_”** porque por convención suele hacerse así para *variables privadas*.

Por último nos falta definir métodos. Vamos a definir un par para manipular el nombre completo y el color.

{% highlight javascript %}
function Animal(nombre, especie, numerodepatas, tienecola) { 
    //Variables privadas 
    var _nombreCompleto; var _color; 
    // Propiedades 
    this.Nombre = nombre; 
    this.Especie = especie; 
    this.NumeroDePatas = numerodepatas; 
    this.TieneCola = tienecola; 
    // Metodos 
    this.setColor = function (color) { 
        _color = color; 
    } 
    this.getColor = function () { 
        return _color; 
    } 
    this.setNombreCompleto = function() { 
        _nombreCompleto = "Este Animal es un " + this.Nombre + " de la especie de los " + this.Especie; 
    } 
    this.getNombreCompleto = function() { 
        this.setNombreCompleto(); 
        return _nombreCompleto; 
    } 
}
{% endhighlight %}

***setColor()*** nos permite modificar el color de nuestro animal. Recibe un parámetro, el color.

***getColor()*** recupera el color del animal.

***setNombreCompleto()*** establece el nombre complete el animal concatenando el ***Nombre*** y la ***Especie***.

***getNombreCompleto()***recupera el nombre completo.


## Herencia

Ahora que tengo definida mi clase **Animal** genérica, voy a derivarla en dos clases hijas, **Vaca** y **Tigre**. En primer lugar vamos a mostrar el código y a continuación lo explicamos.

{% highlight javascript %}
function Vaca(nombre, especie, numerodepatas, tienecola, lecherestante) {   
    Animal.call(this,nombre, especie, numerodepatas, tienecola); 
    this.LecheRestante = lecherestante; 
    //Metodos 
    this.ordenhar = function (litros) { 
        this.LecheRestante -= litros; 
    } 
} 
Vaca.prototype = new Animal(); 

function Tigre(nombre, especie, numerodepatas, tienecola, victimas) {
    Animal.call(this,nombre, especie, numerodepatas, tienecola); 
    this.Victimas = victimas; 
    // Metodos 
    this.comerpersonas = function (victimas) { 
        this.Victimas += victimas; 
    } 
} 
Tigre.prototype = new Animal();
{% endhighlight %}

Para llevar a cabo la herencia he hecho principalmente dos cosas:

1. Añadir a los parámetros formales de cada clase derivada las propiedades de la clase hija a mayores de las de la clase padre, ***lecherestante*** en el caso de la **Vaca** y ***victimas*** en el caso del **Tigre**. Como primera linea de la función, mediante el método *call()* llamo al constructor de la superclase **Animal** pasandole ***this*** y los parámetros formales de la funcion. De esta forma construiré un objeto con todas las propiedades de la superclase y las de la clase hija.
2. Como segundo paso, y en definitiva “el truco” que permite realizar la herencia, es crear una objeto de tipo **Animal** en el ***prototype*** de la clase **Vaca** y **Tigre**.

Sobre el ***prototype*** de una clase (o función) se permite adjuntar objetos que sean comunes a todas las instancias de dicha clase (o función), por lo que al decir que el ***prototype*** de **Vaca** es un **new Animal()**, puedo acceder a todas las propiedades de **Animal** desde cualquier instancia de **Vaca** debido a que cuando accedo a una propiedad de una clase, si no está definida, se mira en el ***prototype*** de la misma para ver si está definida allí. Si el ***protototype*** no es *null* y tampoco encuentra la propiedad, se pasa a mirar en el ***prototype*** del ***prototype***, y así hasta que encuentra uno a null.

En definitiva el ***prototype*** de un objeto es una propiedad común a todos los objetos que se crean de una clase determinada. Es por tanto el lugar donde se ha de establecer la herencia.

Además, creamos una propiedad ***LecheRestante*** y una ***Victimas***en las **Vacas** y los **Tigres** respectivamente.

Como las vacas dan mucha leche voy a crear un método ***ordenhar() ***para quitarle un poco de vez en cuando y como los tigres son bastante peligrosos voy a añadirles un método que sea ***comerpersona() *** para cuando tengan hambre, que tengan como parámetro en número de personas que se hayan comido.


## **Probando nuestras clases**

En definiva, a esta alturas tengo ya una superclase **Animal** y dos clases hijas **Vaca** y **Tigre**. Ahora vamos a crear una página html que demuestre el correcto funcionamiento de las clase y de la herencia entre ellas.

Creo una página web *test_inheritance.html* e incluyo en el mismo directorio una carpeta Scripts que contenga las tres clases aquí creadas además de a nuestro amigo jquery.

La página creada quedaría así

{% highlight html %}
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8" />
        <title>Ejemplo de definicion de clases y herencia</title>
        <meta name="author" content="Yago Perez Vazquez">        
    </head>
  <body>  
    <script src="Scripts/jquery.js"></script>
	<script src="Scripts/Animal.js"></script>	
	<script src="Scripts/Vaca.js"></script>	
	<script src="Scripts/Tigre.js"></script>	
    <script type="text/javascript">
        $(document).ready(function () {
			// Creo una vaca y la orde&ntilde;o
			var animal = new Vaca("Antonia", "Rumiantes", 4, true, 50);
			$("body").append($("<p></p>").html("Creo la vaca ...:" + JSON.stringify(animal)));
			animal.ordenhar(20);
			$("body").append($("<p></p>").html("La ordenho 20 litros asi que la vaca queda ...:" + JSON.stringify(animal)));
			$("body").append($("<p></p>").html("Como puedo comprobar he creado una vaca llamando al constructor de la superclase").css("color","green"));
			$("body").append($("<p></p>").html("... y le he quitado 20 litros de leche").css("color","green"));
			
			//Recupero el nombre completo de la vaca
			$("body").append($("<p></p>").html("El nombre completo de la vaca es ...: " + animal.getNombreCompleto()));
			$("body").append($("<p></p>").html("He recuperado el nombre completo, esto demuestra que puedo acceder a los métodos de la superclase").css("color","green"));
			
			//Modifico el color de la vaca
			animal.setColor("Blanca y Negra");
			$("body").append($("<p></p>").html("El color de la vaca es ...: " + animal.getColor()));
			$("body").append($("<p></p>").html("He cambiado el color de la vaca accediendo tambien a metodos de la superclase").css("color","green"));			
			$("body").append($("<p></p>").html("Ademas se ve como mantengo el estado de las variables privadas").css("color","green"));			
			
			//Creo un tigre
			var animal2 = new Tigre("Pedro", "Felinos", 4, true, 0);
			$("body").append($("<p></p>").html("Creo el Tigre ...:" + JSON.stringify(animal2)));
			
			//El tigre se come a tres personas
			animal2.comerpersonas(3);
			$("body").append($("<p></p>").html("El numero de victimas del Tigre es ...: " + animal2.Victimas).css("color","green"));
			$("body").append($("<p></p>").html("He creado un Tigre que se ha comido a tres personas").css("color","green"));			
			
        });
	</script>
  </body>
</html>
{% endhighlight %}

Como podemos observar lo que estoy haciendo es crear objetos de las clases hijas y comprobando que tengo acceso a las propiedades y métodos tanto de la clase padre como de las clases hijas.

La salida sería algo así

<p><em></em>Creo la vaca ...:{"Nombre":"Antonia","Especie":"Rumiantes","NumeroDePatas":4,"TieneCola":true,"LecheRestante":50}</p>
<p>La ordenho 20 litros asi que la vaca queda ...:{"Nombre":"Antonia","Especie":"Rumiantes","NumeroDePatas":4,"TieneCola":true,"LecheRestante":30}</p>
<p style="color: green;">Como puedo comprobar he creado una vaca llamando al constructor de la superclase</p>
<p style="color: green;">... y le he quitado 20 litros de leche</p>
<p>El nombre completo de la vaca es ...: Este Animal es un Antonia de la especie de los Rumiantes</p>
<p style="color: green;">He recuperado el nombre completo, esto demuestra que puedo acceder a los metodos de la superclase</p>
<p>El color de la vaca es ...: Blanca y Negra</p>
<p style="color: green;">He cambiado el color de la vaca accediendo tambien a metodos de la superclase</p>
<p style="color: green;">Ademas se ve como mantengo el estado de las variables privadas</p>
<p>Creo el Tigre ...:{"Nombre":"Pedro","Especie":"Felinos","NumeroDePatas":4,"TieneCola":true,"Victimas":0}</p>
<p style="color: green;">El numero de victimas del Tigre es ...: 3</p>
<p style="color: green;">He creado un Tigre que se ha comido a tres personas</p>


## Resumen

Hemos visto cómo pueden crearse clases y establecer mecanismos de herencia entre ellas de una manera relativamente sencilla y de este modo ordenar un poco nuestro código javascript y equiparar su funcionamiento al de otro lenguaje orientado a objetos cualquiera.

Hasta pronto!!



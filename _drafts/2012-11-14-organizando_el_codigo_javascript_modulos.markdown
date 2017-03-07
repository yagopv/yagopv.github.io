---
layout: post
title: Organizando el código javascript. Módulos
date: '2012-11-14 05:00:01'
tags:
- patrones
---


Vamos a ver una forma de organizar nuestro código javascript en Módulos a través de un sencillo ejemplo.

El código javascript que escribimos muchas veces se nos puede ir de la manos a medida que nuestra aplicación crece, convirtiéndose en difícil de mantener. Existen muchas posibilidades para realizar una mejor gestión del código como podemos ver en algunas páginas como [esta](http://www.jspatterns.com/ "jspatterns"), o bien [este repositorio de patrones javascript](https://github.com/shichuan/javascript-patterns "javascript patterns") en Github.

En este artículo, vamos a comentar una posibilidad más, ni mejor ni peor que otras, que tendrá sus ventajas y su inconvenientes. Para verlo, vamos a crear un módulo de Contabilidad sencillo.

Comenzamos … para empezar definiremos una clase auxiliar que usaremos en el módulo de Contabilidad, la clase *Cuenta*

{% highlight javascript %}
function Cuenta(numerocuenta, saldo) {
    this.numerocuenta = numerocuenta;
    this.saldo = saldo;
};
{% endhighlight %}

Con esta clase podemos crear cuentas para usar en el módulo. Una nueva *Cuenta *tendrá dos propiedades públicas, el número de cuenta y el saldo.

Una vez tenemos esta clase definida vamos con la creación de nuestro módulo de Contabilidad. La estructura sería algo así:

{% highlight javascript %}
var Contabilidad = function () { 
    //variables y métodos privados 
    return { 
        // Métodos públicos 
    }
}();
{% endhighlight %}

Definimos *Contabilidad* como una función auto ejecutable, es decir, se instancia automáticamente, esto lo conseguimos mediante los “()” que añadimos al final. Por tanto cuando se procesa nuestro script en la página *html* en la que lo incluyamos, ya tendremos disponible un objeto *Contabilidad.*

Como se puede apeciar en los comentarios del código, hay una parte para añadir métodos y variables privadas, y otra, en el interior del *return* en la que añadiré aquellos métodos que quiero exponer al exterior, los métodos públicos.

En primer lugar, vamos a definir las variables privadas que vamos a usar y algún método que haga uso de ellas.

{% highlight javascript %}
var Contabilidad = function () {
    //variables y métodos privados
    var _cuentas,
        _saldototal,
        _Cuentas = function () { return _cuentas },
        _SaldoTotal = function () { return _saldototal }
    return {
        // Métodos públicos
        Saldo: _SaldoTotal,
        Cuentas: _Cuentas
    }
}();
{% endhighlight %}

</div>definimos *_cuentas y _saldototal *como variables privadas para soportar el conjunto de cuentas (será un array de cuentas) y el saldo total que sumen en total los saldos individuales. Además exponemos dos métodos, *Saldo y Cuentas *como métodos públicos, que invocarán a los métodos privados, _SaldoTotal y _Cuentas, con el objetivo de recuperar los valores de las variables privadas definidas (son getters).

A continuación, añadimos un método *init* a modo de constructor que nos servirá para poblar las variables privadas con datos y para inicializar el módulo.

{% highlight javascript %}
var Contabilidad = function () {
    var _cuentas,
        _saldototal,

   _init = function (cuentas, saldototal) {
       _cuentas = cuentas || new Array();
       _saldototal = 0;
   },

   _Cuentas = function () { return _cuentas },
   _SaldoTotal = function () { return _saldototal }

    return {
    // Métodos públicos
        init: _init,
        Saldo: _SaldoTotal,
        Cuentas: _Cuentas
    }
}();
{% endhighlight %}

Además lo exponemos como público de la misma forma que anteriormente lo habíamos hecho.

Por último vamos a incluir los métodos neccesarios para la operativa del módulo de Contabilidad, es decir, añadir, borrar, modificar saldos y calcular el saldo total …

{% highlight javascript %}
var Contabilidad = function () {
    var _cuentas,
        _saldototal,

   _init = function (cuentas, saldototal) {
       _cuentas = cuentas || new Array();
       _saldototal = 0;
   },

   _nuevaCuenta = function (numerocuenta, saldo) {
       _cuentas.push(new Cuenta(numerocuenta, saldo));
   },
   _eliminarCuenta = function (numerocuenta) {
       _borrar(_cuentas, numerocuenta);
   },
   _borrar = function (cuentas, numerocuenta) {
       var j = 0;
       while (j < cuentas.length) {
           if (cuentas[j].numerocuenta == numerocuenta) {
               cuentas.splice(j, 1);
               return;
           } else { j++; }
       }
   },
   _modificarSaldo = function (numerocuenta, saldo) {
       _modificar(_cuentas, numerocuenta, saldo);
   },
   _modificar = function (cuentas, numerocuenta, saldo) {
       var j = 0;
       while (j < cuentas.length) {
           if (cuentas[j].numerocuenta == numerocuenta) {
               cuentas[j].saldo += saldo;
               return;
           } else { j++; }
       }
   },
   _Cuentas = function () { return _cuentas },
   _SaldoTotal = function () { return _saldototal },
   _calcularSaldoTotal = function () {
       var j = 0;
       _saldototal = 0;
       while (j < _cuentas.length) {
           _saldototal += _cuentas[j].saldo;
           j++;
       }
   }

    return {
    // Métodos públicos
        init: _init,
        nuevaCuenta: _nuevaCuenta,
        eliminarCuenta: _eliminarCuenta,
        modificarSaldo: _modificarSaldo,
        calcularSaldoTotal: _calcularSaldoTotal,
        Saldo: _SaldoTotal,
        Cuentas: _Cuentas
    }
}();
{% endhighlight %}

Ahora que ya tenemos nuestro módulo completo, podemos probarlo incluyéndolo en una página *html* cualquiera y viendo que podemos usarlo con instrucciones como ...

{% highlight javascript %}
Contabilidad.init(new Array(), 0); Contabilidad.nuevaCuenta(111111, 100.00); Contabilidad.nuevaCuenta(222222, 200.00); Contabilidad.modificarSaldo(111111, 100.00); Contabilidad.eliminarCuenta(111111); Contabilidad.calcularSaldoTotal();
{% endhighlight %}

Observad que lo primero que hacemos es inicializar nuestro módulo mediante *init()* y a continuación añadimos cuentas, modificamos los saldos, eliminamos y calculamos el saldo total.

Pues ya está ... la única pega que podríamos tener es que este tipo de módulos se instancian automáticamente y no nos permiten crear más módulos de contabilidad en la misma página. Este esquema sigue por tanto el patrón Singleton. En ocasiones será el comportamiento deseado y en otras no. Podemos modificar el módulo para que no se instancie automáticamente simplemente eliminando los "();" del final. Al hacer esto ya podríamos obtener múltiples instancias del módulo de la siguiente forma:

{% highlight javascript %}
var contabilidad = Contabilidad(); contabilidad.init(new Array(), 0); contabilidad.nuevaCuenta(111111, 100.00); contabilidad.nuevaCuenta(222222, 200.00); contabilidad.modificarSaldo(111111, 100.00); contabilidad.eliminarCuenta(111111); contabilidad.calcularSaldoTotal(); var contabilidad2 = Contabilidad(); contabilidad2.init(new Array(), 0); contabilidad2.nuevaCuenta(111111, 100.00); contabilidad2.nuevaCuenta(222222, 200.00); contabilidad2.nuevaCuenta(333333, 200.00); contabilidad2.nuevaCuenta(444444, 200.00); contabilidad2.modificarSaldo(111111, 1000.00); contabilidad2.eliminarCuenta(333333); contabilidad2.calcularSaldoTotal(); var contabilidad3 = Contabilidad(); contabilidad3.init(new Array(new Cuenta(111111, 100.00), new Cuenta(222222, 200.00)), 0); contabilidad3.nuevaCuenta(333333, 100.00); contabilidad3.calcularSaldoTotal();
{% endhighlight %}

Bueno, pues hastá aquí esta artículo ... espero que ayude ...

Hasta pronto!!

<div class="line"></div><div><span class="p">  
</span></div>



---
layout: post
title: Organizando el código javascript. Prototype
date: '2012-11-15 05:38:32'
tags:
- patrones
---


En un post anterior vimos como organizar el código usando módulos.

En este artículo veremos cómo organizarlo usando el prototype con el mismo ejemplo.

En un [post anterior](http://www.yagoperez.com/Post/GetPostByCode/organizando_el_codigo_javascript_modulos), vimos un ejemplo de cómo organizar de una manera más óptima el código javascript mediante el uso de módulos. En dicho ejemplo creamos un módulo simple de Contabilidad que nos permitiese gestionar Cuentas (añadir, modificar, …).

Vamos a ver otra forma de hacer lo mismo, pero en este caso usando el *prototype* de los objetos javascript. El prototype permite definir las características comunes de un tipo, ya que al añadir métodos y propiedades al mismo estamos haciéndolo con todas las instancias del tipo, por lo tanto es un buen lugar para situar nuestros métodos públicos.

En primer lugar habíamos definido una clase *Cuenta* …

```javascript
function Cuenta(numerocuenta, saldo) {
    this.numerocuenta = numerocuenta;
    this.saldo = saldo;
};
```

… para instanciar objetos de tipo *Cuenta* y manipularlos en el módulo de *Contabilidad*.

La estructura de nuestro código, en esta ocasión, seguirá el siguiente esquema.

```javascript
function Cuenta(numerocuenta, saldo) {
    this.numerocuenta = numerocuenta;
    this.saldo = saldo;
};
```

Definimos en primer lugar un clase *Contabilidad* instanciable mediante *new Contabilidad()* a la que además pasaremos un array de cuentas de tipo Cuenta. En este constructor incluiremos las propiedades públicas de Contabilidad.

A continuación usamos el prototype para la definición del resto de variables y métodos privados y los exponemos mediante *return*.

Por último, mediante el uso de los paréntesis al final “()”, auto instanciamos un objeto que se incluirá en el prototype de cualquier objeto *Contabilidad*.

Con esto ya tendríamos la estructura preparada y sólo sería necesario añadir las mismas variables y métodos que habíamos añadido en el post anterior, de manera que el código quedaría defintívamente así:

```javascript
var Contabilidad = function(cuentas) {
    //Propiedades públicas
    this.Cuentas = cuentas || new Array();
    this.SaldoTotal = 0;
};

Contabilidad.prototype = function () {
    //Variables y métodos privados
    _nuevaCuenta = function (numerocuenta,saldo) {
        this.Cuentas.push(new Cuenta(numerocuenta, saldo));
    },
    _eliminarCuenta = function (numerocuenta) {
        _borrar(this.Cuentas, numerocuenta);
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
        _modificar(this.Cuentas, numerocuenta, saldo);
    },
    _modificar = function (cuentas,numerocuenta,saldo) {
        var j = 0;
        while (j < cuentas.length) {
            if (cuentas[j].numerocuenta == numerocuenta) {
                cuentas[j].saldo += saldo;
                return;
            } else { j++; }
        }
    },
    _calcularSaldoTotal = function () {
        var j = 0;
        this.SaldoTotal = 0;
        while (j < this.Cuentas.length) {
            this.SaldoTotal += this.Cuentas[j].saldo;
            j++;
        }
    }
    // Métodos públicos
    return {
        nuevaCuenta: _nuevaCuenta,
        eliminarCuenta: _eliminarCuenta,
        modificarSaldo: _modificarSaldo,
        calcularSaldoTotal : _calcularSaldoTotal
    }
}();
```

Observad que las variables públicas se exponen ahora en la definición de la clase y no como en el [ejemplo de los módulos](http://www.yagoperez.com/Post/GetPostByCode/organizando_el_codigo_javascript_modulos) en el que se hacía en el *return {}* mediante invocación a *getters.*

Ahora ya podríamos en nuestro código hacer cosas como**

```javascript
var contabilidad = new Contabilidad(new Array()); contabilidad.nuevaCuenta(111111, 100.00); contabilidad.nuevaCuenta(222222, 200.00); contabilidad.modificarSaldo(111111, 100.00); contabilidad.eliminarCuenta(111111); contabilidad.calcularSaldoTotal(); var contabilidad2 = new Contabilidad(new Array()); contabilidad2.nuevaCuenta(111111, 100.00); contabilidad2.nuevaCuenta(222222, 200.00); contabilidad2.nuevaCuenta(333333, 200.00); contabilidad2.nuevaCuenta(444444, 200.00); contabilidad2.modificarSaldo(111111, 1000.00); contabilidad2.eliminarCuenta(333333); contabilidad2.calcularSaldoTotal(); var contabilidad3 = new Contabilidad(new Array(new Cuenta(111111, 100.00), new Cuenta(222222, 200.00))); contabilidad3.nuevaCuenta(333333, 100.00); contabilidad3.calcularSaldoTotal();
```

Una ventaja de este modelo de estructuración del código es que es fácilmente extensible ya que podemos en cualquier parte sobreescribir un método expuesto de manera tan sencilla como ...

```javascript
Contabilidad.prototype.calcularSaldoTotal = function () {
    var j = 0;
    this.SaldoTotal = 0;
    while (j <  this.Cuentas.length) {
        this.SaldoTotal += this.Cuentas[j].saldo;
        j++;
    }
    alert("Metodo extendido")
}
```

e incluso añadir nuevos métodos al módulo

```javascript
Contabilidad.prototype.iniciarSaldo = function () {    
    this.SaldoTotal = 0;
}
```

Con lo que ahora podemos usar cualquier objeto anteriormente creado (contabilidad, contabilidad2 o contabilidad3) para llamar a los nuevos métodos ...

```javascript
contabilidad.calcularSaldoTotal(); contabilidad.iniciarSaldo();
```

El primer método hará lo mismo que el original pero lanzará una alerta ya que ahora está usando el método extendido con el que hemos sobreescrito el original. El segundo iniciará el saldo total tal y como indicamos

Pues con esto ya hemos acabado ... espero que resulte de utilidad

Hasta pronto!!



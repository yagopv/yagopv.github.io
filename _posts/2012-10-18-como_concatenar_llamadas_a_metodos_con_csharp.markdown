---
layout: post
title: Como concatenar llamadas a métodos con csharp
date: '2012-10-18 12:00:31'
tags:
- c#
categories:
- .NET
---

En .NET parece que están de moda las Fluent Apis para poder concatenar llamadas a métodos (chaining) de manera que el código quede más legible.

Vamos a ver un par de opciones para hacer esto de manera simple.

Últimamente veo un montón de Fluent Api´s por aquí y por allí, así que estuve mirando un poco qué posibilidades había para crear este tipo de clases, interfaces, …

Podemos hacerlo de un par de formas, retornando *this* en nuestros métodos de manera que podamos concatenar llamadas a los mismos, o bien, podemos usar [extensions methods.](http://msdn.microsoft.com/en-us/library/vstudio/bb383977.aspx "Extension methods") Vamos a poner un par de ejemplos de cómo podríamos hacerlo.

Crearemos una clase para añadir las especificaciones a un Móvil y de esta manera construir el objeto de una manera más alegre

El móvil será como este…

```c
public class Movil {
  public string Dimensiones {
    get;
    set;
  }
  public string Pantalla {
    get;
    set;
  }
  public string Red {
    get;
    set;
  }
  public int MemoriaEnGb {
    get;
    set;
  }
  public string Color {
    get;
    set;
  }
  public string SistemaOperativo {
    get;
    set;
  }
  public int MegapixelsCamara {
    get;
    set;
  }
}
```

### Método 1. Retornando this

En este primer ejemplo vamos a construir el móvil retornando *this* …

```c
public class MovilSpecsBuilderThis {
  Movil Movil;

  public MovilSpecsBuilderThis(Movil movil) {
    Movil = movil;
  }

  public MovilSpecsBuilderThis ConDimensiones(string dimensiones) {
    Movil.Dimensiones = dimensiones;
    return this;
  }

  public MovilSpecsBuilderThis ConPantallaDeTipo(string tipo) {
    Movil.Pantalla = tipo;
    return this;
  }

  public MovilSpecsBuilderThis ConConectividadARedes(string conectividad) {
    Movil.Red = conectividad;
    return this;
  }

  public MovilSpecsBuilderThis ConMemoriaInternaEnGb(int memoria) {
    Movil.MemoriaEnGb = memoria;
    return this;
  }

  public MovilSpecsBuilderThis DeColor(string color) {
    Movil.Color = color;
    return this;
  }

  public MovilSpecsBuilderThis ConSistemaOperativo(string so) {
    Movil.SistemaOperativo = so;
    return this;
  }

  public Movil RecuperarMovil() {
    return this.Movil;
  }
}
```

… como podemos ver, tenemos un constructor al que le pasamos un Móvil. Aquí podríamos tener una interfaz e inyectar dependencias, usar una Factoría o algo así … También podemos instanciar un objeto y ya está :). Después tenemos una serie de métodos que añaden características al Móvil y devuelven el propio objeto. Retornando el objeto conseguimos poder encadenar las llamadas. Finalmente añadimos un método para recuperar el propio objeto.

Ahora ya podemos hacer algo como esto …

```c
MovilSpecsBuilderThis msb = new MovilSpecsBuilderThis(new Movil());
msb.ConPantallaDeTipo("Amoled")
  .ConConectividadARedes("4G")
  .ConDimensiones("163x45x10")
  .ConMemoriaInternaEnGb(2)
  .ConSistemaOperativo("Android")
  .DeColor("blanco");
```

Vamos con el método 2

### Método 2. Usando extensions methods

Vamos a construir otra clase que use [extensions methods](http://msdn.microsoft.com/en-us/library/vstudio/bb383977.aspx "Extensions Methods") sobre el tipo Movil

```c
public static class MovilSpecsBuilderExtensions {
  public static Movil ConDimensiones(this Movil movil, string dimensiones) {
    movil.Dimensiones = dimensiones;
    return movil;
  }

  public static Movil ConPantallaDeTipo(this Movil movil, string tipo) {
    movil.Pantalla = tipo;
    return movil;
  }

  public static Movil ConConectividadARedes(this Movil movil, string conectividad) {
    movil.Red = conectividad;
    return movil;
  }

  public static Movil ConMemoriaInternaEnGb(this Movil movil, int memoria) {
    movil.MemoriaEnGb = memoria;
    return movil;
  }

  public static Movil DeColor(this Movil movil, string color) {
    movil.Color = color;
    return movil;
  }

  public static Movil ConSistemaOperativo(this Movil movil, string so) {
    movil.SistemaOperativo = so;
    return movil;
  }
}
```

… en esta ocasión hemos creado una clase estática que contiene una serie de métodos de extensión. Usamos *this Movil param* para indicar que estamos añadiendo el método al tipo Móvil.

Ya podemos hacer esto …

```c
Movil m = new Movil();
m.ConPantallaDeTipo("Amoled").ConConectividadARedes("4G").ConDimensiones("163x45x10").ConMemoriaInternaEnGb(2).ConSistemaOperativo("Android").DeColor("blanco");
```

Pues ya está, tenemos un par de *fluent clases* listas para probar …

Hasta pronto!!



---
layout: post
title: Creación de Objetos Valor en Entity Framework y el problema de los valores
  nulos
date: '2012-04-27 04:52:50'
tags:
- bases-de-datos
---


Cuando trabajamos con Objetos Valor en la creación de modelos de Dominio es posible que nos encontremos con un error muy común y es que lo Objetos Valor (Complex Types en Entity Framework) son siempre obligatorios.

En la creación de modelos de Dominio normalmente además de **Entidades** necesitaremos definir **Objetos Valor**. La diferencia entre unos y otros reside en que las **Entidades** tienen identidad propia (clave) y se traducen en tablas y relaciones entre tablas en el modelo físico y los **Objetos Valor** no poseen esta identidad y se traducen en campos de una tabla pero agrupados en un objeto.

Por ejemplo, en la entidad ***Comentario*** definida a continuación podriamos incluir un **Objeto Valor** ***UsuarioAnonimo*** que se crease cuando el comentario es escrito por un usuario anónimo. Este ***UsuarioAnonimo*** no tiene identidad por lo que en nuestro modelo vamos a tratarlo como un **Objeto Valor**

{% highlight javascript %}
public class Comentario
{
    public int IdComentario { get; set; }
    public string Titulo { get; set; }
    public string Mensaje { get; set; }
    public string Fecha { get; set; }
    public UsuarioAnonimo {get; set; }
}

public class UsuarioAnonimo
{
    public string Nombre { get; set; }
    public string Email { get; set; }
    public string Web { get; set; }
}
{% endhighlight %}

El problema que nos vamos a encontrar al crear un objeto de tipo ***Comentario*** y guardar los cambios de la siguiente manera

{% highlight javascript %}
using (var context = new ComentariosContext())
{
    Comentario  comentario = new Comentario()
    {
        Titulo  = "Titulo",
        Mensaje  = "Mensaje",
        Fecha  = DateTime.UtcNow;        
    };
    context.Comentarios.Add(comentario);
    context.SaveChanges();   
}
{% endhighlight %}

es que de manera sorprendente este código va a generar una ***Exception***

```
Null value for non-nullable member. Member: 'UsuarioAnonimo'
```

Esta ***Exception*** se produce debido a que los **Objetos Valor** (***ComplexTypes*** en Entity Framework) son siempre obligatorios aunque todos los campos sean *null*.

Por tanto, para solucionar el problema lo único que hemos de hacer es instanciar el **Objeto Valor**

{% highlight javascript %}
using (var context = new ComentariosContext())
{
    Comentario  comentario = new Comentario()
    {
        Titulo  = "Titulo",
        Mensaje  = "Mensaje",
        Fecha  = DateTime.UtcNow;  
        UsuarioAnonimo = new UsuarioAnonimo();      
    };
    context.Comentarios.Add(comentario);
    context.SaveChanges();   
}
{% endhighlight %}

Con la inicialización del ***UsuarioAnonimo***, *SaveChanges()* ya no fallaría y se grabaría un registro en la tabla de ***Comentarios*** con los campos del ***UsuarioAnonimo*** a *null*

Cuando leemos este campo, Entity Framework instacia el **Objeto Valor** y lo retorna con todos sus campos a *null*.

Para chequear si el **Objeto Valor** existe, una forma común de hacerlo es añadir una propiedad al **Objeto Valor** que haga este trabajo por nosotros ya que como podemos comprobar, sean los campos *null* o no el objeto estará siempre iniciado.

{% highlight javascript %}
public class UsuarioAnonimo
{
    public string Nombre { get; set; }
    public string Email { get; set; }
    public string Web { get; set; }
    public bool TieneValor 
    {
        get {
            return (Nombre != null || Email != null || Web != null);
        }
    }
}
{% endhighlight %}

Por tanto, añadimos una propiedad de solo lectura ***TieneValor*** que nos indicará *true* o *false* en función de si el objeto tiene algún campo distinto de *null* o no.

Por tanto, como resumen, es importante acordarse de instanciar los **Objetos Valor** en caso de que todos sus campos sean *null* antes de guardar los cambios.

Hasta pronto!!



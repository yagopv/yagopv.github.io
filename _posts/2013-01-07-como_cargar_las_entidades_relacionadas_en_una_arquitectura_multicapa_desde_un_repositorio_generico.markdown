---
layout: post
title: Cómo cargar las entidades relacionadas en una arquitectura multicapa desde
  un repositorio genérico
date: '2013-01-07 03:12:43'
tags:
- entity framework
categories:
- .NET
---


En este post vamos a ver cómo mediante un sencillo método de extensión podemos incluir la recuperación de propiedades de navegación desde las capas superiores de una arquitectura orientada al Dominio.

Cuando trabajamos en una arquitectura multicapa usando Entity Framework y aplicando los principios del Domain Driven Design, posiblemente nos encontraremos preguntándonos cómo cargar las entidades relacionadas sin sobrecargar nuestros repositorios con métodos practicamente iguales que simplemente se diferencian en el número de relaciones a incluir.

Normalmente el filtrado de entidades lo haremos desde un repositorio genérico en el que podríamos tener un método como el siguiente …

```c
public virtual IEnumerable<TEntity> GetFiltered(System.Linq.Expressions.Expression<Func<TEntity, bool>> filter)
{
    return GetSet().Where(filter);
}

IDbSet<TEntity> GetSet()
{
    return _UnitOfWork.CreateSet<TEntity>();
}
```

… que tiene cómo parámetro un filtro cualquiera y retorna las entidades que cumplan la condición del filtro.

Si no estamos usando Lazy Loading, entonces las entidades relacionadas nunca se cargarán si no lo indicamos de forma explícita mediante los métodos de inclusión que nos facilita Entity Framework ([Include](http://msdn.microsoft.com/es-es/library/bb738708.aspx "Include")). Esto puede provocar que acabemos sobreescribiendo en los repositorios específicos el método *GetFiltered* o bien añadiendo otros similares con el único objetivo de añadir los objetos relacionados. ¿No sería mucho más sencillo poder especificar directamente en nuestro método *GetFiltered* de nuestro repositorio genérico los objetos relacionados a incluir?

Técnicamente es posible hacerlo. Simplemente tenemos que crear un método de extensión en nuestra capa de acceso a datos, donde hayamos alojado nuestro repositorio genérico por ejemplo y mantengamos las referencias a Entity Framework.

El método de extensión sería así …

```c
public static IQueryable<T> IncludeMultiple<T>(this IQueryable<T> query, params Expression<Func<T, object>>[] includes)
    where T : class
{
    if (includes != null)
    {
        query = includes.Aggregate(query, 
                  (current, include) => current.Include(include));
    }
 
    return query;
}
```

… una vez definido ya lo podríamos usar en nuestro repositorio genérico y redefinir GetFiltered de la siguiente forma:

```c
public virtual IEnumerable<TEntity> GetFiltered(System.Linq.Expressions.Expression<Func<TEntity, bool>> filter, params Expression<Func<T, object>>[] includes)
{
    return GetSet().Where(filter).IncludeMultiple(includes);
}
```

Con esto ya está listo y podemos recuperar las entidades relacionadas de una forma muy sencilla desde las capas superiores de nuestra aplicación.

Por ejemplo, podríamos llamar a GetFiltered así …

```c
MyOrderRepository.GetFiltered(o => o.Id == id, o => o.OrderLines, o => o.ShipAddress, o => o.Customer)
```

En esta llamada estaríamos recuperando una colección de “Order” junto con sus propiedades de navegación “OrderLines”, “ShipAddress” y “Customer”

¿Sencillo no?

Espero que resulte de utilidad

Hasta pronto!!



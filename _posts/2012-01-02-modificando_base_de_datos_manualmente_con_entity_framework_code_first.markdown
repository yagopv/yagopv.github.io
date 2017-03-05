---
layout: post
title: Como modificar manualmente una base de datos generada a partir de Entity Framework
  Code First
date: '2012-01-02 21:51:57'
tags:
- bases-de-datos
---


Entity Framework Code First tiene la capacidad de validar el modelo de datos contra la base de datos subyacente y recrearla automáticamente cuando se realiza alguna modificación en las entidades que conforman el modelo de dominio.

Realizar cambios manuales en la base de datos puede provocar que la aplicación falle si no hemos tenido en cuenta algunos detalles.

Con Entity Framework 4.1 (Code First) podemos detectar automáticamente cambios en el modelo que no se corresponden con el esquema de la base de datos subyacente.

Cuando estamos comenzando el desarrollo y ejecutamos la aplicación, si estos cambios se detectan, podemos incluir código que nos permite regenerar la base de datos como deseemos. Para generar la base de datos podemos crear una clase que herede de [IDatabaseInitializer<TContext>](http://msdn.microsoft.com/en-us/library/gg696323%28v=vs.103%29.aspx) y customizar el proceso, o bien usar alguna de las implementaciones que ya existen, como la que usa siempre por defecto Entity Framework, [DropCreateDatabaseIfModelChanges<TContext>](http://msdn.microsoft.com/en-us/library/gg679604%28v=vs.103%29.aspx) y que elimina y genera de nuevo la base de datos cada vez que cambia el modelo.

Efectivamente, esto es perfecto cuando estamos comenzando con el desarrollo y regeneramos la base de datos costántemente, pero si estamos realizando un mantenimiento de alguna aplicación lo más probable es que necesitemos en más de una ocasión añadir campos a la base de datos, modificar longitudes y demás acciones sin poder por supuesto eliminar los datos que ya existen. Si modificamos la base de datos manualmente y adaptamos el modelo para que encaje con la modificación que hemos realizado nos vamos a encontrar con que se produce la siguiente excepción:

```
The model backing the 'MyDbContext' context has changed since the database was created. Either manually delete/update the database, or call Database.SetInitializer with an IDatabaseInitializer instance. For example, the DropCreateDatabaseIfModelChanges strategy will automatically delete and recreate the database, and optionally seed it with new data.<br />
```

La razón por la que se produce esta excepción es porque Entity Framework no chequea el esquema del modelo de datos completamente. En lugar de hacer esto calcula un código hash que compara con el código calculado la última vez que se generó el modelo. Este código se almacena en la tabla EdmMetadata que podemos observar que se genera automáticamente cada vez que generamos la base de datos.

Podemos solucionar este problema de varias formas diferentes, dependiendo de nuestra situación:

1. indicar explicitamente a Entity Framework que no calcule más este código:

protected override void OnModelCreating(DbModelBuilder modelBuilder) { base.OnModelCreating(modelBuilder); modelBuilder.Conventions.Remove<IncludeMetadataConvention>(); }

Esto provocará que ya no se valide el modelo automáticamente, con lo cual perdemos esta característica y ahora deberemos ser más cuidadosos cuando modifiquemos la base de datos manualmente.

2. Recalcular el código hash para el nuevo modelo y actualizar la fila con el codigo en la tabla EdmMetadata. Para esto podemos usar la siguiente función:

System.Data.Entity.Infrastructure.EdmMetadata.TryGetModelHash(context)

Usando esta función podemos recuperar el código hash del modelo que estamos intentando crear y actualizarlo en la base de datos. Con este pequeño truco podemos seguir manteniendo la validación del modelo que siempre es una característica interesante de Entity Framework Code First. Además de actualizar este código, hay que por supuesto actualizar la base de datos con los cambios que se han hecho en el modelo. Una alternativa a la obtención del código con esta función sería regenerar completamente una base de datos vacía con la opción de borrar y crear de nuevo ([DropCreateDatabaseIfModelChanges<TContext>](http://msdn.microsoft.com/en-us/library/gg679604%28v=vs.103%29.aspx)) y copiar el código de la tabla EdmMetadata.

**Modificado 22/06/2012****

A partir de la versión 4.3 de Entity Framework, la excepción nos indica otra cosa:

```
The model backing the 'MyDbContext' context has changed since the database was created.Consider using Code First Migrations to update the database<br />
```

Lo cierto es que existe una posibilidad de evitar el error y tomar el control manual de la base de datos. Simplemente has de establecer el Initializer a null, por ejemplo en el Global.asax

protected void Application_Start() { AreaRegistration.RegisterAllAreas(); RegisterRoutes(RouteTable.Routes); //... Database.SetInitializer<MyContext>(null); }

Es necesario hacerlo así porque IncludeMetadataConvention ha dejado de estar disponible en esta última versión (Han sustituido la tabla EdmMetadata por MigrationHistory.

Puedes hacer esto, o bien como indica el error usar Migrations y actualizar la base de datos con las últimas modificaciones en el modelo de datos pero esto ya es otra historia …

Hasta pronto!!



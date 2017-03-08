---
layout: post
title: Entity Framework Code First. Cómo añadir un índice a la base de datos.
date: '2013-02-28 10:26:13'
tags:
- bases-de-datos
---


Debería ser muy sencillo realizar una tarea tan común como añadir un índice usando un ORM como Entity Framework … pero no.

Una de las cosas que rápidamente viene a la cabeza cuando estoy creando un modelo de datos con Code First es ¿Cómo creo un índice en mi tabla? ¿Cómo se llama el atributo con el que decorar mi propiedad? ¿Dónde está el método de la Fluent Api con el que puede especificar que tal campo será un índice? … Pues bien, a todas las preguntas que se vienen de primeras a la cabeza la respuesta es la misma … No hay ni atributo ni método ni nada :)

Sin embargo, con la llegada de Entity Framework Migrations, es relativamente sencillo hacerlo.

En primer lugar he de habilitar las migraciones escribiendo en la consolo del administrador de paquetes de Nuget

```
PM> Enable-Migrations -EnableAutomaticMigrations
```

Esto añadirá la carpeta de migraciones junto a la clase de configuración *Configuration.cs* a mi proyecto.

Podríamos añadir directamente en el método *Seed* de esta clase de configuración la creación del índice. Pero existe otra forma más elegante. Para ello añadimos una nueva migración mediante

```
PM> Add-Migration CrearIndices
```

<span>Con lo que se añadiría una nueva clase *XXXX_CrearIndices.cs* en mi directorio de migraciones. La clase sería algo así</span>

```c
namespace MiProyecto.Migrations { 
    using System.Data.Entity.Migrations; 
    public partial class CrearIndices : DbMigration {
        public override void Up() { } 
        public override void Down() { } 
    } 
}
```

<span style="font-size: 1em; line-height: 1.6em;">Los métodos *Up() y Down()* tienen como objetivo que indiquemos el trabajo a realizar cuando paso de una migración a otra más actual o cuando vuelvo a la original. Por tanto usaremos *Up()* para crear los índices y *Down()* para eliminarlos.</span>

```c
namespace MiProyecto.Migrations { 
    using System.Data.Entity.Migrations; 
    public partial class CrearIndices : DbMigration {
        public override void Up() {
            CreateIndex("Tabla1", "Campo1", true, "IX_Campo1"); 
            CreateIndex("Tabla2", "Campo2", true, "IX_Campo2"); 
        } 
        public override void Down() { 
             DropIndex("Tabla1", "IX_Campo1");
             DropIndex("Tabla2", "IX_Campo1"); 
        } 
    } 
}
```

<span style="font-size: 1em; line-height: 1.6em;">Por último, sólo quedaría aplicar la migración mediante</span>

```
PM> Update-Database
```

Sencillo no?

Hasta pronto!!

<span>  
</span>



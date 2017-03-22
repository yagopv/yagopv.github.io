---
layout: post
title: Referencia de comandos para Entity Framework Migrations
date: '2013-02-28 10:15:28'
tags:
- visual studio
categories:
- Tools
---

En este artículo vamos a listar y ver los diferentes comandos que tenemos disponibles para manejar nuestra base de datos usando Entity Framework Migrations.

La consola del administrador de paquetes Nuget es el lugar indicado para actualizar nuestra base de datos a lo largo de la vida del proyecto. Actualmente hay cuatro comandos básicos disponibles para la ejecución de diferentes tareas de mantenimiento. Vamos a verlos

#### Enable-Migrations

Este es el comando que usaremos cada vez que comencemos a usar Entity Framework Migrations. Lo que hace es habilitar el uso de las mismas

Su sintaxis es la siguiente:

```
Enable-Migrations [-EnableAutomaticMigrations][[-ProjectName] <String>][-Force][<CommonParameters>]
```

La tarea que realiza es generar la clase de configuración de Migrations en el directorio correspondiente. Esta clase será *Configuration.cs.* Además se encarga de crear una migración inicial en caso de que estemos usando un *Initializer* para la creación de la base de datos.

Los parámetros que podemos usar con el comando son:

**<span class="br0">[</span>-EnableAutomaticMigrations**<span class="br0">**]** : Especifica si se habilitarán las migraciones automáticas en la creación de la clase de configuración. Si no lo indico, las migraciones automáticas estarán desabilitadas aunque lo podemos cambiar posteriormente directamente sobre la clase de configuración.</span>

```language-javascript
public Configuration() { 
    AutomaticMigrationsEnabled = false; 
}
``` 

<span class="br0"></span>

<span class="br0">**<span class="br0">[</span>-ProjectName<span class="br0">]</span><span class="br0">** : Especifica el nombre del proyecto en el que se creará la clase de configuración y la carpeta de migraciones. También puedo seleccionarlo del combobox de la ventana de la consola. Por defecto usará el que está seleccionado en dicho combo.</span></span>

<span class="br0"><span class="br0">**<span class="br0">[</span>-Force**<span class="br0">**]** : Especifica que la clase de configuración será sobreescrita independientemente de si existe o no</span></span></span>

<span class="br0"><span class="br0"><span class="br0">**<span class="br0">[<CommonParameters>] : </span>**<span class="br0">Se permite añadir otra serie de parámetros como </span><span class="br0">*Verbose, Debug, ErrorAction, ErrorVariable, WarningAction, WarningVariable, OutBuffer and OutVariable* con diferentes utilidades. Si queremos ver una descripción de cada uno podemos teclear *get-help*.</span></span></span></span>

#### Add-Migration

Se encarga de generar una nueva clase en nuestra carpeta de migraciones incluyendo los cambios que se hayan hecho sobre el modelo de datos desde la última migración aplicada.

```
Add-Migration [-Name] <String> [-Force][-ProjectName <String>][-StartUpProjectName <String>][-ConfigurationTypeName <String>][-ConnectionStringName <String>][-IgnoreChanges][<CommonParameters>]  </p>
<p>Add-Migration [-Name] <String> [-Force][-ProjectName <String>][-StartUpProjectName <String>][-ConfigurationTypeName <String>] -ConnectionString <String> -ConnectionProviderName <String> [-IgnoreChanges][<Common Parameters>]
```

**[-Name] <String> :** Especifica el nombre que va a tener la migración, y por tanto el archivo correspondiente

**[-Force] :** Fuerza la sobreescritura de la migración si ya existiese

**[-ProjectName <String>] :** Especifica el nombre del proyecto en el que se creará la clase de configuración y la carpeta de migraciones. También puedo seleccionarlo del combobox de la ventana de la consola. Por defecto usará el que está seleccionado en dicho combo.

**[-StartUpProjectName <String>] :** Especifica el archivo de configuración a usar. Por defecto tratará de encontrar un archivo de configuración en el proyecto que me encuentro situado en la consola.

**[-ConfigurationTypeName <String>] :** Indica el tipo de la clase de configuración a usar. Si no se indica nada usará la del proyecto en la que esté situado.

**[-ConnectionStringName <String>] :** Especifica el nombre de la cadena de conexión a usar.

**[-ConnectionString <String>] :** Especifica la cadena de conexión a usar.

**[ConnectionProviderName <String>] :** Especifica el proveedor de datos a usar

**[-IgnoreChanges] :** Crea una migración vacía ignorando los cambios que hayan podido aplicarse a las clases del contexto de trabajo. Este comando es de gran utilidad para crear una migración inicial con una base de datos que ya exista.

**[<CommonParameters>] :**Se permite añadir otra serie de parámetros como *Verbose, Debug, ErrorAction, ErrorVariable, WarningAction, WarningVariable, OutBuffer and OutVariable* con diferentes utilidades. Si queremos ver una descripción de cada uno podemos teclear *get-help.*

#### Update-Database

Este comando es el encargado de aplicar la migración sobre la base de datos reflejando de esta manera el modelo actual en la misma.

```
Update-Database [-SourceMigration <String>][-TargetMigration <String>][-Script][-Force][-ProjectName <String>][-StartUpProjectName <String>][-ConfigurationTypeName <String>][-ConnectionStringName <String>][<CommonParameters>]  </p>
<p>Update-Database [-SourceMigration <String>][-TargetMigration <String>][-Script][-Force][-ProjectName <String>][-StartUpProjectName <String>][-ConfigurationTypeName <String>] -ConnectionString <String> -ConnectionProviderName <String> [<CommonParameters>]
```

**[-SourceMigration <String>] :** Especifica la migración que se quiere usar como punto de partida. Cuando se omite, se aplicará la última migración disponible. Sólo se permite su uso con -Script

**[-TargetMigration <String>] :** Especifica la migración de destino. Indica la migración cuyos cambios queremos aplicar sobre la base de datos. Si se omite, se usa el modelo actual como destino.

**[-Script] :** Genera un script sql en lugar de ejecutar los cambios directamente.

**[-Force] :** Habilita la permisividad con la pérdida de datos en la migración a aplicar. Por ejemplo, cuando modifico un campo string de longitud 150 a 100, si no indico -Force, se producirá una excepción debido a que la migración puedo provocar pérdidas de datos en la base de datos.

**[-ProjectName <String>] :** Especifica el nombre del proyecto en el que se creará la clase de configuración y la carpeta de migraciones. También puedo seleccionarlo del combobox de la ventana de la consola. Por defecto usará el que está seleccionado en dicho combo.

**[-StartUpProjectName <String>] :** Especifica el archivo de configuración a usar. Por defecto tratará de encontrar un archivo de configuración en el proyecto que me encuentro situado en la consola.

**[-ConfigurationTypeName <String>] :** Indica el tipo de la clase de configuración a usar. Si no se indica nada usará la del proyecto en la que esté situado.

**[-ConnectionStringName <String>] :** Especifica el nombre de la cadena de conexión a usar.

**[-ConnectionString <String>] :** Especifica la cadena de conexión a usar.

**[ConnectionProviderName <String>] :** Especifica el proveedor de datos a usar

**[<CommonParameters>] :** Se permite añadir otra serie de parámetros como *Verbose, Debug, ErrorAction, ErrorVariable, WarningAction, WarningVariable, OutBuffer and OutVariable* con diferentes utilidades. Si queremos ver una descripción de cada uno podemos teclear *get-help*.

#### Get-Migrations

Muestra la migración que se ha aplicado sobre la base de datos

```
Get-Migrations [-ProjectName <String>] [-StartUpProjectName <String>] [-ConfigurationTypeName <String>] [-ConnectionStringName <String>] [<CommonParameters>]  </p>
<p>Get-Migrations [-ProjectName <String>] [-StartUpProjectName <String>] [-ConfigurationTypeName <String>] -ConnectionString <String> -ConnectionProviderName <String> [<CommonParameters>]
```

**[-ProjectName <String>] :** Especifica el nombre del proyecto en el que se creará la clase de configuración y la carpeta de migraciones. También puedo seleccionarlo del combobox de la ventana de la consola. Por defecto usará el que está seleccionado en dicho combo.

**[-StartUpProjectName <String>] :** Especifica el archivo de configuración a usar. Por defecto tratará de encontrar un archivo de configuración en el proyecto que me encuentro situado en la consola.

**[-ConfigurationTypeName <String>] :** Indica el tipo de la clase de configuración a usar. Si no se indica nada usará la del proyecto en la que esté situado.

**[-ConnectionStringName <String>] :** Especifica el nombre de la cadena de conexión a usar.

**[-ConnectionString <String>] :** Especifica la cadena de conexión a usar.

**[ConnectionProviderName <String>] :** Especifica el proveedor de datos a usar

**[<CommonParameters>] :** Se permite añadir otra serie de parámetros como *Verbose, Debug, ErrorAction, ErrorVariable, WarningAction, WarningVariable, OutBuffer and OutVariable* con diferentes utilidades. Si queremos ver una descripción de cada uno podemos teclear *get-help.*

Para obtener información acerca de estos comandos también podemos escribir directamente en la consola el comando

```
get-help command-name -detailed
```

Esto es todo!!

Hasta pronto!!



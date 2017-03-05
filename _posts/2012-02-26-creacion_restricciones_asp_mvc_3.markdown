---
layout: post
title: Creación de restricciones personalizadas en ASP MVC 3
date: '2012-02-26 12:51:52'
tags:
- validacion
---


Cuando se desarrollan modelos de datos es común la necedidad de realizar ciertas validaciones sobre las propiedades de las entidades.

El uso de restricciones es una de la maneras más comunes y sencillas de poder realizar esta tarea.

#### Creación de restricciones personalizadas en ASP MVC 3

Cuando desarrollamos un modelo de datos de Entity Framework, es común la utilización de [DataAnnotations ](http://msdn.microsoft.com/es-es/library/system.componentmodel.dataannotations.aspx "Anotaciones de datos")para validar las propiedades de nuestras entidades de datos.

Es posible definir atributos personalizados [además de los que ya existen en el Framework](http://msdn.microsoft.com/en-us/library/dd901590%28v=vs.95%29.aspx "Atributos predefinidos") en *<span style="font-style: italic;">System.ComponentModel.DataAnnotations</span>* y que nos permiten establecer restricciones como pueden ser rangos de valores correctos, longitudes máximas para una propiedad, evitar la introducción de nulos y diversas opciones más. Estas anotaciones se conocen comúnmente como restricciones.

Vamos a ver diferentes formas de definir nuestras restricciones de manera que podamos personalizar al máximo la validación de nuestras entidades de datos.

#### Validación utilizando el atributo [CustomValidation]

El atributo [[CustomValidation]](http://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.customvalidationattribute%28v=vs.95%29.aspx "El atributo CustomValidation") nos permite seleccionar una clase y un método de la misma que se encargará de efectuar la validación del campo. Este atributo se puede aplicar sobre una propiedad concreta de la entidad o bien sobre toda la entidad.

[CustomValidation(typeof(ProductValidations), "IsValidIdProducto")] public int IdProducto {get; set;}

El código anterior nos permite validar la propiedad **IdProducto**. Para esto, según se ha indicado en la anotación, se usará una clase **ProductValidations** y dentro de esa clase, el método **IsValidIdProducto **se encargará de la validación de la propiedad. Este método ha de ser estático**.**

****Supongamos que el valor del identificador del producto no puede superar el 10000. Una posible implementación de esta restriccion personalizada sería la siguiente

public static class ProductValidations { public static ValidationResult IsValidIdProducto(int value) { if (value Como podemos comprobar, en caso de que la validación sea correcta se está retornado *ValidationResult.Success*. En caso contrario se devuelve un nuevo [*ValidationResult*](http://msdn.microsoft.com/es-es/library/system.windows.controls.validationresult.aspx "ValidationResult")con el texto del mensaje de error. Es posible retornar *null* en este nuevo ValidationResult que se genera al producirse un error. En este último caso, el mensaje de error se recogerá de las propiedades del atributo *[CustomValidation]*.

Como comentábamos al principio, si la validación afecta a más de una propiedad, podemos aplicar el atributo a nivel de clase.

Supongamos una clase **Video** que contiene las propiedades *altura* y *anchura*

[CustomValidation(typeof(VideoValidations),"comprobarMedidas")] public class Video { public string Url {get; set;} public int Altura { get; set;} public int Anchura {get; set;} }

si queremos validar que las medidas del video cumplan un mínimo y un máximo podríamos hacerlo con una implementación como la siguiente

public static class VideoValidations { public static ValidationResult comprobarMedidas(Video value) { if ((value.Altura > 100) && (value.Anchura > 200)) { if (value.Anchura > value.Altura) { return ValidationResult.Success; } } return new ValidationResult("Error en las medidas del Video"); } }

En la siguiente validación se obliga a que la anchura del video sea mayor que la altura y además que la altura sea mayor que 100 y la anchura mayor de 200.

#### Validación mediante la creación de atributos personalizados

Otra forma de aplicar restricciones sobre nuestras entidades es directamente definir atributos como los que ya nos ofrece el propio Framework. Para ello deberemos crear una clase que herede de [**ValidationAttributte**](http://msdn.microsoft.com/es-es/library/system.componentmodel.dataannotations.validationattribute.aspx "Clase base de todos los atributos de validación") .Esta clase tiene que sobreescribir el método* IsValid()* que nos indicará si se cumple o no la restricción

public class IsValidIdProductoAttributte : ValidationAttibutte { public override bool IsValid(object value) { int id = Convert.Int32(value); if (id En el ejemplo anterior, se crea un atributo que realiza la misma función que en el primer ejemplo que vimos. Obliga a que el identificador del producto introducido sea menor o igual a 10000.

Este nuevo atributo que acabamos de crear se aplicaría de la siguiente forma

[IsValidIdProducto] public int IdProducto {get; set;} //Si queremos devolver un mensaje de error [IsValidIdProducto(ErrorMessage="El identificador de producto tiene que ser menor o igual que 10000")] public int IdProducto {get; set;}

Como en el caso del empleo de [CustomValidation], los atributos personalizados pueden aplicarse a nivel de clase. En este caso el tipo de *value* sería la clase sobre la que se aplique.

#### Validación con IValidatableObject

La interfaz [**IValidatableObject**](http://msdn.microsoft.com/es-es/library/system.componentmodel.dataannotations.ivalidatableobject.aspx "La interfaz IValidatableObject")también nos permite aplicar restricciones sobre nuestras entidades del modelo. Esta interfaz reside en *System.ComponentModel.DataAnnotations*.

Para usar la interfaz hacemos que directamente la entidad sobre la que queremos implementar restricciones la implemente. La implementación de **IValidatableObject** añadirá un método *Validate()* a nuestra clase que usaremos para definir nuestras restricciones. En este método comprobaremos la validez de la entidad, devolviendo una lista de errores (**ValidationResult**) en caso de que se produzcan.

public class Video : IValidatableObject { public string Url {get; set;} public int Altura { get; set;} public int Anchura {get; set;} public IEnumerable<validationresult> Validate(ValidationContext validationContext) { if (string.IsNullOrEmpty(Url)) { yield return new ValidationResult("Url obligatoria", new [] {"Url"} ); } if ((Altura Anchura) { yield return new ValidationResult("La altura no debe ser mayor que la anchura", new [] {"Altura", "Anchura"} ); } } } }</validationresult>

En la implementación del método *Validate()* se definen las restricciones a aplicar sobre la clase. Vemos que los **ValidationResult** devuelven el mensaje de error además de las propiedades que lo están provocando. Se genera por tanto una lista de errores que se propagará a la Vista. El uso de la interfaz **IValidatableObject** es compatible con el uso del resto de restricciones, pero sólo se ejecutará el método *Validate()* cuando no exista ningún error una vez pasadas las validaciones de las anotaciones de datos.

A modo de resumen, hemos visto que disponemos de diversas técnicas para realizar la validación de nuestro modelo. La última técnica (IValidatableObject) resulta de gran utilidad cuando no queremos hacer depender a nuestras entidades de las anotaciones de datos y establecer así una validación tal vez un poco más limpia.

Hasta aquí este breve resumen de las posibilidades de validación de modelos usando restricciones personalizadas ... hasta pronto!!



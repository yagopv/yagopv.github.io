---
layout: post
title: Cómo validar una entidad de datos manualmente
date: '2013-04-08 06:44:23'
---


Normalmente, además de realizar una validación de datos en el cliente, se ha de realizar una (o varias) validaciones de datos en el servidor.

Vamos a definir una clase que nos permita validar nuestras entidades manualmente desde la capa que queramos.

Cuando desarrollamos una aplicación usando .NET, antes de persistir nuestras entidades de datos en la base de datos deberíamos validar que lo que vamos a insertar o modificar es correcto.

Este proceso de validación suele iniciarse en el cliente, repitiéndose la validación en uno o varios puntos en el servidor.

Digo varios puntos porque, por ejemplo, si estamos usando *viewmodels*, haremos una validación del *viewmodel* y posteriormente generaremos una entidad de nuestro modelo de Dominio a partir de dicho *viewmodel*, que será la que realmente persistiremos. Esta entidad de nuestro modelo será diferente que nuestro *viewmodel* en muchas ocasiones por lo que la deberíamos validar de nuevo después de hacer el mapeo entre el *viewmodel* y el *modelo* real que se persistirá en la base de datos.

Nuestras entidades de datos normalmente se validarán usando **DataAnnotations** y/o implementando la interfaz **IValidatableObject** por lo que nos vendría bien definir una clase que nos echara una mano con la acción repetitiva de validar la entidad antes de persistirla.

Esta clase podría ser algo así

{% highlight javascript %}
public class MyEntityValidator     
{
    void SetValidatableObjectErrors<TEntity>(TEntity item, List<string> errors) where TEntity : class
    {
        if (typeof(IValidatableObject).IsAssignableFrom(typeof(TEntity)))
        {
            var validationContext = new ValidationContext(item, null, null);
            var validationResults = ((IValidatableObject)item).Validate(validationContext);
            errors.AddRange(validationResults.Select(vr => vr.ErrorMessage));
        }
    }

    void SetValidationAttributeErrors<TEntity>(TEntity item, List<string> errors) where TEntity : class
    {
        var result = from property in TypeDescriptor.GetProperties(item).Cast<PropertyDescriptor>()
                     from attribute in property.Attributes.OfType<ValidationAttribute>()
                     where !attribute.IsValid(property.GetValue(item))
                     select attribute.FormatErrorMessage(string.Empty);
        if (result != null
            &amp;&amp;
            result.Any())
        {
            errors.AddRange(result);
        }
    }

    public bool IsValid<TEntity>(TEntity item) where TEntity : class
    {
         if (item == null)
            return false;
         List<string> validationErrors = new List<string>();
        SetValidatableObjectErrors(item, validationErrors);
        SetValidationAttributeErrors(item, validationErrors);
         return !validationErrors.Any();
    }
		
    public IEnumerable<string> GetInvalidMessages<TEntity>(TEntity item) where TEntity : class
    {
        if (item == null)
           return null;
         List<string> validationErrors = new List<string>();
         SetValidatableObjectErrors(item, validationErrors);
         SetValidationAttributeErrors(item, validationErrors);
         return validationErrors;
    }
}
{% endhighlight %}

De esta forma, cada vez que quisiéramos validar una entidad, no tendríamos nada más que crear la clase, llamar al método *IsValid()* para comprobar si la clase es válida o no, y en caso de que no lo sea llamar a *GetInvalidMessages()* y recuperar todos los errores que se hayan producido en la validación de la entidad.

Nótese que lo que se está haciendo es simplemente comprobar las validaciones que se hayan especificado en la clase del modelo de datos tanto implementando **IValidatableObject** como mediante **DataAnnotations**.

Hasta pronto!!



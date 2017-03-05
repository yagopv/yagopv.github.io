---
layout: post
title: El patrón especificación
date: '2012-02-26 12:11:49'
tags:
- patrones
---


El patrón especificación resulta de gran utilidad en aplicaciones orientadas al dominio.

Permite separar claramente las responsabilidades entre el conjunto de objetos que se quiere recuperar y la forma de hacerlo, por lo que resulta ideal en este tipo de escenarios.

#### El patrón especificación

El patrón especificación trata de separar la decisión de qué objetos seleccionar en una query del objeto que realiza la selección en sí.

Las especificaciones, en términos de aplicaciones orientadas al Dominio, se suelen definir en la capa del Dominio de la aplicación. Normalmente se definen junto con cada uno de los agregados que conforman el Dominio.

Existe un [excelente documento](http://martinfowler.com/apsupp/spec.pdf "El patrón especificación") en el que se explica de manera inmejorable este patrón y cuyos autores son Eric Evans y Martin Fowler. En este documento se puede ver una implementación del patrón especificación basada en objetos en memoria pero en las versiones actuales de C# podemos, gracias a las expresiones lambda, implementar especificaciones de manera más efectiva y potente.

Las especificaciones suelen ser muy útiles en escenarios en donde el usuario puede componer queries y guardarlas para usos futuros.

¿En qué casos se puede usar y resulta de gran utilidad una especificación?

<div class="x-content-band border-top border-left border-right border-bottom" id="x-content-band-1" style="background-color: #f1f0ff;"><div class="x-container max width"> – Selección de un grupo de objetos que cumplan un criterio  
 – Asegurar que sólo un grupo de objetos cumplen una determinada regla  
 – Necesidad de describir lo que un objeto es capaz de hacer pero sin indicar cómo lo hace, de una manera que el objeto candidato puede ser construido para que encaje con el requerimiento  
</div></div>Para construir una especificación es necesario crear una clase (la especificación) que sea capaz de indicar si un objeto candidato cumple con un criterio o no. La especificación tendrá un método **bool IsSatisfiedBy (object)** que devuelve *true* si se cumplen los criterios o *false* en caso contrario.

La ventaja de usar especificaciones es el desacoplamiento que podemos conseguir en nuestra aplicación al definir las queries en la capa del Dominio. Además al poder testearlas aumentamos la confianza y la capacidad de validación de nuestra aplicación.

<span><span>De todas maneras, la especificación no debe usarse en cualquier caso ya que se corre el riesgo de caer en un anti-patrón. No se debe usar para todos los tipos de queries. Si nos encontramos usando métodos poco comunes tal vez estemos abusando del patrón.   
</span></span>

<span><span>Existen un montón de implementaciones de este patrón buscando por la red. Yo voy a dejar dos enlaces a dos proyectos de Codeplex en los que se pueden ver dos implementaciones similares (Usando C# y expresiones lambda) y que además están bien documentados.</span></span>

<span>  
</span>

<span id="ctl00_ctl00_MasterContent_ProjectTitleControl1_TitleLabelBelow">[Microsoft Spain – Domain Oriented N-Layered .NET 4.0 Sample App](http://microsoftnlayerapp.codeplex.com/ "Aplicación de ejemplo de una arquitectura orientada al Dominio"). Es un excelente proyecto de ejemplo de cómo construir una aplicación orientada al Dominio que contiene una imnplementación de este patrón  
</span>

<span>[LinqSpecs](http://linqspecs.codeplex.com/ "Framework que implementa el patrón especificación"). Es un Framework que nos permite el uso de especificaciones  
</span>

<span><span>  
</span></span>

<span><span>También puede verse una definición e implementación sencilla y rápida [aquí](http://en.wikipedia.org/wiki/Specification_pattern "El patrón especificación")</span></span>

<span><span>Hasta otra!!  
</span></span>



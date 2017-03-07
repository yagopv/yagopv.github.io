---
layout: post
title: Modificadores en C#
date: '2012-03-13 11:07:45'
tags:
- poo
---


Los modificadores son palabras reservadas de la sintaxis de C# que alteran las declaraciones de tipos y miembros de tipos provocando una serie de restricciones de acceso o modificaciones de comportamiento en los miembros sobre los que se aplican.

#### **Modificadores de acceso**

Los modificadores de acceso indican el nivel de accesibilidad que tendrá un miembro o un tipo desde otros miembros que lo referencien. Existen cuatro modificadores de acceso en C#.

***<span style="text-decoration: underline;">public </span>***

 Es el modificador menos restrictivo. **No existen restricciones** para el acceso a los miembros o tipos que se hayan definido mediante *public.*

<span style="text-decoration: underline;">***private***</span>

Los miembros privados **sólo son accesibles dentro de la clase en la que se definen**. Es por tanto el modificador más restrictivo.

<span style="text-decoration: underline;">***internal***</span>

El modificador *internal *indica que aquellos miembros o tipos que se hayan definido con este modificador de acceso sólo serán accesibles **desde los archivos del mismo ensamblado.**

<span style="text-decoration: underline;">***protected***</span>

El modificador *protected *indica que **sólo la clase en la que se ha utilizado el modificador y sus clases derivadas** tendrán acceso al miembro o tipo definido como *protected.*

#### Modificadores de miembros y tipos*  
*

<span style="text-decoration: underline;">***abstract***</span>

El modificador *abstract* se puede utilizar con clases, métodos, propiedades, indizadores y eventos. El modificador *abstract* se utiliza principalmente para definir clases base, obligándo a las clases derivadas a implementar los miembros marcados con *abstract.*

Si en una clase se definen miembros marcados con *abstract*, **la clase debe marcarse como *abstract****.*

Las clases marcadas con *abstract ***no se pueden instanciar*.***

Debido a que las clases abstractas suelen ser clases base, no se pueden modificar con el modificador **sealed, **que hace que la clase no se pueda derivar

Una clase no abstracta derivada de una clase abstracta **debe incluir implementaciones reales de todos los descriptores de acceso****y métodos abstractos heredados**.

Si defino métodos como *abstract*, estoy indicando que el **método no contiene implementación, **por tanto**, **los métodos abstractos son lo mismo que los métodos virtuales (*virtual) * ya que obligan a las clases derivadas a implementarlos. **Sólo se pueden definir métodos abstractos en las clases abstractas.**

Las propiedades abstractas funcionan como los métodos abstractos, salvo las diferencias en la sintaxis de las declaraciones y llamadas. **Las propiedades abstractas funcionan igual que los métodos abstractos.**

Para sobreescribir métodos o propiedades abstractas utilizaré el modificador *override.*

<span style="text-decoration: underline;">***const***</span>

El modificador *const* se utiliza para la **definición de constantes.**

<span style="text-decoration: underline;">***event***</span>

Este modificador se utiliza para **declarar eventos.**

<span style="text-decoration: underline;">***extern***</span>

El modificador *extern *indica que el método marcado con este modificador **se implementa externamente en otro ensamblado**.

<span style="text-decoration: underline;">***new***</span>

Este modificador se utiliza en tres situaciones diferentes

- - Para **crear objetos**
- **Ocultación de miembros heredados** de un miembro de una clase base. Al aplicarlo en una clase derivada estamos ocultando el método de la clase base.
- **Restricción en la definición de clases genéricas** para indicar que el tipo genérico debe tener un constructor público sin parámetros

<span style="text-decoration: underline;">***override***</span>

Se usa para **implementar métodos de clases base marcados como *virtual* o *abstract*** en las clases derivadas. Al aplicar *override *en una clase derivada **no se oculta la implementación en la clase base**, como sucede con el modificador *new*

<span style="text-decoration: underline;">***partial***</span>

Permite **dividir la definición de miembros **en varios archivos dentro del mismo ensamblado

<span style="text-decoration: underline;">***readonly***</span>

Se utiliza en campos. Provoca que el campo definido con el modificador *readonly ***no se pueda modificar fuera de la propia declaración del campo o del contructor de la clase.**

<span style="text-decoration: underline;">***sealed***</span>

Se aplica a clases, métodos, propiedades. Los miembros marcados con *sealed ***no se pueden derivar. **Por tanto permite definir clases que no se pueden heredar**.  
**

<span style="text-decoration: underline;">***static***</span>

Los miembros a los que se aplica el modificador *static* pertenecerán al tipo declarado y no a un objeto especifico. El modificador *static* puede utilizarse con clases, campos, métodos, propiedades operadores y eventos,pero no con indizadores, destructores o tipos que no sean clases. Por tanto, los miembros declarados con *static* tienen la cualidad de sólo existir una vez y no pueden coexistir múltiples instancias en memoria al mismo tiempo.

**No es posible instanciar clases estáticas**, es el propio CLR el que las carga en memoria cuando se carga el espacio de nombres que contiene dicha clase

<span style="text-decoration: underline;">***unsafe***</span>

La palabra clave *unsafe *denota un contexto no seguro, que es necesario para cualquier operación que involucre a punteros

<span style="text-decoration: underline;">***virtual***</span>

**  
**Al utilizar el modificador *virtual *sobre un método, propiedad, indizador o declaración de evento, estamos permitiendo que este se pueda sobreescribir en una clase derivada.

<span style="text-decoration: underline;">***volatile***</span>

La palabra clave *volatile* indica que varios subprocesos que se ejecutan a la vez pueden modificar un campo. Los campos que se declaran como *volatile* no están sujetos a optimizaciones del compilador que suponen el acceso por un subproceso único. Esto garantiza que el valor más actualizado está en todo momento presente en el campo.

Hasta pronto!!



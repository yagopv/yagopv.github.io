---
layout: post
title: 'Los operadores ? : y ?? en c#'
date: '2012-07-10 06:54:11'
tags:
- c#
categories:
- .NET
---

Vamos a ver en este post cómo usar el operador ternario y el operador null coalescing (??) en c#.

### El operador ternario (?)

Este operador permite escribir instrucciones tipo *if* de manera más concisa y legible. Supongamos que tenemos una instrucción como la siguiente

```javascript
if (condicion) { 
    expresion1 
} else { 
    expresion2 
}
```

Usando el operador ternario podemos hacer exactamente lo mismo escribiendo la instrucción de esta forma

```javascript
(condicion) ? expresion1 : expresion2
```

### El operador null coalescing (??)

El operador null coalescing permite retornar un valor nulo o no dependiendo de la nulidad del primer valor evaluado

Vamos a modificar un poco el ejemplo anterior

```javascript
if (variable != null) 
{
    return variable;
} else {
    return "**";
}
```

Estamos evaluando si una variable es *null* o no. Con el operador ?? podríamos reducir esta instrucción a

```javascript
return variable ?? "**";
```

Bastante más compacto ¿no?. Si *variable* es *true* se retornará *variable*. En caso contrario '**'.

El operador ?? es de gran utilidad para retornar valores en funciones o en la asignación de variables que pueden ser nulas.

Hasta pronto!!



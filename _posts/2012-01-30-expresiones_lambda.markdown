---
layout: post
title: Las expresiones Lambda
date: '2012-01-30 14:47:24'
---


Una expresión lambda es una función o una subrutina sin nombre que se puede utilizar donde haya un delegado válido.

Las expresiones lambda se usan en consultas de LINQ basadas en métodos como argumentos de los métodos de operador de consulta estándar, tales como Where.

Son ampliamente usadas en el ORM de Microsoft, Entity Framework, donde se puede ver claramente todo su potencial.

#### ¿Qué es una expresión Lambda?

Las expresiones lambda son funciones anónimas que devuelven un valor. Se pueden utilizar en cualquier lugar en el que se pueda utilizar un delegado. Son de gran utilidad para escribir métodos breves que se pueden pasar como parámetro.

Las expresiones lambda se declaran en el momento en el que se van a usar como parámetro para una función.

#### Declaración

Una expresión lambda se declara mediante la siguiente sintaxis:

```
<p>param => expr</p>
<p>-    param será la lista de parámetros separados por comas<br />-    => es la separación entre los parámetros y la declaración de la función<br />-    expr es la implementación de la función</p>
```

<span style="text-decoration: underline;">*Ejemplos de funciones lambda*</span>

```
<p>n => n / 10 . Función que divide un número entre 10<br />(x,y) => x / y.  Función que divide dos números<br />N => {  <br />    N = n*2;<br />    return n;<br />}. Función que multiplica un número por dos</p>
<p>mensaje => Console.WriteLine(mensaje);  . Función que no retorna nada y escribe por pantalla el mensaje que ha recibido como parámetro</p>
```

El tipo de retorno es inferido por el contexto. También destacar que si se realizan varias operaciones es necesario utilizar *return*. Los paréntesis sólo son obligatorios cuando hay más de un parámetro.

#### Definición de funciones anónimas

Las expresiones lambda se convierten en tiempo de compilación en funciones anónimas. Las referencias a estas funciones se transforman en delegados a dicha función. Esto permite invocar estas funciones desde código, como por ejemplo:

 delegate dividir = (x,y) => x / y; Console.WriteLine(dividir(10,5)); // Escribirá un 2

**dividir** en este caso es un delegado, no la función. Será el compilador el que asigne el nombre a la función en tiempo de compilación

Una manera rápida de escribir lo mismo sería usando el tipo **Func<>**

Func<int int=""> dividir = (x,y) => x/y; Console.WriteLine(dividir(10,5)); // Escribirá un 2</int>

Mediante Func<int,int,int> le asignamos tipos a un delegado. Mediante esta definición estamos indicando que el tipo Func<int,int,int> es un delegado que apunta a una función, que tiene dos parámetros de tipo entero y devuelve un resultado de tipo entero.

Esta variable **dividir**, podemos pasarla como parámetro a una función para que la utilice.

public int ejecutarOperacion(int valor1, int valor2, Func<int int=""> operacion) { return operacion(valor); } </int>

 y la usaremos de cualquiera de las siguientes dos formas

int i = ejecutarOperacion(10,5, (x,y) => x / y); int j = ejecutarOperacion(10,5, dividir);

Como se puede observar, se puede definir directamente la función en la llamada al método o bien utilizar una existente como la vista anteriormente (**dividir**) que encaje con la definición del parámetro

Es importante recordar que las expresiones lambda se pueden utilizar en cualquier lugar en el que se acepten delegados como por ejemplo en la subscripción a eventos (Formularios WebForms) proporcionando una sintaxis más concisa y fácil de leer.

#### Definición de delegados con expresiones lambda

.NET Framework dispone de una serie de definiciones genéricas de delegados predefinidas y preparadas para utilizar. Son **Action** y **Func** y son generics.

El tipo **Func** se utilizará para expresiones lambda que retornen valores (funciones) y **Action** para expresiones que simplemente ejecuten un grupo de instrucciones que no retornan ningún valor

Estos tipos están definidos en la librería System. Existen definiciones predeterminadas con hasta cuatro parámetros. Si necesitamos más parámetros no tenemos más que definirlo en nuestro código. En el ejemplo a continuación vemos una definición que permite utilizar Func<> con hasta 8 parámetros

 public delegate TResult Func<t1 t2="" t3="" t4="" t5="" t6="" t7="" t8="" tresult=""> (T1 p1, T2 p2, T3 p3, T4 p4, T5 p5, T6 p6, T7 p7, T8 p8); Func<int> Suma = (a,b,c,d,e,f,g,h) => a + b + c + d + e + f + g + h;</int></t1>

Si no se devuelven valores no se puede utilizar **Func**. Referenciar Func<int> produce un error de compilación y no es válido. Para esta tarea se dispone del tipo Action que no retorna valores, simplemente ejecuta grupos de instrucciones</int>

 Action<string> escribirMensaje = (mensaje) => Console.WriteLine(mensaje); escribirMensaje("Un saludo"); Action escibirMensajePorDefecto = () => Console.WriteLine("Hola");</string>

#### Arboles de expresión

Las expresiones lambda vistas en el punto anterior se utilizan exclusivamente como funciones. Hay otra manera de utilizarlas que resulta de gran utilidad y es ampliamente usada en el ORM de la casa, Entity Framework. En las expresiones lambda vistas hasta ahora, el compilador genera una función ejecutable que se permite invocar desde la parte del código en que necesitemos dicha función de una forma directa. Cuando construimos un árbol de expresión no se genera una función ejecutable, sino una estructura de datos en forma de árbol donde cada unos de los nodos es una expresión, almacenando las operaciones definidas, por ejemplo x == y.

Este almacen tiene gran utilidad, ya que nos permite enviar el árbol entre capas, recorrerlo, analizarlo, serializarlo y llegado el caso compilarlo y ejecutarlo. La potencia de este tipo se aprecia perfectamente, y como comentaba anteriormente, en Entity Framework, ya que se pueden definir consultas de recuperación de datos en una capa superior y evitar su compilación y ejecución hasta que llegue a la capa de acceso a datos.

Para definir este tipo de estructuras se usa el tipo **Expression** que genera objetos del tipo que se haya definido y no delegados como en el caso de las funciones lambda.

Un árbol de expresión se define mediante la siguiente sintaxis:

```
<p>    Expression<TDelegate> exp;</p>
```

Donde TDelegate es un delegado definido, por ejemplo, con los tipos **Func** o **Action**

Con el siguiente código

 Expression<func int="">> dobla = x => x * 2;</func>

Lo que hacemos es crear un árbol que almacena una expresión y que recibe un entero devolviendo su valor multiplicado por 2.

La limitación que existe en este tipo de declaraciones es que no se pueden almacenar expresiones lambda con cuerpo, sólo tipos de expresiones que se puedan convertir en un árbol.

Como comentábamos antes, las expresiones lambda son utilizadas ampliamente en Entity Framework. Cuando realizamos una consulta del tipo

var consulta = post.Where(p => p.User == "admin");

la cláusula **Where** tiene como parámetro una expresión lambda. La operación descrita para recuperar los posts del usuario admin se almacenará como una estructura de datos que podemos pasar entre capas como parámetro de métodos. Esta expresión la analizará y ejecutará el proveedor de datos que se está usando y la convertirá en código SQL para realizar la consulta contra la base de datos.

Además de usar los árboles de expresión como almacen de datos, también es posible ejecutarlos cuando sea necesario previa conversión a código ejecutable. La expresión vista anteriormente en la que se pasaba un parámetro de tipo entero y se obtenía el mismo entero multiplicado por 2 puede ejecutarse de la siguiente manera:

 Expression<func int="">> dobla = x => x * 2; Func<int> doblaEjecutable = doble.Commpile(); int npordos = doblaEjecutable(10); console.WriteLine(npordos);</int></func>

Con este código obtendríamos como salida por pantalla un 20.** Compile()** extrae el delegado almacenado en la expresión y genera el código ejecutable necesario para emplearlo en donde lo necesitemos.

Con esto finalizamos una breve descripción del concepto de delegado y sus posibles aplicaciones.

Espero que ayude!! Hasta luego!!



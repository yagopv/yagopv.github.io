---
layout: post
title: El diseño adaptativo (Responsive design)
date: '2012-03-19 15:20:02'
tags:
- responsive-design
---


El diseño adaptativo (“Responsive design”) persigue la creación de páginas web preparadas para visualizarse de manera adecuada en todo tipo de dispositivos, tanto ordenadores de escritorio como en dispositivos móviles.

Este objetivo se ha de lograr con la misma sitio web y no creando diferentes sitios desarrollados mediante diferentes tecnologías.

El diseño adaptativo permite mediante el desarrollo de un único sitio web abarcar múltiples tipos de dispositivos y continuar mostrando visualmente el sitio web de manera correcta, eliminando en la medida de lo posible los típicos reescalados que provocan que tengas que estar continuamenta haciendo zoom sobre cada parte de la web que te interesa visualizar.

Últimamente he leido bastantes artículos acerca del diseño adaptativo y qué beneficios aporta a la web. El concepto despertó mi interés de inmediato así que comencé a leer acerca de las diferentes posibilidades que se ofrecían a los desarrolladores para conseguir este tipo de diseños adaptados a cualquier tipo de dispositivos y comencé a explorar diferentes páginas construídas bajo estos principios, llamándome la atención la flexibilidad que ofrecían y los excelentes diseños que se pueden conseguir.

Como ejemplo podéis visitar el sitio web de [inspirationfeed](http://inspirationfeed.com/inspiration/websites-inspiration/60-examples-of-responsive-website-design/ "Ejemplos de diseño adaptativo") o [mediaqueri](http://mediaqueri.es/ "Ejemplos de diseño adaptativo") en los que se ofrecen enlaces a diferentes páginas construidas bajo este principio.

Navegando por Internet, rápidamente comencé a encontrar artículos acerca de un concepto relacionado, los **sistemas de grid** para ayuda en el diseño, comprobando la popularidad que han conseguido en los últimos años. Dado que me parece que son conceptos que están evolucionando de la mano, hablaré un poco de este tipo de sistemas en primer lugar.


## **Los sistemas de grid**

Existen múltiples sistemas de este tipo, el más popular sin duda es el creado por Nathan Smith y denominado [960 Grid System](http://960.gs/) (Su nombre viene del ancho en pixels característico de este tipo de implementaciones). El concepto al principio me resultó complicado pero en realidad no lo es.

La base es tomar como referencia una página de anchura 960 pixels y dividirla en N columnas, 10, 12, 16, 24 , etc. Las columnas tendrán huecos entre ellas con una longitud fija.

***![Grid layout](http://img.technospot.net/gird-system-sample.png "Grid layout")***

***  
***

Como vemos en la imagen (En este caso de un grid de 940px), las lineas verticales serían las columnas y entre cada columna existe un hueco. También hay una serie de cajas horizontales que serían nuestros **<div>** y que ocuparían un ancho basado en un número de columnas, este número será el que consideremos necesario para mostrar cada componente.

Por tanto, haciendo cálculos y dependiendo del número de columnas que escojamos, rápidamente obtenemos el ancho de cada columna y hueco entre ellas con lo que lo podemos definir una estructura de manera bastante rápida.

El **Framework 960**, ofrece una serie de hojas de estilo predeterminadas que contienen clases con los tamaños de las columnas y huecos.

También existen herramientas que nos muestran el grid por pantalla de la misma manera que se puede ver en la imagen y otras que nos permiten introducir los datos del grid y a partir de ellos generar una hoja de estilos adaptada a los datos introducidos.

Las hojas de estilo del** Framework 960**, como comentaba, tienen una serie de clases predeterminadas que nos permiten conceptualmente y de manera sencilla asignarlas a nuestros bloques HTML para ocupar N columnas del grid.

Supongamos la hoja de estilos de un grid de 12 columnas… la clase **.grid_1** ocupa 1 columna, la clase **.grid_2** ocupa dos columnas + el hueco que hay entre ellas, la clase **.grid_3**, tres columnas y dos huecos y así hasta la clase **.grid_12** que ocuparía 12 columnas, el ancho total.

Las clases **._grid_X ** son las clases principales en el sistema 960, pero la hoja de estilos va mucho más allá, ya que contiene otra serie de utilidades para ayudarnos en nuestro diseño. Hay clases para añadir espacio antes de cada columna (.***preffix_X***), otras para quitar espacio delante de una columna (***.suffix_X***), también tenemos otras que ocupan todo el ancho y actúan como contenedor (***.container_12***), otras que añaden huecos entre columnas …etc.

A partir de este grid abstracto, si yo quiero dividir mi diseño por ejemplo en una cabecera que ocupe todo el ancho, a continuación el contenido más una barra lateral, y por último un pié de página con todo el ancho, podría estructurarlo de la siguiente manera según las clases del grid 960

<div class="container_12"><div class="grid_12"> //Cabecera </div><div class="grid_8"> //Contenido principal </div><div class="grid_4"> //Barra lateral </div><div class="grid_12"> //Pié de página </div></div>

Las clases de la hoja de estilos de 960 serían las siguientes (Recordad que ya vienen dadas)

.container_12 { margin-left: auto; margin-right: auto; width: 960px; } .grid_4, .grid_8, .grid_12 { display: inline; float: left; margin-left: 10px; margin-right: 10px; } .container_12 .grid_4 { width: 300px; } .container_12 .grid_8 { width: 620px; } .container_12 .grid_12 { width: 940px; }

Con esto ya tendríamos montado el esqueleto de nuestra página. Las propiedades ***float:left*** son las que hacen funcionar el sistema, ya que esta propiedad hace “flotar” las cajas hacia la izquierda y cuando se quedan sin espacio comienza a mostrarlas en la fila siguiente. Tal y como hemos montado nuestro esqueleto, el primer bloque ocupa el ancho total con lo que el siguiente bloque comienza en la segunda fila. Los bloques dos y tres se muestran por tanto en la segunda fila y cuando se quedan sin espacio se pasa a la tercera fila para mostrar el pié de página.

El **Framework 960** se basa en una hoja de estilos con N clases predefinidas que ayudan a los desarrolladores en la construcción de documentos HTML bien estructurados. En el ejemplo se parte de un grid de 12 columnas, pero también se ofrece una hoja de estilos para grid de 24 columnas. Cuantos más bloques tengamos por fila, más sencillo será dividirlos usando hojas de estilo con más columnas.

Existen múltiples sistemas de grid, pero todos, en mayor o menor medida, están basados en los mismos principios que 960.

Por tanto, los sistemas de grid, son en definitiva **una ayuda** para la construcción de esqueletos de páginas web.


## **Diseño adaptativo**

En mayo de 2010, Ethan Marcotte publicó un artículo llamado “[Responsive Web Design](http://www.alistapart.com/articles/responsive-web-design/)“. El artículo habla de la necesidad de construir sitios web adaptados a la web actual, es decir, sitios que se puedan visualizar en los equipos de escritorio, tablets, móviles, etc, de manera correcta y adaptados a las particularidades de cada uno de los dispositivos. Esto, se debería intentar conseguir sin construir sitios diferentes para cada uno de los dispositivos, sino que un único sitio web “inteligente” debería adaptar su diseño dependiendo del tipo de dispositivo en el que se esté mostrando.

Una vez comprendido el concepto de **grid** y de** diseño adaptativo**, lo primero que no me casaba es que el sistema 960 está construído basado en clases de longitud fija y por tanto, ¿Cómo se puede adaptar a cualquier tipo de dispositivo un tamaño fijo? … parece que no tiene mucho sentido y lo ideal sería utilizar tamaños basados en porcentajes ¿no?.

En la web oficial de [**960 Grid System**](http://adapt.960.gs/ "Adaptación de páginas basadas en 960"), se puede ver que los autores definieron un plugin javascript (***adapt.js***) que lo que hace es modificar el tamaño del grid cuando reescalo la pantalla (imitando el comportamiento basado en porcentajes). Este reescalado se basa en apuntar a diferentes hojas de estilo en función del tamaño que se necesita, existiendo concretamente una hoja de estilos para móviles que muestra la web en una sólo columna y N filas.

Esto me parece una idea correcta depende para qué sitio web, pero en general no me gusta y no me acaba de convencer la verdad, sobre todo en la visualización de la web en la pantalla de un dispositivo móvil ya que las características de estos varían en gran medida de unos a otros.

El otro concepto de muchos sistemas de grid que no me gusta es que en muchos casos están basados en tamaños fijos como comentaba anteriormente. Me parece más adecuado el trabajo con porcentajes, ya que permiten más flexibilidad a la hora de mostrar los diseños en la mayor parte de dispositivos.

Hay sistemas tipo grid que sí que se basan en porcentajes pero al igual que 960 definen una serie de clases fijas. Esto está bien, pero lo ideal es definir nuestras propias clases y así utilizar sólo las estrictamente necesarias y con una nomenclatura descriptiva.

Para mí, lo ideal sería una combinación de un tamaño fijo de contenedor para cada uno de los dispositivos junto con valores porcentuales en el interior de los mismos con los que podamos jugar y así poder mostrar diferentes diseños de manera rápida y efectiva, pero ¿como podemos hacer para variar nuestros diseños de manera dinámica?.


## **Media queries**

Existe un ¿framework? denominado [Less Framework](http://lessframework.com/) que lo que nos ofrece es una hoja de estilos que simplemente contiene guías para la construcción de un grid y una serie de [media queries](http://www.w3.org/TR/css3-mediaqueries/) para las modificaciones necesarias para cada tipo de dispositivo además de todos los reseteos necesarios en los diferentes elementos del DOM y que para mí es un buen punto de partida para definir nuestras propias clases.

Digo lo de ¿framework? precisamente porque la hoja de estilos se ofrece como un punto de partida, y no nos dá todos los estilos ya predefinidos como en el caso de 960.

Pero ¿Qué es una **media query**?

Una **media query** permite mediante una sintaxis sencilla y clara definir estilos en nuestra hoja de estilos para diferentes resoluciones**** mediante herencia, es decir, definiré los estilos por defecto y luego una serie de media queries que modifiquen los estilos por defecto necesarios para mostrar nuestro sitio a distintas resoluciones de manera que las **media queries** de nivel inferior modifican los estilos de las definidas en niveles superiores y heredan el resto de elementos no redefinidos.

En la hoja de estilos Less, se definen 4 **media queries** que modifican los estilos por defecto del sitio. Una para Tablets, otra para dispositivos móviles, otra para dispositivos móviles con pantalla ancha y una última para dispositivos con pantalla de tipo Retina (como el iPhone 4).

Por supuesto, podemos añadir todas las **media queries** que queramos, pero con esto creo que sería suficiente como punto de partida.

Las media queries forman parte de **CSS 3**, con lo que por supuesto no tendrán efecto en navegadores antiguos que no soporten la nueva sintaxis. De toda maneras existen plugins que permiten capear con este “defecto” como por ejemplo ***respond.js ***que nos permite activar esta característica en navegadores antiguos.

En fin, después de hablar de todos estos conceptos, la mejor manera de aclararlo todo es mediante un ejemplo que vamos a ver [en este otro post](../../../Post/GetPostByCode/creando_una_pagina_web_mediante_dise%C3%B1o_adaptativo_%28responsive_design%29 "Creando un página web mediante diseño adaptativo (responsive design)") y que nos llevará a crear una página web [como esta](../../../Links/FlexibleLayout/flexiblelayout.html "Diseño adaptativo") (Modificad el tamaño de la ventana del navegador para ver como la página adapta sus contenidos a diferentes resoluciones), os invito a leerlo !!

Hasta pronto!!



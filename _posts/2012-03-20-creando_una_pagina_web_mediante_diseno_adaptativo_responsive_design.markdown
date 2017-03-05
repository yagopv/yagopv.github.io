---
layout: post
title: Creando una página web mediante diseño adaptativo (Responsive design)
date: '2012-03-20 03:08:30'
tags:
- responsive-design
---


En un [post anterior](http://yagoperez.com/Post/GetPostByCode/el_dise%C3%B1o_adaptativo_responsive_design), intentaba explicar qué era el diseño adaptativo y qué beneficios nos aportaba como desarrolladores.

Ahora toca mostrar un ejemplo de cómo se puede conseguir y comprobar lo bien que se adapta a diferentes dispositivos de manera que nos evitamos desarrollar una aplicación para escritorio, otra para tablets y otra para móviles.

Un poco más de esfuerzo en el diseño puede hacernos ahorrar tiempo más adelante.

La página que vamos a diseñar se mostrará de la siguiente manera a diferentes resoluciones (y por tanto en diferentes dispositivos).

**FIREFOX**

[![9aafb4af-35f2-4a52-a709-db4065e9a549_flexibleLayout_firefox](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/9aafb4af-35f2-4a52-a709-db4065e9a549_flexibleLayout_firefox-219x300.jpg)](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/9aafb4af-35f2-4a52-a709-db4065e9a549_flexibleLayout_firefox.jpg)

**TABLET**

[![6eb089e4-af1b-46eb-8f3f-946ba7df8297_flexibleLayout_tablet](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/6eb089e4-af1b-46eb-8f3f-946ba7df8297_flexibleLayout_tablet-244x300.jpg)](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/6eb089e4-af1b-46eb-8f3f-946ba7df8297_flexibleLayout_tablet.jpg)

**MOVIL**

[![caf96b15-440b-446e-807a-a46712106c0d_flexibleLayout_mobile](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/caf96b15-440b-446e-807a-a46712106c0d_flexibleLayout_mobile-129x300.jpg)](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/caf96b15-440b-446e-807a-a46712106c0d_flexibleLayout_mobile.jpg)


## Comenzamos

En primer lugar vamos a crear una página llamada***flexibleLayout.html***y a escribir elHTMLnecesario para mostrar el contenido deseado

Vamos a usar de paso un poco deHTML5, concretamente***<section>***y***<nav>******.***

   <meta charset="utf-8" /> <title>Diseño adaptativo</title> <meta name="viewport" content="width=device-width, initial-scale=1,maximum-scale=1">   <section class="header-left"> <h2>Lorem ipsum dolor sit amet, consectetur adipiscing elit</h3> </section> <section class="header-right"> <h2>Lorem ipsum dolor sit amet, consectetur adipiscing elit</h3> </section> <section class="navigation margin-top"> <nav> <a href="#">menuLink1</a> <a href="#">menuLink2</a> <a href="#">menuLink3</a> <a href="#">menuLink4</a> <a href="#">menuLink5</a> </nav> </section> <section class="main-left margin-top"> <p> Lorem ipsum dolor sit amet, consectetur adipiscing elit. Aliquam interdum ante sed magna consequat dictum. Pellentesque habitant morbi tristique senectus et netus et malesuada fames ac turpis egestas. Aliquam erat volutpat. Maecenas at diam nisi, et imperdiet magna. Mauris nec fermentum est. Maecenas ultricies suscipit risus, commodo pretium leo tincidunt in. Pellentesque habitant morbi tristique senectus et netus et malesuada fames ac turpis egestas. Maecenas sagittis purus sit amet nibh fermentum dapibus. Ut viverra molestie gravida. Vivamus augue arcu, posuere et adipiscing quis, cursus et arcu. Praesent egestas tincidunt odio, mollis mollis felis pretium at. Sed lobortis enim nec purus luctus vitae cursus eros suscipit. Maecenas eros sem, fringilla a rutrum eget, fringilla feugiat nulla. Sed tincidunt erat ac quam suscipit viverra euismod purus eleifend. Nullam arcu est, fringilla at dapibus ac, fringilla non odio. </p> </section> <section class="main-right margin-top"> <p> Lorem ipsum dolor sit amet, consectetur adipiscing elit. Aliquam interdum ante sed magna consequat dictum. Pellentesque habitant morbi tristique senectus et netus et malesuada fames ac turpis egestas. Aliquam erat volutpat. Maecenas at diam nisi, et imperdiet magna. Mauris nec fermentum est. Maecenas ultricies suscipit risus, commodo pretium leo tincidunt in. Pellentesque habitant morbi tristique senectus et netus et malesuada fames ac turpis egestas. Maecenas sagittis purus sit amet nibh fermentum dapibus. Ut viverra molestie gravida. Vivamus augue arcu, posuere et adipiscing quis, cursus et arcu. Praesent egestas tincidunt odio, mollis mollis felis pretium at. Sed lobortis enim nec purus luctus vitae cursus eros suscipit. Maecenas eros sem, fringilla a rutrum eget, fringilla feugiat nulla. Sed tincidunt erat ac quam suscipit viverra euismod purus eleifend. Nullam arcu est, fringilla at dapibus ac, fringilla non odio. </p> </section> <section class="submain-left margin-top"> <p class="small"> Lorem ipsum dolor sit amet, consectetur adipiscing elit. Aliquam interdum ante sed magna consequat dictum. Pellentesque habitant morbi tristique senectus et netus et malesuada fames ac turpis egestas. Aliquam erat volutpat. Maecenas at diam nisi, et imperdiet magna. Mauris nec fermentum est. Maecenas ultricies suscipit risus, commodo pretium leo tincidunt in. </p> </section> <section class="submain-middle-left margin-top"> <p class="small"> Lorem ipsum dolor sit amet, consectetur adipiscing elit. Aliquam interdum ante sed magna consequat dictum. Pellentesque habitant morbi tristique senectus et netus et malesuada fames ac turpis egestas. Aliquam erat volutpat. Maecenas at diam nisi, et imperdiet magna. Mauris nec fermentum est. Maecenas ultricies suscipit risus, commodo pretium leo tincidunt in. </p> </section> <section class="submain-middle-right margin-top"> <p class="small"> Lorem ipsum dolor sit amet, consectetur adipiscing elit. Aliquam interdum ante sed magna consequat dictum. Pellentesque habitant morbi tristique senectus et netus et malesuada fames ac turpis egestas. Aliquam erat volutpat. Maecenas at diam nisi, et imperdiet magna. Mauris nec fermentum est. Maecenas ultricies suscipit risus, commodo pretium leo tincidunt in. </p> </section> <section class="submain-right margin-top"> <p class="small"> Lorem ipsum dolor sit amet, consectetur adipiscing elit. Aliquam interdum ante sed magna consequat dictum. Pellentesque habitant morbi tristique senectus et netus et malesuada fames ac turpis egestas. Aliquam erat volutpat. Maecenas at diam nisi, et imperdiet magna. Mauris nec fermentum est. Maecenas ultricies suscipit risus, commodo pretium leo tincidunt in. </p> </section> <section class="footer margin-top"> <footer> <p>@Copyright 2012 - <em>Yago Perez Vazquez</em></p> </footer> </section>  

Hasta aquí, nada especial aparte deHTMLnormal y corrientey un montón de clases cuyo nombre indica qué tipo de contenido se mostrará y la situación del mismo.La situación de cada clase se muestra en las imágenes superiores. Lo más importante tal vez de la estructura sea el meta tag***viewport:***

<meta name="viewport" content="width=device-width, initial-scale=1,maximum-scale=1">

Si no incluimos este meta tag, todos nuestros esfuerzos no se verán recompensados ya que los dispositivos móviles, por defecto, tomarán el ancho máximo de la páginaHTMLy lo reescalarán para que se muestre al completo, por lo que lo único que conseguiremos es que se vea de la misma manera que a toda resolución pero reescalado a la pantalla del dispositivo móvil como se puede apreciar a continuación:

[![5766b566-d4eb-4832-98e2-9fbec5017923_flexibleLayout_mobile_no_viewport](http://yagoperez.azurewebsites.net/wp-content/uploads/2012/03/5766b566-d4eb-4832-98e2-9fbec5017923_flexibleLayout_mobile_no_viewport-211x300.jpg)](http://yagoperez.azurewebsites.net/wp-content/uploads/2012/03/5766b566-d4eb-4832-98e2-9fbec5017923_flexibleLayout_mobile_no_viewport.jpg)



Indicando que***width=device-width***hace que el ancho inicial no se reescale y se considere que es el ancho de la pantalla del dispositivo.

Por lo demás, vemos que estamos aplicando clases a cada***<section>***que definencada uno de los bloques***header-left,header-right,***… Algunas clases***margin-top***porque queremos separan un poco cada una de nuestras secciones y alguna clase***small***para mostrar el texto en letra más pequeña

Siabrimos nuestra página en el navegador veremos lo siguiente:

[![58c1a1fd-ee9d-46ab-ad63-35db753e55c3_flexibleLayout_1](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/58c1a1fd-ee9d-46ab-ad63-35db753e55c3_flexibleLayout_1-300x234.jpg)](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/58c1a1fd-ee9d-46ab-ad63-35db753e55c3_flexibleLayout_1.jpg)

Un simple documento sin estilos ….


## Creando nuestra hoja de estilos

Ahora vamos a crear y linkar una hoja de estilos***styles.css***que guardaremos en una carpeta**css**al mismo nivel que la páginaHTML***flexiblelayout.html.***

Para ello añadimos en nuestro******el link a la hoja de estilos


## Añadiendo estilos a la hoja

Lo primero que vamos a hacer en nuestra nueva hoja de estilos es resetear todos los estilos de los componentes principales:

/* Reseteamos los tags HTML y HTML5 */ html, body, div, span, object, iframe, h1, h2, h3, h4, h5, h6, p, blockquote, pre, a, abbr, address, cite, code, del, dfn, em, img, ins, kbd, q, samp, small, strong, sub, sup, var, b, i, hr, dl, dt, dd, ol, ul, li, fieldset, form, label, legend, table, caption, tbody, tfoot, thead, tr, th, td, article, aside, canvas, details, figure, figcaption, hgroup, menu, footer, header, nav, section, summary, time, mark, audio, video { margin: 0; padding: 0; border: 0; } article, aside, canvas, figure, figure img, figcaption, hgroup, footer, header, nav, section, audio, video { display: block; } a img {border: 0;}

[![b48e251a-3ace-4e82-ac9c-0d620912a6ed_flexibleLayout_2](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/b48e251a-3ace-4e82-ac9c-0d620912a6ed_flexibleLayout_2-300x279.jpg)](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/b48e251a-3ace-4e82-ac9c-0d620912a6ed_flexibleLayout_2.jpg)



Hasta aquí no hemos avanzado en nada y la visualización sería la misma, pero los reseteos son una buena práctica para evitar males posteriores.

A continuación añadimos una serie de clases para definir el tipo de fuente y los tamaños a utilizar:

/* Incluimos los valores por defecto de las fuentes para texto */ /* Incluimos los valores por defecto de las fuentes para texto */ .gigantic { font-size: 110px; line-height: 120px; letter-spacing: -2px; } .huge, h1 { font-size: 68px; line-height: 72px; letter-spacing: -1px; } .large, h2 { font-size: 42px; line-height: 48px; } .bigger, h3 { font-size: 26px; line-height: 36px; } .big, h4 { font-size: 22px; line-height: 30px; } body { font: 16px/24px Georgia, serif; } .small, small { font-size: 13px; line-height: 18px; }



Aunque no vayamos a usar todos estos estilos definimos tamaños de fuente para todos los posibles headings además de clases que se puedan usar en marcas que no sean del tipo heading como podría ser un**<p>**

Ahora lo que hemos conseguido es darle algo de estilo a las fuentes de nuestra página web. Por ahora nada novedoso tampoco:

[![a1e3c5c3-32a7-4cfe-b1f3-bbe9babf7305_flexibleLayout_3](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/a1e3c5c3-32a7-4cfe-b1f3-bbe9babf7305_flexibleLayout_3-300x178.jpg)](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/a1e3c5c3-32a7-4cfe-b1f3-bbe9babf7305_flexibleLayout_3.jpg)




## Definiendo nuestro Layout

Como siguiente paso,vamos a definir el ancho por defecto de nuestra página web. Además indicamos que se centre el contenido (***margin-left, margin-right***) y que se establezca un espacio respecto a los bordes (***padding***),***background***blanco, etc…

/* Marcamos el ancho de nuestra página por defecto. Serán 992 pixeles */ body { width: 992px; padding: 72px 48px 84px; margin-left: auto; margin-right: auto; background: #FFFFFF; color: #888888; -webkit-text-size-adjust: 100%; /* Para que Safari no haga auto-zoom*/ }

y un poco de estilo para nuestras cajas***<section>***

/* Las diferentes sections las definimos para que su posición sea relativa y que el color de fondo sea un color expresado como gradiente mediante las posibilidades que nos ofrece CSS 3 Importante!!. Definimos los border-box (border de las setions), de manera que no ocupen espacio y se conviertan en un quebradero de cabeza adicional. Por tanto podremos calcular nuestras columnas basándose en el 100% de espacio como el total */ section { background: #feffff; /* Para antiguos navegadores */ background: -moz-linear-gradient(top, #feffff 0%, #d2ebf9 100%); /* FF3.6+ */ background: -webkit-gradient(linear, left top, left bottom, color-stop(0%,#feffff), color-stop(100%,#d2ebf9)); /* Chrome,Safari4+ */ background: -webkit-linear-gradient(top, #feffff 0%,#d2ebf9 100%); /* Chrome10+,Safari5.1+ */ background: -o-linear-gradient(top, #feffff 0%,#d2ebf9 100%); /* Opera 11.10+ */ background: -ms-linear-gradient(top, #feffff 0%,#d2ebf9 100%); /* IE10+ */ background: linear-gradient(top, #feffff 0%,#d2ebf9 100%); /* W3C */ filter: progid:DXImageTransform.Microsoft.gradient( startColorstr='#feffff', endColorstr='#d2ebf9',GradientType=0 ); /* IE6-9 */ position:relative; border: 2px solid #888888; -webkit-box-sizing: border-box; -moz-box-sizing: border-box; box-sizing: border-box; -moz-border-radius: 5px; border-radius: 5px; -moz-box-shadow: 10px 10px 5px #888; -webkit-box-shadow: 10px 10px 5px #888; box-shadow: 10px 10px 5px #888; } /* Cuando pasamos el ratón sobre una sección se modifica la opacity al 50 por ciento */ section:hover { opacity:0.5; } /* Cada uno de los párrafos y títulos tendran un padding del 4% respecto a la sección exterior que los contiene */ h2, p { padding:4%; } /* Define los margenes entre cada fila y la fila inmediatemente superior */ .margin-top { margin-top: 4%; }

Estamos estableciendo un gradiente para el fondo y estableciendo su posición como relativa por si queremos posicionar elementos de manera absoluta en su interior. Además definimos el borde de manera que no ocupe espacio. Esto evita muchos quebraderos de cabeza ya que de otra manera tendríamos que tener en cuenta el ancho del borde en nuestros cálculos posteriores.

border: 2px solid #888888; -webkit-box-sizing: border-box; -moz-box-sizing: border-box; box-sizing: border-box; -moz-border-radius: 5px;

También establecemos que cuando el ratón pase por encima de una***<section>***su opacidadvarie en un 50% y definimos la separación entre las***<section>***(***.****margin-top***) y entre las***<section>***y su contenido (***<h2>,<p>***)

[![2d17c963-ba0e-4f24-b1d8-0a19d7f00ad0_flexibleLayout_4](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/2d17c963-ba0e-4f24-b1d8-0a19d7f00ad0_flexibleLayout_4-300x171.jpg)](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/2d17c963-ba0e-4f24-b1d8-0a19d7f00ad0_flexibleLayout_4.jpg)



Ya se vé un poco mejor!!Vamos con los serio.


## Calculando los anchos de las <section>

En realidad lo que queremos es que nuestra página se muestre de manera que existan dos cabeceras, una a izquierda (***header-left***) y otra a la derecha (***header-right***), luego un menu (***navigation***), a continuación dos secciones con contenidos (***main-left, main-right***) más 4 subsecciones debajo de ellas (***submain-left, submain-middle-left, submain-middle-right, submain-right***) cerrando el esquema con un pié de página (***f****ooter***).

El cálculo se hace de manera similar para todas las***<section>***. Por ejemplo, para la cabecera y contenidos principales se va a mostrar una***<section>***al lado de la otra. Añadiremos el siguiente estilo

.header-left, .header-right, .main-left, .main-right { width: 46%; float:left; margin-left: 2%; margin-right: 2%; }

El ancho de cada***<section>***lo establecemos en un 46% y los márgenes de cada una con izquierda y derecha serán de un 2%. Además estableceremos que floten a la izquierda,con lo que el cálculo sería

El ancho de cada***<section>***lo establecemos en un 46% y los márgenes de cada una con izquierda y derecha serán de un 2%. Además estableceremos que floten a la izquierda,con lo que el cálculo sería

```
<p>46% (header-left)  + 46% (header-right) + 2% (margin-left de header-left )  + 2% (margin-right de header-left )   + 2% (margin-left de header-right ) + 2% (margin-right de header-right ) = 100% de espacio total de la fila, que a su vez contiene 2 columnas y un hueco entre ellas.</p>
```

Como hemos hecho que los bordes no ocupen espacio este cálculo ya nos vale. Si no lo hubiésemos hecho de esta manera, entonces habría que tener en cuenta el espacio que ocupan estos bordes o de otra manera***header-right**y**main-right***se mostrarían en la fila siguiente.

Para las cuatro subsecciones el cálculo es similar

.submain-left, .submain-middle-left, .submain-middle-right, .submain-right { width: 21%; float:left; margin-left: 2%; margin-right: 2%; }

```
<p>(21% *  4 secciones) + ((2% + 2%) * 3  de espacio entre cada sección) + (2% + 2% de margen de la primera y última sección  con la izquierda y la derecha respectivamente) = 100% de espacio total de la fila, que contiene 4 columnas + 3 huecos.</p>
```

¿Sencillo no?

Para la barra de navegación y pié de página, el espacio será la fila entera

.navigation, .footer { width: 96%; float:left; padding: 2%; margin-left:2%; margin-right:2%; }



Ocuparán el ancho total con un pequeño porcentaje de margen con las esquinas.

En labarra de navegación aparecerán 5 botones de link con lo que crearemos una subgrid de links en el interior de la***<section>***siguiendo las mismas directrices.

Comentar que para porcentajes en los elementos hijos de cualquier elemento del DOM,**el padre ha de tener también un ancho definido**. Hemos definido el ancho de la***<section>***que contiene el menu, por lo que podemos aplicar porcentajes a los elementos del menu.

Además queremos que al pasar el ratón por encima de un link se muestre en rojo y negrita

nav a { display:inline-block; color: #999999; width: 16%; float:left; margin-left: 2%; margin-right: 2%; } nav a:hover { color: red; font-weight:bold; }

Después de aplicar todos estos estilos, la página luce así

[![a4124c3d-6717-4c4d-8715-a1196be1eae7_flexibleLayout_5](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/a4124c3d-6717-4c4d-8715-a1196be1eae7_flexibleLayout_5-252x300.jpg)](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/a4124c3d-6717-4c4d-8715-a1196be1eae7_flexibleLayout_5.jpg)



Ahora la página ya se muestra como buscábamos. Eso sí, de adaptación según reescalemos el tamaño del navegador nada.

Como hemos aplicado porcentajes el único reescalado que se hace es a partir de 992px,que es el ancho que indicamos para el tag**********. Para anchos menores de 992px se aplica la norma***width:992px;***y la página no se adapta.


## **Media queries al rescate**

El único trabajo que queda por hacer es crear una serie de[**media queries**](http://www.w3.org/TR/css3-mediaqueries/ "Media queries")que modifiquen nuestro diseño cuando el ancho de la pantalla se modifica o bien se muestre en un dispositivo móvil.

Podemos crear el número de**media queries**que queramos, pero con las que vamos a definir debería bastar en la mayoría de los casos.

Por ejemplo, lo primero que me viene en la cabeza es mi iPad o cualquier tablet. El reescalado que hace sin añadir nada más en nuestra hoja de estilos es bastante bueno y se deja ver bien, pero tal vez se podría mejorar un poquito haciendo que las 4 secciones de subcontenidos que se muestran en una sóla fila, se muestren en dos. Sería más legible.

Bien, pues vamos a incluir una**media query**que permita esta adaptación

@media only screen and (min-width: 768px) and (max-width: 991px) { body { width: 768px; padding: 48px 28px 60px; } .submain-left, .submain-middle-left, .submain-middle-right, .submain-right { width: 46%; } }



El iPad trabaja, creo recordar que a 796px, por lo que creamos una**media query**que modifica los estilos para los rangos que van entre 768px y 991px y así de paso cubrimos un buen número de tablets.

Lo que vamos a modificar es el ancho de nuestro******, el***padding***con el exterior, y vamos a hacer que nuestras subsecciones se comporten de manera similar que nuestros***header-left***y***header-righ****t***o***main-left***y***main-right***. Por tanto con modificar el ancho al 46%, de la misma manera que en las otras***<section>***, es suficiente.

El resto de estilos se heredan de los estilos por defecto que hemos ido definiendo en nuestra hoja de estilos.

¿Es posible que sea suficiente con esto? … Vamos a ver como queda ahora nuestra aplicación si reescalo la pantalla a una resolución con un ancho entre 768px y 991px



[![6d846aa7-7b8f-474f-b566-a05016afdf83_flexibleLayout_6](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/6d846aa7-7b8f-474f-b566-a05016afdf83_flexibleLayout_6-300x236.jpg)](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/6d846aa7-7b8f-474f-b566-a05016afdf83_flexibleLayout_6.jpg)



Perfecto!!,es lo que buscábamos …. Pues vamos a por más dispositivos …

El siguiente dispositivo en el que pienso es por supuesto mi teléfono, un HTC Mozart con una resolución estándar de 320px de ancho (creo). Lo cierto es que hay una gama de dispositivos bastante importante que trabajan más o menos a esta resolución … En fin, vamos a crear otra**media query**que se adapte a cualquier rango que vaya desde 0 hasta nuestro rango creado paratablets, es decir, que trabajen con un máximo de 767px, el siguiente pixel ya es para el tablet y se aplica la primera**media query**que hemos definido.

¿Y qué quiero que pase cuando se está mostrando mi página web en un móvil?. Bueno … lo único que se me ocurre es que se muestre todo en una columna, es la mejor manera de ver bien los contenidos y además lo que se suele hacer ¿verdad?.

Para conseguir esto, es suficiente con hacer que todas las secciones ocupen un ancho de 100% (96% mejor dicho, acordémosnos de los margenes!!) . Además de establecer nuestro ancho de******en 320px por defecto y modificar un poco el***padding***de paso.

Además, a nuestro***header-right***no le habíamos añadido un margen con el borde superior, ya que se mostraba en la primera fila, por tanto vamos a añadírselo también para separarlo de su ex-hermano***header-left***.

/* Con esta media query, se redefinen los estilos de la página para que se muestren en móviles. El ancho para el que se redefinirán los estilos mediante esta media query está marcado por un máximo de 767px */ @media only screen and (max-width: 767px) { body { width: 320px; padding: 48px 34px 60px; } .header-left, .header-right, .main-left, .main-right { width: 96%; } .header-right { margin-top:4%; } .submain-left, .submain-middle-left, .submain-middle-right, .submain-right { width: 96%; } }

y ahora ¿cómo se verá?

[![f50d1e20-962b-4dc0-99c6-e126c949c506_flexibleLayout_7](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/f50d1e20-962b-4dc0-99c6-e126c949c506_flexibleLayout_7-183x300.jpg)](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/f50d1e20-962b-4dc0-99c6-e126c949c506_flexibleLayout_7.jpg)



Vaya … perfecto!! … ya lo hemos conseguido!!, nos hemos cargado el menu de navegación!!.

La página se muestra en una columna, pero el menú se ha quedado sin espacio y es ilegible.

Para arreglar esto podemos hacer varias cosas …Podemos cambiar los links de sitio, mostrarlos en un par de filas, aplicar un plugin jquery que haga que cuando se queden sin espacio se abra un submenú a partir del último link al pasar el ratón por encima, etc…

Por simplicidad, lo que vamos a hacer es prescindir de un par de links del menu para que todo vuelva a su sitio, así que añadimos en la media query

nav a:nth-child(4), nav a:nth-child(5) { display: none; } nav a { width: 29%; }

y comprobamos como ha quedado …

[![b8ba1a6f-2985-430e-a0de-8092571de1ba_flexibleLayout_8](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/b8ba1a6f-2985-430e-a0de-8092571de1ba_flexibleLayout_8-172x300.jpg)](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/b8ba1a6f-2985-430e-a0de-8092571de1ba_flexibleLayout_8.jpg)



Ahora sí que se ve todo en una columna y como buscábamos …

¿Qué más podríamos hacer? … Hay ciertos dispositivos móviles, como el Samsung Galaxy que tienen pantallas anchas de 480px. Esta pantalla es suficientemente ancha como para permitirnos mejorar un poco nuestro diseño. Podríamos en lugar de mostrar todo en una columna, en el caso de las subsecciones de contenido, mostrarlas de dos en dos.

¿Qué**media query**aplicaríamos? La anterior ya establecía que para cualquier dispositivo móvil de un máximo de 767pxse mostrase todo en una sóla columna … ¿Cómo puedo modificarlo mediante otra query?.

Como comentábamos, las**media queries**heredan de los estilos definidos en niveles superiores de la hoja de estilos, por lo que podemos crear una media query para dispositivos que estén entre 480px y 767px, es decir, con pantalla ancha. Se herederán todos los estilos que hacen que se muestre a una columna, ya que la media query anterior se procesará, y además definiremos nuevos estilos en caso de que la pantalla sea de 480px como mínimo, redefiniendo así los estilos aplicados por la**media query**para móviles de pantalla estándar.

@media only screen and (min-width: 480px) and (max-width: 767px) { body { width: 480px; padding: 36px 22px 48px; } .submain-left, .submain-middle-left, .submain-middle-right, .submain-right { width: 46%; } nav a:last-child { display: none; } nav a:nth-child(4) { display: inline-block; } nav a { width: 21%; } }

Con esta media query, que además recupera uno de los links del menu que ya se vería bien, mostraríamos nuestra página en cualquier dispositivo tipo Samsung Galaxy con pantalla de 480px de ancho hasta 767px

[![e8ef091d-2910-4eac-8484-28b2072835f5_flexibleLayout_9](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/e8ef091d-2910-4eac-8484-28b2072835f5_flexibleLayout_9-244x300.jpg)](http://yagoperez.azurewebsites.net/wp-content/uploads/2014/01/e8ef091d-2910-4eac-8484-28b2072835f5_flexibleLayout_9.jpg)



Perfecto!!

Con todas esta**media queries**cubriríamos un rango de dispositivos muy amplio. He probado esta hoja de estilos en un iPad 3,HTC Wildfire, HTC Mozart, Samsung Eee Pad, Kindle Fire además de en sobremesas usando Firefox 10, Chrome y Explorer 9 … y se ve perfectamente en cualquiera de ellos. Puedes probar tú mediante el[enlace de ejemplo](http://yagoperez.com/Links/FlexibleLayout/flexiblelayout.html "Ejemplo de diseño adaptativo")o bien[descargarte la página web y la hoja de estilos aquí](http://yagoperez.com/Content/Files/26eed8d6-c071-467a-9cfe-31fe685b15d8_flexibleLayout.zip).

Verás que cuando reescalamos el navegador la página adapta su contenido según hemos definido.

Con un poco de esfuerzo hemos conseguido una página que se adapta y se muestra perfectamente en cualquier disposito con lo que… uy!! vaya, me olvidaba… Ahora resulta que están sacando pantallas con un ratio de pixels por pulgada superior al que se solía usar… como la pantalla Retina del iPhone 4 con 326 pixels opr pulgada. ¿Se ha fastidiado el diseño? … no, pese a que se sigue viendo correctamente tal y como está, puede que para este tipo de pantallas queramos hacer alguna pequeña modificación por las nuevas capacidades que se nos ofrecen, basta con añadir una**media query**para pantallas de este tipo y listo

/* Sobreescribe los estilos para dispositivos que usen pantalla de tipo Retina como es el caso del iPhone 4 */ @media only screen and (-webkit-min-device-pixel-ratio: 2), only screen and (min-device-pixel-ratio: 2) { body { } }



¿Te animas a escribirla?

Hasta pronto!!





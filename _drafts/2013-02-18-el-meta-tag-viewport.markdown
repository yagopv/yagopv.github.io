---
layout: post
title: El meta tag viewport
date: '2013-02-18 06:46:36'
tags:
- responsive-design
---


Hasta hace bien poco, la mayoría de páginas web habían sido creadas sin ningún pensamiento acerca de cómo se mostrarían en tamaños de pantalla más pequeños. <span style="font-size: 1em; line-height: 1.6em;">Los navegadores para dispositivos móviles han estado durante mucho tiempo luchando para tratar de mostrar correctamente estas páginas al usuario final.</span>

Los diseños cuyo foco principal es el contenido de texto, son más sencillos de reformatear que aquellos con interfaces visualmente complejas. Estos últimos suelen acabar funcionando mediante el zoom manual para poder ver el contenido de manera correcta.

Cómo la mayoría de los sitios no han sido diseñados para un correcto reescalado, los navegadores móviles normalmente recurren a la solución del zoom para visualizar las páginas web.

La solución al problema es comunicarle al navegador cuales son las dimensiones de nuestro diseño para que no tenga que suponer nada por sí mismo. Con este fin usamos el **viewport**.

Con frecuencia, estos **viewports** se usan en páginas que están específicamente diseñadas para visualizarse a una resolución concreta, bien mediante detección del tamaño o bien mediante entrada manual por parte del usuario. Por ejemplo, un diseño adaptado a pantallas de 320 píxeles debería estar acompañado por la definición del viewport entre las etiquetas.

```
<meta content="width=320" name="viewport"></meta>
```

Esta definición funciona correctamente para vistas específicas pero no se adaptará a tamaños mayores de forma correcta debido a que precisamente hemos comunicado al navegador que el **viewport** ha de ajustarse a 320. La mejor solución es crear nuestros estilos CSS de forma que se adapten correctamente a diferentes tamaños (Por ejemplo mediante el uso de media queries) y usar el **viewport** para comunicarle al navegador que el tamaño será el que el dispositivo soporte. Esto lo podemos hacer de manera muy sencill

```
<meta content="width=device-width" name="viewport"></meta>
```

<span style="font-size: 1em; line-height: 1.6em;">En nuestra hoja CSS tendríamos los estilos específicos para los diferentes dispositivos, que serán normalmente desktop, tablet y móvil.</span>

<span style="font-size: 1em; line-height: 1.6em;">Hasta pronto !!</span>



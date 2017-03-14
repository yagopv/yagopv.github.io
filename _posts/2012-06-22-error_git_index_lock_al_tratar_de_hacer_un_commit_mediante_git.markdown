---
layout: post
title: Error ".git/index.lock" al tratar de hacer un commit mediante git
date: '2012-06-22 03:16:23'
tags:
- git
categories:
- Source Control
---

Un error muy típico al tratar de hacer un commit mediante git es que nos indique que no puede crear el archivo .git/index.lock porque ya existe.

Varias veces al tratar de hacer un **commit** mediante **git** me ha salido el siguiente error:

```javascript
fatal: unable to create '.git/index.lock': File exists
```

Es posible que si estás usando alguna herramiento como [Github For Windows](http://windows.github.com/ "Github for windows") o [GitExtensions](http://code.google.com/p/gitextensions/ "Git Extensions"), el error no sea tan claro y por el contrario me esté indicando otra cosa o bien me inste a abrir una consola y depurar el repositorio.

Si estoy recibiendo algún tipo de error que no identifico, siempre puedo abrir una consola y ejecutar el comando **git commit**. Es en este momento cuando puedo recibir este error explicitamente.

Su solución es sencilla, simplemente he de ir a la raiz del proyecto, entrar en la carpeta **git** y borrar el archivo **index.lock**, tal y como se indica en el error.

A partir de ahí ya puedo hacer el **commit** sin problemas …

Hasta pronto!!
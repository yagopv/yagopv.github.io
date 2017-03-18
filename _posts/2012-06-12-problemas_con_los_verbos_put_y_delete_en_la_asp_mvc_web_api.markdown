---
layout: post
title: Problemas con los verbos PUT y DELETE en la  ASP MVC Web API
date: '2012-06-12 03:55:43'
tags:
- asp mvc
categories:
- .NET
---

Es posible que al usar la Web API con Visual Studio y tratar de procesar peticiones mediante los verbos PUT o DELETE el servidor me indique que el recurso no se ha encontrado (404 Not Found) o que dichos verbos no están permitidos (405 Not Allowed).

Es posible que al intentar trabajar con la *Web API* en Visual Studio experimentes algún tipo de problema con las peticiones *PUT* y *DELETE*. Concretamente que se rechacen las peticiones de este tipo debido a un *404 Not Found* o un *405 Not Allowed.*

En mi caso, con Visual Studio 2012, estos problemas se resolvieron incluyendo las siguientes líneas en el *web.config …*

```xml
<system.webserver>
    <modules runallmanagedmodulesforallrequests="true">
    <remove name="WebDAVModule"></remove></modules></system.webserver>
```

… ya que al parecer el módulo *WebDav* estaba interfiriendo en dichas peticiones.

Como digo, esto es en mi caso, pero es posible que aún así sigas experimentando problemas si usas **IIS 8** ya que la configuración por defecto al realizar la instalación de Visual Studio 2012 podría estar bloqueando las peticiones *PUT* y *DELETE*. De esto se habla en el siguiente [enlace](http://stackoverflow.com/questions/10906411/asp-net-web-api-put-delete-verbs-not-allowed-vs2012rc-iis-8-express "Problema 404 con PUT & DELETE en la Web API") en el que se indica como modificar el archivo *applicationhost.config* para que se permitan *PUT* y *DELETE* con **IIS8.**

Hasta pronto!!



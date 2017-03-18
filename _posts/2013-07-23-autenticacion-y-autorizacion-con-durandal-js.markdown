---
layout: post
title: Autenticación y autorización con Durandal JS
date: '2013-07-23 07:32:07'
tags:
- Durandal
categories:
- Frontend
---

En las últimas semanas he estado intentando crear una plantilla de inicio para aplicaciones Web API/Durandal que incluya el sistema de autenticación y autorización de la plantilla que incluye Visual Studio 2012 para ASP MVC 4 (Internet).

La plantilla la comparto en [mi cuenta de Github](https://github.com/yagopv/durandalauth) y también he creado un [sitio web de ejemplo](https://durandalauth.azurewebsites.net/#/ "DurandalAuth sample site") en Azure.

Algunas de las características que se incluyen son

1. Autorización y Autenticación del lado servidor usando SimpleMembership
2. Autenticación y Autorización del lado cliente (Recordar siempre acompañarla con los respectivos [Authorize] en el servidor ya que el cliente está en poder del usuario final
3. 3 capas (Web + Dominio + Data)
4. Implementación del patrón repositorio y UnitOfWork en la capa de acceso a datos
5. Protección contra ataques CRSF mediante anti forgery tokens
6. Validación del modelo en cliente (knockout validation) y en el servidor

La maquetación usa bootstrap [(plantilla Cosmo de bootswatch.com)](http://bootswatch.com/cosmo/ "Cosmo template") por defecto

A ver si a alguien que quiera iniciarse con Durandal le resulta de utilidad :)

Hasta pronto!!



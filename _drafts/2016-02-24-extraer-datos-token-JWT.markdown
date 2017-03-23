---
layout: post
title: Extraer datos de un token JWT
date: '2016-02-24 13:44:16'
tags:
- snippets
categories:
- JavaScript
---

A veces necesitamos extraer los datos públicos de un token JWT. Podemos hacerlo de forma sencilla con una función como esta

```javascript
const parseJWT(token) => {
   var segments = token.split(".");
   if (!segments instanceof Array || segments.length !== 3) {
      throw new Error("Invalid JWT");
   }
   var claims = segments[1];
   return JSON.parse(decodeURIComponent(escape(window.atob(claims))));
}
```
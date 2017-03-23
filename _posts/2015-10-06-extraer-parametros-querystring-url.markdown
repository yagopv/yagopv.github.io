---
layout: post
title: Extraer datos de un token JWT
date: '2016-02-24 13:44:16'
tags:
- snippets
categories:
- Frontend
---

La siguiente función autoejecutable es bastante útil para extraer los parámetros de un Querystring

```javascript
var QueryString = function () {
  var query_string = {};
  var query = window.location.search.substring(1);
  var vars = query.split("&");

  for (var i=0;i<vars.length;i++) {
    var pair = vars[i].split("=");

    if (typeof query_string[pair[0]] === "undefined") {
      query_string[pair[0]] = decodeURIComponent(pair[1]);
    } else if (typeof query_string[pair[0]] === "string") {
      var arr = [ query_string[pair[0]],decodeURIComponent(pair[1]) ];
      query_string[pair[0]] = arr;
    } else {
      query_string[pair[0]].push(decodeURIComponent(pair[1]));
    }
  } 
    return query_string;
}();
```

Esta función generarará un objeto `QueryString` a través del cual podremos acceder a los diferentes parámetros de la URL

```javascript
//URL => www.mydomain.com?param1=parameter1&param2=parameter2

QueryString.param1 === 'parameter1'; //true
QueryString.param2 === 'parameter2'; //true
```

Hasta pronto!!
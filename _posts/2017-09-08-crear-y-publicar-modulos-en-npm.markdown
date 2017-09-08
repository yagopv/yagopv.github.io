---
layout: post
title: Crear y publicar módulos en npm
date: '2017-09-08 13:43:16'
tags:
- node npm
categories:
- Frontend
---

# Crear y publicar módulos en npm

Para crear un paquete en el registro de npm hay que seguir unos sencillos pasos. 

### Crear proyecto

Crear una carpeta vacía y abrir un terminal sobre la misma

```javascript

$ npm init

```

Con esto crearemos el fichero `package.json` del proyecto. De las preguntas que se nos hacen sólo es obligatorio introducir `name` y `version`

### Crear el módulo

Crear el punto de entrada que será cargado la importar el módulo. Por ejemplo `index.js`. En dicho archivo añadir código

```javascript

const log = (message) => console.log(message);
exports log;

```

### Crear usuario npm

Necesitaremos crear un usuario de npm para subir el módulo

```javascript
$ npm adduser
```

o bien loguearnos si ya lo tenemos

```javascript
$ npm login
```

Una vez realizada una de las dos acciones podemos almacenar las credenciales en la cache

```javascript
$ npm config ls
```

### Publicar el paquete

```javascript
$ npm publish
```

### Modificar el paquete

Después de realizar la modificaciones necesarias tenemos que actualizar el número de versión

```javascript
$ npm version z.y.z
```

Y publicar de nuevo
```javascript
$ npm publish
```

Podemos comprobar que todo esté correcto acudiendo al registro

https://npmjs.com/package/<package>

Con estos simples pasos habremos publicado un módulo en npm

Podemos comenzar a utilizarlo de la forma habitual

### Testing

En un nuevo proyecto en nueva carpeta

```javascript
$ npm install {nombre-paquete}
```

Añadiremos el fichero `test.js`

```javascript
const pkg = require('nombre-paquete');

pkg.log('Funciona!!');
```

Y comprobamos que se muestra el mensaje

```javascript
$ node test
```

Deberíamos ver por consola el mensaje introducido

Esto es todo!!




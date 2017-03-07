---
layout: post
title: Comandos git
date: '2013-05-30 07:19:55'
---


Vamos a recopilar un poco de información acerca de comandos para usar con repositorios git.

Bueno, este post lo escribo un poco para mí y mi memoria :)

A lo mejor a alguien más le viene bien :)

**git init**

Inicializa un nuevo repositorio git. Crea el directorio .git inicial en un proyecto nuevo o existente

```
cd /dir/subdir/micarpetagit
git init
```

**git clone**

<span>Crea un repositorio git desde un origen remoto. Añade la localización del repositorio clonado como remoto así que se podrán obtener de nuevo los cambios en el original mediante **git fetch** o subir las modificaciones locales en caso de tener permiso mediante **git push**</span>

```
git clone git@github.yagopv/Stashy.git
```

**git add [-A] [fichero]**

Añade ficheros modificados en tu directorio de trabajo al indice de modificaciones para poder hacer commit posteriormente

Si uso la opcion -A añado todos los ficheros modificados. En caso contrario tendré que añadir el nombre concreto del fichero a añadir

```
git add "dir/subdir/fichero.js"
git add -A
```

**git commit [-m] [mensaje de commit]**

Hago un commit de los ficheros añadidos con *git add* incluyendo el mensaje de commit que quiera.

```
git commit -m "Se añaden nuevos componentes"
```

**git rm**

Elimina ficheros de tu indice de modificaciones y de tu directorio de trabajo

```
git rm "dir/subdir/fichero.js"
```

**git commit**

Selecciona todos los ficheros de tu índice y crea un nuevo commit. Hace que el branch actual apunte al nuevo commit. No actualiza el repositorio remoto, para eso ejecutaré **git push** posteriormente.

```
git commit
```

**git status**

Muestra las diferencias entre mi directorio de trabajo respecto al indice de modificaciones

Los ficheros pueden estar en varios estados:

```
//untracked. No se están siguiendo. Sólo están en el directorio de trabajo
//modified. Se está siguiendo el fichero pero no está en el índice de modificaciones
//staged. Añadidos al índice y preparados para el commit (Se les ha hecho git add)
git status
```

**git branch**

Crea una nueva branch. Puedo usar la opción -a para que muestre un listado de branches, incluidas las remotas

```
git branch "nuevabrach"
git branch -a
```

**git checkout**

Trae una branch diferente al directorio de trabajo modificando todo lo relacionado, directorio de trabajo, indice de modificaciones, etc.

```
git checkout "branchqueyaexiste"
```

**git merge**

Hace un merge de la branch indicada sobre la actual y crea un commit de forma automática si no hay conflictos de fusión

```
git merge "branchafusionar"
```

**git reset**

Resetea el directorio de trabajo al estado del último commit realizado

```
git reset
```

**git stash **

Salva modificaciones temporalmente para poder cambiar de branch (checkout). Posteriormente se pueden volver a aplicar estos cambios sobre el directorio. Puedo usar las opciones list, para ver la lista de stash, apply para aplicar un stash de nuevo al directorio. También puedo aplicar un stash concreto mediante apply + el stash.

```
git stash
git stash list
git stash apply
git stash apply <span>stash@{2}
```

**git tag [-a] [mitag] [-m] [mensaje]**

Etiquetamos un commit con algo “comprensible”. Típicamente la versión de la aplicación. Lo normal es usarlo con la -a para incluir el tag (v1.0 …)

```
git tag -a v1.0 -m "La primera versión estable de mi aplicación!!"
git tag -a v2.0
```

**git fetch**

Recoge todos las modificaciones del repositorio remoto que no están en el local. Así obtenemos los cambios que haya hecho cualquiera que colabore en el proyecto.

```
git fetch origin
git fetch
```

**git pull**

Hace exactamente lo mismo que git fetch, y además hace un merge. Es lo mismo que ejecutar git fetch + git merge consecutivamente

```
git pull origin
git pull
```

**git push**

Envía todos los objetos del repositorio local al remoto

```
git push origin master
git push
```

**git log**

Muestra una lista de commits en una branch con su correspondiente detalle

```
git log
```

**git show**

Muestra información acerca del objeto que se le indique. Si no incluyo el objeto muestra el último commit

```
git show
git show 17ce9c0bda7c7dd44ad3f0add1f64b4d54edf04b
```

**git diff**

Muestra diferencias entre archivos modificados en el directorio de trabajo

```
git diff
```

**git grep**

Para realizar búsquedas

```
git grep "var mivar = 3"   -- *.js
```

Por ahora es suficiente para ir tirando …

Hasta pronto!!



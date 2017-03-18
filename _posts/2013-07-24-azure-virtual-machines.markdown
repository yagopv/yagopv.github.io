---
layout: post
title: Azure Virtual Machines
date: '2013-07-24 05:46:31'
tags:
- azure
categories:
- Cloud
---


Vamos a hablar un poco de las máquinas virtuales de Azure y cómo las podemos gestionar sin que el gasto sea excesivo.

[Windows Azure](http://windowsazure.com/ "Windows Azure") facilita en gran medida el trabajo con [máquinas virtuales en la nube](http://www.windowsazure.com/es-es/services/virtual-machines/ "Azure Virtual Machines") y su gestión. Parece de gran utilidad crear entornos de trabajo en la nube, con el sistema operativo que necesites (UNIX, Windows …) e instalar tus aplicaciones en ellos.

¿Os imagináis instalar por ejemplo Visual Studio en una máquina virtual y acceder a ella desde cualquier PC, portátil?

Otra gran utilidad que le veo es por ejemplo probar las diferentes previews, release candidates, … etc de Visual Studio sin tener que instalarlas en tu propia máquina. En la actualidad, por ejemplo, cuando creo una máquina virtual en Azure, se me ofrece la posibilidad de crearla directamente con una imagen de Windows Server que incluye la versión preview de Visual Studio Ultimate 2013.

También puedo crear máquinas virtuales de SharePoint, SQL Server, BizTalk, Linux, Ubuntu, OpenLogic …

El problema con el que me he encontrado es el precio que se ha de pagar por la máquina virtual, y es que aunque se cobre por horas, la máquina virtual permanece siempre ocupando instancias y por tanto nos siguen facturando, aunque la tenga apagada. Yo por ejemplo, por una máquina virtual con Windows Server + Visual Studio acabo pagando sobre 1 euro al día.

Para utilizar la máquina virtual de una forma que no me ponga de los nervios por la lentitud, necesitaré por lo menos una instancia de tipo L (Large, 4 núcleos con 7Gb memoria) para arriba, por lo que si no la estoy utilizando y no quiero estar pagando el precio de la L, lo que puedo hacer es reducirla a una XS cuando no la utilice. La otra forma de directamente ;evitar el pago es que la borre en el portal y cuando la vuelva a necesitar la vuelva a crear. No muy cómodo desde luego.

<span style="font-size: 1em; line-height: 1.6em;">Hay una tercera forma que sí que me ha resultado cómoda, y es utilizando la consola Powershell de Azure. La consola se puede descargar usando Web Platform Installer.</span>

<span style="font-size: 1em; line-height: 1.6em;">Utilizando la consola, se puede exportar la configuración de la máquina virtual y a continuación borrarla con dos simples comandos. Cuando quiera volver a utilizarla, a partir del archivo de configuración vuelvo a crear la máquina virtual y en un minuto o dos la tengo operativa de nuevo. Esto es ya bastante más cómodo :)

<span style="font-size: 1em; line-height: 1.6em;">Los pasos serían los siguientes:</span>

<span style="font-size: 1em; line-height: 1.6em;">1) Instalar la consola de administración claro …</span>

<span style="font-size: 1em; line-height: 1.6em;">2) A continuación configurar el entorno como se explica [en este artículo](http://msdn.microsoft.com/en-us/library/windowsazure/jj554332.aspx "Azure console"). Para ello ejecutaremos algunos comandos</span>

```
Set-ExecutionPolicy RemoteSigned (Para ejecutar este comando necesitaré lanzar la consola como administrador la primera vez)
```

A continuación, si mi sistema es de 64 bits …

```
Import-Module "C:\Program Files (x86)\Microsoft SDKs\Windows Azure\PowerShell\Azure\Azure.psd1"
```

O si por el contrario es de 32 bits

```
Import-Module "C:\Program Files\Microsoft SDKs\Windows Azure\PowerShell\Azure\Azure.psd1"
```

3) Recuperar el perfil de publicación de Azure y guardarlo en algún sitio de mi PC

```
Get-AzurePublishSettingsFile
```

 4) Instalar un cliente de azure mediante el gestor de paquetes ***npm***```
npm install azure-cli -g<br />

 5) Una vez instalado podemos usar el comando "azure", pero previamente hemos de configurar nuestra cuenta. Para ello debemos importar el perfil de publicación anteriormente descargado

```
azure account import <miFichero>.publishsettings
```

 6) Ahora ya podemos exportar nuestra configuración de alguna máquina virtual que tengamos en azure y borrarla

```
azure vm export yagoperez D:\Azure\yagoperezVMinfo.json
azure vm delete yagoperez
```

7) Una vez he eliminado la máquina virtual y guardado su perfil, puedo recrearla cuando quiera</span>

```
azure vm create-from yagoperez D:\Azure\yagoperezVMinfo.json --location "West Europe"
```

… y ya está, en un minuto o dos la tengo de nuevo operativa.

Además, como nota adicional, si abro el archivo *.json* con la configuración de la máquina virtual veré cosas como “RoleSize”:”Small” que podré cambiar a por ejemplo “RoleSize”:”Large” para regenerar la máquina virtual con una instancia superior.</span>

<span style="font-size: 1em; line-height: 1.6em;">A través de la linea de comandos puedo también crear máquinas virtuales desde cero sin pasar por el portal de azure mediante el comando **azure vm create**</span>

Ahora, por tanto, cuando quiera usar y dejar de usar la máquina, sólo tendré que repetir los comandos

```
azure vm create-from
azure vm delete
```

Pues esto es todo …

Hasta pronto!!



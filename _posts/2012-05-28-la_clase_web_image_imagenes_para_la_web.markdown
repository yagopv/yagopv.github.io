---
layout: post
title: La clase WebImage. Imágenes para la Web
date: '2012-05-28 07:58:27'
tags:
- asp mvc
categories:
- .NET
---

La clase WebImage representa a objetos que permiten manipular y mostrar imágenes en una página Web. Es una clase especialmente diseñada para este tipo de entornos y nos permite olvidarnos de la programación de complicados algoritmos para el manejo de imágenes.

La clase *WebImage* nos facilita el tratamiento de imágenes en entornos Web evitando la escritura manual (y por tanto propensa a errores) de engorrosos métodos para el tratamiento de las mismas.

Bien es cierto que hoy en día hay librerías que ya nos aportan este tipo de facilidades y que son superiores a la clase *WebImage* ya que además nos aportan mecanismos de cacheo, gestión avanzada, … etc.

Pero *WebImage* es un producto que ya nos viene dado en *System.Web.Helpers*, de muy fácil manejo como veremos a continuación y además más que suficiente para la mayor parte de las situaciones.

Vamos a ver qué nos podemos encontrar en esta clase:


## Constructores

Para crear una nueva imagen Web podemos hacerlo a través de los tres métodos contructores que expone:


<p><strong>WebImage(byte[])</strong>: Inicializa una nueva instancia de la clase <em>WebImage</em> a través de un array de bytes que representan dicha imagen</p>
<p><strong>WebImage(stream)</strong>: Inicializa una nueva instancia de la clase usando un stream</p>
<p><strong>WebImage(string)</strong>: También se puede iniciar una instancia de <em>WebImage</em> simplemente pasándole el path en el que se encuentra la imagen</p>



## Propiedades


<p><strong>Filename</strong>: Representa el nombre del fichero de imagen. Se puede leer y modificar</p>
<p><strong>Height</strong>: La altura de la imagen en pixels. Es una propiedad de sólo lectura</p>
<p><strong>Width</strong>: La anchura de la imagen en pixels. Es una propiedad de sólo lectura</p>
<p><strong>ImageFormat</strong>:  Obtiene el formato de la imagen (jpeg, png, ...etc)</p>



## **Métodos**

***Marcas de agua***

```c
WebImage AddImageWatermark(
    string watermarkImageFilePath (o WebImage watermarkImage), 
    int width, 
    int height, 
    string horizontalAlign, 
    string verticalAlign, 
    int opacity, 
    int padding)
```

Añade una marca de agua a una imagen. Los parámetros representan la localización del fichero con la marca de agua (*watermarkImageFilePath)*, la anchura (*width*), y altura (*height*) de la marca de agua en pixels.

Además de la localización de la marca de agua, también se puede pasar como parámetro en su lugar una *WebImage* representándola (*watermarkImage*)

Se permite orientar la marca de agua sobre la imagen mediante *horizontalAlign (Left, Right o Center)* y *verticalAlign (Top,Middle o Bottom)*

*opacity* establecerá la opacidad de la marca de agua. Permite valores entre 0 y 100.

*padding* será un valor en pixels para indicar el espacio que se generará alrededor de la marca de agua con los bordes de la imagen.

Este método devuelve la imagen con la marca de agua incorporada.

```c
WebImage AddTextWatermark(string text, string fontColor, int fontSize, string fontStyle, string fontFamily, string horizontalAlign, string verticalAlign, int opacity, int padding )
```

Este método es similar a la marca de agua, pero en lugar de un imagen se creará un texto. Los parámetros son bastante autoexplicativos y similares a los de la marca de agua

***Duplicidad de imágenes***

```c
WebImage Clone()
```

*Clone()* copia y devuelve una *WebImage* con las mismas características que la original

***Recorte de imágenes***

```c
WebImage Crop(int top, int left, int bottom, int right )
```

*Crop()* nos permite recortar un área de la imagen lo que es de gran utilidad. Los parámetros del método representan los pixeles a recortar por cada lado de la imagen.

Existe un bug en esta clase ya que al crear una nueva *WebImage* se crear un borde en la parte de arriba e izquierda de la imagen con anchura de un pixel. Es por tanto necesario usar este método para recortar dicho borde.

```c
var image = new WebImage(path).Crop(1,1);
```

***Girar imágenes***

```c
WebImage FlipHorizontal()
WebImage FlipVertical()
WebImage RotateLeft()
WebImage RotateRight()
```

Estos cuatro métodos pemiten voltear o rotar las imágenes retornando una nueva *WebImage* volteada hacia abajo, hacia arriba, rotada a la derecho o a la izquierda.

***Obtener la imágen en bytes***

```c
byte[] GetBytes(string requestedFormat )
```

Este método es interesante ya que me devuelve un array de bytes representando la imagen que puedo usar en multitud de situaciones. El parámetro representa el formato de la imagen (jpg, png, …etc.)

***Obtener la imágen del contexto de la petición***

```c
static WebImage GetImageFromRequest( string postedFileName )
```

Este método permite obtener una instancia de *WebImage* del contexto de la petición si es que se ha hecho un post de un fichero. Opcionalmente como parámetros se puede pasar el nombre del fichero (*postedFileName*). Si no se especifica se toma como válido el primero que se carga.

***Almacenamiento o envío de respuestas al navegador***

```c
WebImage Save( string filePath, string imageFormat, bool forceCorrectExtension )
```

El método *Save()* nos permitirá guardar una *WebImage* en el *filePath* indicado. *imageFormat* sirve para indicar el formato de la imagen.

*forceCorrectExtension*, cuando es *true* hace que se corrija la extensión de la imagen en caso de detectar que es incorrecta. Es decir, una imagen llamada *imagen.zip* que sea en realidad .jpg, se guardará como *imagen.zip.jpg.*

```c
WebImage Write(string requestedFormat )
```

*Write()* renderiza directamente una imágen al servidor por lo que es ideal para crear imágenes al vuelo y enviarlas al servidor como veremos un poco más adelante. El parámetro es simplemente el formato de la imagen.

***Reescalado***

```c
WebImage Resize( int width, int height, bool preserveAspectRatio, bool preventEnlarge )
```

Sin duda uno de los métodos que más utilizaremos será *Resize()*, ya sea para crear y almacenar thumbnails o diferentes versiones de tamaños de una imagen o directamente para devolver al vuelo este tipo de versiones de menor tamaño.

Los parámetros no tienen mucho misterio, la altura y anchura a la que quiero que quede la imagen (*width, height*).

La relación de aspecto *preserveAspectRatio*, que será un *bool* y no un valor. Si es *true* se mantendrá la relación de aspecto. Si es *false* no se hará.

Por último *preventEnlarge* que evita el alargamiento y deformación de la imagen.

Uno de los usos más interesantes de Resize es crear imágenes al vuelo

```javascript
public void GetThumbnail(int imageId, int width, int height)
{
      // Obtengo la imágen de la base de datos. DomainImage será una clase con dos propiedades Path y ImageId

      // Services.GetImage() sería un método de una capa de servicios que obtiene la imagen de la BBDD
       DomainImage photo = photos. Services.GetImage(imageId); 
       if (photo != null)
       {
             WebImage image =  new WebImage(photo.Path)
 		      .Resize(width, height, false, true) 
              // Reescalo la imágen al vuelo
 	                           .Crop(1, 1) 
               // La recorto debido al bug en la clase WebImage
	                            .Write();
                // Finalmente la renderizo al navegador                
        }    
       else
       {
                //Cargo una imagen por defecto si no existe en la BBDD
                 new WebImage(Server.MapPath("~/Content/images/no-photo100x100.png")).Write();		
        }
}
```

que usaremos de la siguiente forma en nuestra vista

```html
<img src="@Url.Action("GetThumbnail", new { imageId= item.Id, width = 100, height = 100 })" />
```

## Resumen

La clase *WebImage* es de gran utilidad y nos facilita en gran medida el manejo de imágenes en nuestros desarrollos. Nos permite añadir marcas de agua o texto a las imágenes, rotarlas, recortarlas, reescalarlas, …

Ahora sólo queda comenzar a utilizarla…

Hasta pronto!!


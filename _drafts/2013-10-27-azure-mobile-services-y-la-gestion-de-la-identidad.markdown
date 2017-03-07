---
layout: post
title: Azure Mobile Services y la gestión de la Identidad
date: '2013-10-27 07:39:04'
tags:
- seguridad
---


Azure Mobile Services facilita en gran medida la integración de los principales proveedores sociales de identidad (Facebook, Twitter, Microsoft y Google) en nuestra aplicación.

Lo que no se consigue con la misma sencillez es la información de los usuarios en dichos proveedores.

Vamos a ver como podemos arreglarlo.

[Azure Mobile Services](http://www.windowsazure.com/en-us/develop/mobile/ "ZUMO") es básicamente un backend en la nube para nuestras aplicaciones. El nombre del producto incluye el término ***Mobile,*** pero a mi entender no hay ningún problema en utilizarlo con sitios web corrientes.

De una forma ágil podemos conseguir una serie de funcionalidades en el lado del servidor como pueden ser:

1. Servicios Web estándar para acceso a datos
2. Servicios Web customizados para consultas complejas o cualquier otra necesidad
3. Notificaciones Push
4. Integración con los principales proveedores de Identidad
5. Planificación de trabajos periódicos

El backend que se genera de forma mágica no es otra cosa que un servidor *NodeJs* con *express*.

Centrándonos en el punto 4, es realmente sencillo realizar el proceso de autenticación con Mobile Services. Lo que no resulta tan trivial es la recuperación de los datos de los usuarios en dichos proveedores. Si no recuperamos dichos datos, la información de la que vamos a disponer acerca de los usuarios autenticados es un poco pobre ya que ni siquiera podremos mostrar un nombre de usuario en condiciones. <span style="font-size: 1em; line-height: 1.6em;">Los identificadores de usuario recuperados tienen el formato “Proveedor : Número” con lo que podemos acabar mostrando mensajes del tipo “Bienvenido Google:67364873“. Esto no es muy amigable que digamos.</span>

Poniendo en común algunos artículos leídos en la red he generado un script para utilizar en una aplicación mía, que obtiene estos datos que echaba en falta.

Lo que habría que hacer es crear una nueva tabla en el portal de Azure llamada por ejemplo *SocialIdentities*. En dicha tabla estableceremos todos los permisos menos el de insertar como “Sólo scripts y administradores“. En el caso de los permisos para insertar lo dejaremos como “Sólo usuarios autenticados“.

Sustituimos el script por defecto por este otro:

{% highlight javascript %}
function insert(item, user, request) {    
    
    // Node Requires
    // To work with node packages => 
    //      1. Put the service under source control
    //      2. Clone
    //      3. Execute the npm install commands for the required packages 
    //      4. Push again.
    var oAuth = require('OAuth'),
        req = require('request');
    
    // Variables
    var identities = user.getIdentities(),        
        url;

    // Select an url depending on the authenticated provider to get the user data    
    if (identities.google) {
        url = "https://www.googleapis.com/oauth2/v1/userinfo?access_token=" + 
               identities.google.accessToken;
     } else if (identities.twitter) {
        var twtuserId = identities.twitter.userId;
        url = "https://api.twitter.com/1.1/users/show.json?user_id=" +
               twtuserId.substring(twtuserId.indexOf(':') + 1);
     } else if (identities.microsoft) {
        url = "https://apis.live.net/v5.0/me/?method=GET&amp;access_token=" +
               identities.microsoft.accessToken;
     } else if (identities.facebook) {
        url = "https://graph.facebook.com/me?access_token=" + 
              identities.facebook.accessToken;              
     }

    // Get OAuth2 providers data 
     if (identities.facebook || identities.google || identities.microsoft) {
         req(url, function (err, resp, body) {
             if (err || resp.statusCode !== 200) {
                 console.error('SocialIdentities:Read:Error sending data to provider', err);
                 request.respond(statusCodes.INTERNAL_SERVER_ERROR, body);
             } else {             
                 var userData = JSON.parse(body);                                                                     
                 saveChangesAndRespond(userData);                  
             }
         });         
     } 

    // Get OAuth1 providers data
    // For twitter we have to send authorized requests for retrieving the user info.
    // This is the reason to use the node-oauth library 
    if (identities.twitter) {      
        var oa = new oAuth.OAuth(
          'https://api.twitter.com/oauth/request_token',
          'https://api.twitter.com/oauth/access_token',
          'My Twitter App Id',
          'My Twitter App secret',
          '1.0A',
          null,
          'HMAC-SHA1'
        );

        oa.get(
          url,
          identities.twitter.accessToken, // user token
          identities.twitter.accessTokenSecret, // user secret            
          function (error, result, res){              
            if (error) {
                 console.error('SocialIdentities:Read:Error sending data to provider', error);
                 request.respond(statusCodes.INTERNAL_SERVER_ERROR);                
            }
            var userData = JSON.parse(result);
            saveChangesAndRespond(userData);                
         });        
    }   
    
    // Save Changes and send response to the client
    function saveChangesAndRespond(userData) {
        var identityTable = tables.getTable('SocialIdentities');                         
        identityTable
            .where({ userId: user.userId})
            .read({ success : function (result) {      
                 if (result.length > 0) {
                     // Update identity in database
                     var updId =  createIdentityObject(userData, false);                            
                     updId.id = result[0].id;
                     console.log("SocialIdentities:Read:Update in database",updId);
                     identityTable.update(updId, {
                             success: function() {                                
                                 request.respond(200, updId);
                             }
                      });
                  } else {
                      //Insert new identity in database
                      var newId = createIdentityObject(userData, true);
                      console.log("SocialIdentities:Read:Insert in database", newId);
                      identityTable.insert(newId, {
                              success: function() {                                
                                  request.respond(200, newId);
                              }
                       });
                   }
               }
          });          
    }        

    // Object to be saved to the database
    function createIdentityObject(userData, insert) {
                
        var identity = {};
        
        identity.userId = user.userId;
        identity.name = userData.name !== undefined ? userData.name : null;
        identity.profileId = userData.id !== undefined ? userData.id : null;
        identity.gender = userData.gender !== undefined ? userData.gender : null;
        identity.locale = userData.locale !== undefined ? userData.locale : null;
        
        if (identities.google) {
            identity.firstName = userData.given_name !== undefined ? userData.given_name : null;
            identity.lastName = userData.family_name !== undefined ? userData.family_name : null;            
        } else {
            identity.firstName = userData.first_name !== undefined ? userData.first_name : null; 
            identity.lastName = userData.last_name !== undefined ? userData.last_name : null;                        
        }        
                        
        if (identities.twitter) {
            identity.userName = userData.screen_name !== undefined ? userData.screen_name : null;
            identity.link = userData.url !== undefined ? userData.url : null;            
        } else {
            identity.userName = userData.username !== undefined ? userData.username : null;
            identity.link = userData.link !== undefined ? userData.link : null;            
        }
           
        if (insert) {
            identity.createdDate = new Date();
        }
        
        identity.updatedDate = new Date();
        
        return identity;      
    }           
}
{% endhighlight %}

El script está bastante comentado. Básicamente lo que hace es llamar a las url que expone cada proveedor para recuperar los datos del usuario. Vemos que en el caso de Twitter varía un poco por estar usando oAuth 1.0.

Una vez recuperados los datos se *colocan* en un objeto común para insertar en la tabla de identidades y se devuelve el objeto creado al cliente.

Con esto ya podríamos obtener los datos que necesitamos desde el lado cliente. En un cliente HTML/JavaScript podríamos autenticar a nuestros usuarios de la siguiente forma:

{% highlight javascript %}
var socialLogin = function(provider) {
    azureClient.login(provider)
        .then(function(user) {                  
            azureClient.getTable("SocialIdentities")
                .insert({ })
                .done(function(result) {
                    var userData = result;
		    ... y hago lo que sea con los datos del usuario ...
                })
        }, function(error) {                    
            console.log(error);                    
     });
}
{% endhighlight %}

Pues esto es todo. A partir de ahora ya podemos mostrar un mejorarado mensaje al autenticarse un usuario como podría ser “Bienvenido José Pérez Pérez !!”.

Hasta pronto!!



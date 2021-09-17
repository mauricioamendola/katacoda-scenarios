### Aplicaciones con HA

A menudo es necesario desplegar mas de una réplica por componente, por lo que debemos de tener containers que se crean usando la misma imagen base. Por ejemplo, para la aplicación del contador, podemos tener dos réplicas de la imagen que generamos. Algunas consideraciones que debemos de tener:  

* Si estamos en el mismo docker host no podemos usar el mismo puerto de binding en el host, por lo que el segundo container tendrá que usar otro, por ejemplo el 8081.
* Si estamos en el mismo docker host, por mas que tengamos mas de un container de la aplicación, si el host se rompe, perdemos todos los containers.
* La publicación de la aplicación debemos de resolverla a otro nivel, puesto que Docker por omisión no balancea el acceso a la app.

Es posible levantar un container con Nginx, HAProxy o similar, para resolver la publicación de la app. En nuestro ejemplo, vamos a levantar mas de una réplica de la app, pero no configuraremos un balanceador.  

Como la imagen ya existe, no hace falta construirla nuevamente. Podemos validar con el comando `docker images`{{execute}}  

Instanciar el container de Redis.  

`docker run -d --name redis --network red-interna redis:alpine`{{execute}}  

Instanciar el container 1 de nombre `contador-app1`.  

`docker run -dp 8080:5000 --network red-interna --name contador-app1 contador:v1`{{execute}}  

Instanciar el container 2 de nombre `contador-app2`.  

`docker run -dp 8081:5000 --network red-interna --name contador-app2 contador:v1`{{execute}}  
  
Podemos probar consumir la segunda aplicación, abriendo el dashboard apuntando al puerto 8081.  

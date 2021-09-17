Como habíamos comentado, un container es una instancia en tiempo de ejecución de una imagen, por lo que para crear uno basta con ejecutar el comando referenciando a la imagen que queremos usar.  

Instanciar el container con `docker run`  usando la imagen oficial de Apache `httpd:2.4`:  

`docker run -d -p 8080:80 httpd:2.4`{{execute}}

Vamos por parte:

* docker run es el comando para correr un comando
* Con -d le indicamos que queremos que corra en `detached mode`
* Con -p le indicamos con qué puerto queremos publicar el servicio que corre dentro del container. Es un mapeo que hacemos en el host con el formato hostPort:containerPort
* nodejs-app:v1 es la imagen:tag que usamos como base para el container

Podemos comprobar la ejecución del container con el comando `docker ps`  

`docker ps`{{execute}}

Comprobar el servicio localmente:  
`curl -v http://localhost:8080`{{execute}}  

En el panel de "Dashboard" se puede consultar la web de la aplicación.  
![dashboard](./assets/dashboard.png)
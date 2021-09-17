### App + db

En el paso anterior realizamos el proceso de crear una imagen para instanciar un solo container, pero no todos los escenarios son así, de hecho, lo común es que tengamos muchos componentes, desarrollados en lenguajes distintos y conectados para resolver distintos problemas, por ejemplo, una aplicación que necesita persistir datos, es posible que necesite un backend de base de datos. 

Lo que haremos ahora será instanciar dos aplicaciones y relacionarlas, para esto, debemos de entender como funciona el networking en Docker.  

Con cada instalación de Docker se agregan drivers / plugin de red para proveer distintas funcionalidades. Por omisión tenemos los siguientes drivers:  
* Bridge: Driver por omisión, establece un adaptador de bridge entre el adaptador del container y el adaptador del host.
* Host: Elimina el aislamiento entre el container y el host y utiliza directamente el adaptador del host.
* Overlay: Se usa para conectar dos o mas containers que están en distintos Docker host.
* Macvlan: Se usa para asignarle una dirección física (MAC Address) al container.
* none: Deshabilita el networking para un container.

![docker-network](./assets/docker-network.png)

La aplicación que usaremos es un contador escrito en python y que graba el incremento en un backend Redis. 
### Prerequisitos

Hay que crear una Docker Network  

`docker network create red-interna`{{execute}}  

Instanciar el backend Redis. Para esto hay que crear un container de nombre "redis" porque la aplicación tiene grabado que el hostname se llama asi.  

`docker run -d --name redis --network red-interna redis:alpine`{{execute}}  

### Construír la imagen aplicación

`mkdir contador && cd contador`{{execute}}  

Crear un archivo contador.py con el siguiente código  

`touch contador.py`{{execute}}  

```python  
import time
import os

import redis
from flask import Flask

app = Flask(__name__)
cache = redis.Redis(host='redis', port=6379)
def get_hit_count():
    retries = 5
    while True:
        try:
            return cache.incr('hits')
        except redis.exceptions.ConnectionError as exc:
            if retries == 0:
                raise exc
            retries -= 1
            time.sleep(0.5)

@app.route('/')
def hello():
    count = get_hit_count()
    return 'Hola Mundooooooo! I have been seen {} times.\n'.format(count)
```  

Crear el Dockerfile

```cat <<EOF > Dockerfile
FROM python:3.7-alpine
WORKDIR /code
ENV FLASK_APP=contador.py
ENV FLASK_RUN_HOST=0.0.0.0
ENV REDIS_HOST=redis
RUN apk add --no-cache gcc musl-dev linux-headers
RUN pip install flask redis
EXPOSE 5000
COPY . .
CMD ["flask", "run"]
EOF
```{{execute}}  

Buildear la imagen  

`docker build -t contador:v1 .`{{execute}}  

Finalmente, corremos la aplicación.

`docker run -dp 8080:5000 --network red-interna --name contador-v1 contador:v1`{{execute}}  

En el panel de "Dashboard" se puede consultar la web de la aplicación.  
![dashboard](./assets/dashboard.png)

### Clean up

`docker stop redis && docker rm redis`{{execute}}
`docker stop contador-v1 && docker rm contador-v1`{{execute}}
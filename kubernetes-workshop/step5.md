### Configmaps, variables de entorno y Secrets

Para configurar las aplicaciones que residen en los containers tenemos algunas herramientas para hacerlo, sin la necesidad de manipular manualmente los archivos de conf de las aplicaciones dentro de la Imagen.  
Para esto, existen tres mecanismos que podemos adoptar, eligiendo el adecuado dependiendo el caso de uso.  

* Variables de entorno: Sirven para pasar al momento de crear el POD información que será interpretada por la aplicación.
* Configmaps: Sirven para presentarle determinadas configuraciones o archivos de config en un path que la aplicación luego va a buscar.  
* Secrets: Similar al configmap pero con datos sensibles como passwords o certificados.  

#### Creando Variables de Entorno

Podemos crear variables de entorno usando `_kubectl_` o directamente en el manifiesto del Deployment / POD. Por ejemplo, si quisieramos crear un POD de MySQL, la imagen soporta varias variables de entorno que podemos usar, como _MYSQL_ROOT_PASSWORD_ ó _MYSQL_DATABASE_.
`kubectl run mysql-db --image=mysql:latest --env=MYSQL_DATABASE=my-app-db`{{execute}}  

```
cat <<EOF > mysql-deployment.yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: mysql-deployment
  labels:
    app: mysql
    type: database
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: mysql
        type: database
    spec:
      containers:
        - name: mysql
          image: mysql
          ports:
            - containerPort: 3306
              name: dbport
          env:
            - name: MYSQL_DATABASE
              value: my-app-db
EOF
```{{execute}}  

Después, como siempre, podemos hacer `kubectl create -f mysql-deployment.yaml`.  

#### Creando Configmaps

Un Configmap se puede crear a partir de un directorio, archivo o un "literal". Luego de creado el objeto, en el Deployment / POD se debe referenciar cada clave que queremos especificar. Siguiendo el ejemplo anterior:  

`kubectl create cm mysql --from-literal=mysql-database=my-app-db`{{execute}}  

Luego, debemos de modificar el deployment, en la sección ENV, para que quede de la siguiente forma:  

```yaml

#Antes
          env:
            - name: MYSQL_DATABASE
              value: my-app-db

#Después
          env:
            - name: MYSQL_DATABASE
              valueFrom:
                configMapKeyRef:
                  name: mysql
                  value: mysql-database
```  

```
cat <<EOF > mysql-deployment-cm.yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: mysql-deployment
  labels:
    app: mysql
    type: database
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: mysql
        type: database
    spec:
      containers:
        - name: mysql
          image: mysql
          ports:
            - containerPort: 3306
              name: dbport
          env:
            - name: MYSQL_DATABASE
              valueFrom:
                configMapKeyRef:
                  name: mysql
                  value: mysql-database
EOF
```{{execute}} 

#### Creando Secrets

Para todos aquellos datos que necesitamos cuidar, los secrets son el tipo de objeto recomendado. Hay que tener en cuenta, que el dato almacenado en el secret no está cifrado, el termino correcto es "ofuscado".  

De la misma forma que trabajamos con los ConfigMaps, lo haremos con los Secrets.  

`kubectl create secret generic mysql --from-literal=mysql-root-password=some_evil_password`{{execute}}  

Luego, editamos el Deployment para agregar el dato desde el Secret:  

```yaml
          env:
            - name: MYSQL_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql
                  key: mysql-root-password
```   

Finalmente, el deployment completo quedaría así:  

```
cat <<EOF > mysql-deployment-secret.yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: mysql-deployment
  labels:
    app: mysql
    type: database
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: mysql
        type: database
    spec:
      containers:
        - name: mysql
          image: mysql
          ports:
            - containerPort: 3306
              name: dbport
          env:
            - name: MYSQL_DATABASE
              valueFrom:
                configMapKeyRef:
                  name: mysql
                  value: mysql-database
             - name: MYSQL_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql
                  key: mysql-root-password
EOF
```{{execute}}            
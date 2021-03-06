Para comprobar si tenemos instalado _kubectl_ correctamente, ejecutar lo siguiente para mostrar la versión que tenemos instalada:  

`kubectl version`{{execute}}  

Si obtenemos la siguiente salida `The connection to the server localhost:8080 was refused - did you specify the right host or port?` debemos de darle unos minutos hasta que carguen todos los componentes del Cluster.  

Comprobamos la configuración de 'kubectl':  

`kubectl config view`{{execute}}  

Vamos a crear nuestro primer POD.  

Un POD es la unidad mínima de trabajo. Un POD puede contener uno o mas containers. Cuando la aplicación escala, lo hace en cantidad de PODs.  

`kubectl run webapp --image=httpd:2.4 --restart=Never`{{execute}}  

Podemos verificar el estado de creación usando `kubectl get pods`{{execute}} o `kubectl get events`{{execute}}  

Luego de que el pod está running, podemos hacer un describe del PODs.  

`kubectl describe pod webapp`{{execute}}  

Vamos a eliminar el POD y a crearlo de nuevo por otro mecanismo.  

`kubectl delete pod webapp`{{execute}}  

Ahora vamos crear el mismo POD pero esta vez armando un archivo en formato YAML el cuál servirá de manifiesto para crear el objeto POD.   

```
cat <<EOF > webapp.yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: webapp
  name: webapp
spec:
  containers:
  - image: httpd:2.4
    name: webapp
    resources: {}
  restartPolicy: Never
EOF
```{{execute}}

Verificar el archivo creado con `cat webapp.yaml`{{execute}}  

Ahora podemos crear el objeto con el comando `kubectl`  

`kubectl create -f webapp.yaml`{{execute}}  


### Clean Up

`kubectl delete -f webapp.yaml`{{execute}}
### Organizando despliegues / aplicaciones

Al momento hemos estado trabajando en un **Namespace** llamado _default_ donde quedamos automaticamente posicionados cuando ingresamos al cluster de k8s. Los **Namespaces** sirven para agrupar objetos que pertenecen a una misma aplicación o proyecto. Por ejemplo, podríamos tener **Namespaces** separados por ambientes: QA, Prod, Test, etc.  
A cada **Namespace** se le puede configurar determinadas reglas para aislar los componentes y hacer que nuestras aplicaciones no sean consumibles desde fuera del Namespace; incluso podríamos configurar para que las aplicaciones dentro de un **Namespace** sean consumidas por ciertos componentes en otros **Namespaces**.  

#### Creando Namespaces

`kubectl create namespace MyNamespace`{{execute}}  

`kubectl get namespaces`{{execute}}  

Para posicionarnos en el namespace creado, basta con:  

`kubectl config set-context --current --namespace=MyNamespace`{{execute}}  
### Services

Los **Services** son una abstracción que nos permiten publicar las aplicaciones para ser consumidas, ya sea por otros PODs o usuarios.  
Existen cuatro tipo de **Services**  

* ClusterIP   
* NodePort  
* LoadBalancer  
* ExternalName  

#### ClusterIP

Se le asigna una IP virtual que puede ser consumida por otras aplicaciones dentro del cluster (tráfico este-oeste) o desde fuera del cluster (norte-sur) usando `kubectl proxy`.  
#### NodePort

Sirve para exponer un puerto al exterior. Por omisión, el puerto se asigna de forma Random entre el bloque 30000 y 40000.  

#### LoadBalancer

En los servicios de Cloud Pública, este tipo de **Service** se integra con los servicios de balanceadores de carga para publicar directamente un Service mediante una URL ó IP pública.  


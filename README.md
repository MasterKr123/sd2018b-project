# sd2018b-project
## Miniproyecto Sistemas Distribuidos
Repository for the distributed computing project

**Universidad ICESI**  
**Curso:** Sistemas Distribuidos  
**Docente:** Daniel Barragán C.  
**Tema:**  Kubernetes  
**Correo:** daniel.barragan at correo.icesi.edu.co
**Estudiantes:** Jorge Eliecer, Jonatan Ordoñez, Julian Gonzalez.


### Objetivos
* Identificar los componentes de un cluster de kubernetes
* Desplegar un cluster de kubernetes
* Desplegar aplicaciones en un cluster de kubernetes empleando sus propiedades
de volumenes persistentes, balanceo de carga y descubrimiento de servicio
* Diagnosticar y ejecutar de forma autónoma las acciones necesarias para lograr infraestructuras estables

### Prerrequisitos
* Cluster de Kubernetes

### Descripción
Debera realizar el despliegue de un cluster de kubernetes en al menos tres nodos independientes (un nodo maestro y al menos dos nodos de trabajo). Para ello realice la instalación y configuración de las dependencias necesarias (kubeadm, kubectl, kubelet), y el despliegue de los pods de red necesarios para la comunicación. Todo el proceso debera ser debidamente documentado

Para la prueba del funcionamiento del cluster deberá realizar el despliegue de un pod con al menos un servicio web que se ejecute en el framework de su preferencia. La especificación del deployment debe cumplir con los siguientes requerimientos:

* Cantidad de replicas: 3
* Asignar una etiqueta que identifique al pod

La especificación del servicio debe mapear un puerto de cada pod a un balanceador de carga, de esta manera la aplicación desplegada debe poder ser accedida a través de un navegador o cliente de consola (curl, wget)

**Opcional:**
* Plantee e implemente una estrategia para el monitoreo de los contenedores (Pods) puede emplear alguna de las siguientes tecnologías: kubernetes health checks, efk stack, cadvisor, otros.
* Plantee e implemente una estrategia para la automatización de la conexión a través de tokens

**Nota**
* No se requiere que incluya archivos de la automatización del cluster o infraestructura base. El empleo de tecnologías de automatización para la infraestructura base es a consideración del estudiante

### Actividades
1. Documento en formato PDF:  
  * Formato PDF (5%)
  * Nombre y código de los integrantes del grupo (5%)
  * Ortografía y redacción (5%)
2. Consigne la documentación que incluya los comandos de linux necesarios para el aprovisionamiento del cluster de kubernetes
  * Instalación y configuración de kubeadm, kubectl, kubelet (10%)
  * Instalación y configuración de un pod de red que interconecte los pods a desplegar en el cluster (20%)
  * Evidencias del despliegue del servicio solicitado ejecutandose correctamente en los nodos del cluster. Deberá demostrar que ante la caída de uno de los nodos que forman parte del cluster, los pods son reasignados automáticamente a un nodo saludable. Incluya los archivos: deployment.yml, service.yml empleados (15%)
4. El informe debe publicarse en un repositorio de github el cual debe ser un fork de https://github.com/ICESI-Training/sd2018b-project y para la entrega deberá hacer un Pull Request (PR) respetando la estructura definida. El código fuente y la url de github deben incluirse en el informe (15%). Tenga en cuenta publicar los archivos para el aprovisionamiento
5. Incluya evidencias que muestran el funcionamiento de lo solicitado (15%)
6. Documente algunos de los problemas encontrados y las acciones efectuadas para su solución al aprovisionar la infraestructura y aplicaciones (10%)



### Desarrollo

1. :heavy_check_mark:

2. Aprovisionamiento del cluster de kubernetes

**2.1 Instalación y configuración de kubeadm, kubectl, kubelet**

La instalación de estos paquetes permite:

* kubeadm: el comando para arrancar el cluster.
* kubelet: el componente que se ejecuta en todas las máquinas de su clúster y hace cosas como arrancar contenedores y contenedores.
* kubectl: la línea de comandos para hablar con tu grupo.

Para la instalación de estas de dependencias se ejecuto los siguientes comandos:
```
apt-get update && apt-get install -y apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF
apt-get update
apt-get install -y kubelet kubeadm kubectl
apt-mark hold kubelet kubeadm kubectl
```

**2.2 Instalación y configuración de un pod de red que interconecte los pods a desplegar en el cluster**

Se debe instalar un complemento de red de pod para que los pods puedan comunicarse entre sí. Para ello, se realiza lo siguiente:

Primero, instalamos el Pod Network, ejecutando el siguiente comando:
```
kubectl apply -f https://docs.projectcalico.org/v3.3/getting-started/kubernetes/installation/hosted/rbac-kdd.yaml
kubectl apply -f https://docs.projectcalico.org/v3.3/getting-started/kubernetes/installation/hosted/kubernetes-datastore/calico-networking/1.7/calico.yaml
```
![][2]
Como se puede apreciar, se hizo uso del pod network Calico.

Luego, verificamos que todos los Pods se encuentren ejecutando:
```
watch kubectl get pods --all-namespaces
```

Despues, debemos permitir que el nodo master admita el despliegue de Pods. Hay que tener en cuenta que por defecto, el nodo master de un clúster de Kubernetes no ejecuta ningún tipo de carga de trabajo relacionada con los pods desplegados en el clúster, centrándose en las tareas de gestión de los pods y del propio clúster. Por lo cual, ejecutamos lo siguiente:
```
kubectl taint nodes --all node-role.kubernetes.io/master-
```



**2.3 Despliegue del servicio**

Primero se ejecuta el kubeadm
```
sudo kubeadm init --pod-network-cidr=192.168.0.0/16
```
donde:
–pod-network-cidr: valor del pools de IP, requerido por el complemento de red de pod para la asignación de rangos de red (CIDRS) a cada nodo. En nuestro caso “Calico” por defecto utiliza 192.168.0.0/16.
![][1]
```
kubeadm join 192.168.130.132:6443 --token pd8hwx.cojtmlbibatkjulk --discovery-token-ca-cert-hash sha256:2967c997d2dc52310b13125b5369051311bdf6af2a68ee78b425941ae2eff07a
```
Nota: Guardar el Token generado para ser utilizado en la unión de los nodos


3. :heavy_check_mark:

4. :heavy_check_mark:

5. :heavy_check_mark:

6. Problemas encontrados y las acciones efectuadas para su solución

### Referencias

* https://kubernetes.io/docs/setup/independent/install-kubeadm/
* https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/#pod-network
* https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/
* https://juantrucupei.wordpress.com/2018/03/25/instalacion-de-kubernetes-en-linux-con-kubeadm/

[1]: images/kubeadm.png
[2]: images/podnetwork.png
[3]: images/pods.png

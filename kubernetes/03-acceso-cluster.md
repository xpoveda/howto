Acceso al cluster de kubernetes
===============================

* Acceso a los pods internamente via IP interna dinamica
* Acceso a los servicios internamente via IP cluster interna
* Acceso externo via IP externa estatica y nodeport
* Acceso a los servicios vía DNS

Acceso a los pods internamente via IP interna dinamica
-------------------------------------------------------
Creamos un pod con una imagen que nos devuelve el hostname donde se está ejecutando.
Aunque se llame nginx no tiene nada que ver con el servidor web.
```
[root@master ~]# more nginx-deployment.yaml
apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2 # tells deployment to run 2 pods matching the template
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: sirile/scala-boot-test
        ports:
        - containerPort: 80
```
Desplegamos
```        
[root@master ~]# kubectl create -f nginx-deployment.yaml
```

Y vemos los despliegues que tenemos
```
[root@master ~]# kubectl get deployments
NAME               DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   2         2         2            2           5m43s
```
Y donde se han desplegado
```
[root@master ~]# kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-5dd98684f6-lh6zv   1/1     Running   0          5m22s
nginx-deployment-5dd98684f6-mg4wk   1/1     Running   0          5m22s
```

Podriamos borrar un pod, pero se levantaria automaticamente otro porque el 
despliegue indica que han de haber 2 replicas.
```
[root@master ~]# kubectl delete pod nginx-deployment-5dd98684f6-mg4wk
pod "nginx-deployment-5dd98684f6-mg4wk" deleted

[root@master ~]# kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-5dd98684f6-lh6zv   1/1     Running   0          7m16s
nginx-deployment-5dd98684f6-sjf4f   1/1     Running   0          11s
```

Cada pod tiene un IP dinamica y propia
```
[root@master ~]# kubectl describe pod nginx-deployment-5dd98684f6-lh6zv | grep IP
IP:                 10.47.0.2
[root@master ~]# kubectl describe pod nginx-deployment-5dd98684f6-lh6zv | grep "Node:"
Node:               node2/10.0.1.7

[root@master ~]# kubectl describe pod nginx-deployment-5dd98684f6-sjf4f | grep IP
IP:                 10.44.0.4
[root@master ~]# kubectl describe pod nginx-deployment-5dd98684f6-sjf4f | grep "Node:"
Node:               node1/10.0.1.6
```

A la que podemos acceder directamente por el interface de red que nos ha habilitado kubernetes, no por la IP del nodo
```
[root@master ~]# curl http://10.44.0.4
{"hostname":"nginx-deployment-5dd98684f6-sjf4f","time":"2018-11-02T12:25:10Z","language":"Scala"}[root@master ~]#
[root@master ~]# curl http://10.0.1.6
curl: (7) Failed connect to 10.0.1.6:80; Connection refused
```

Buscamos por label
```
[root@master ~]# kubectl get pods -l app=nginx -o wide
NAME                                READY   STATUS    RESTARTS   AGE    IP          NODE    NOMINATED NODE
nginx-deployment-5dd98684f6-lh6zv   1/1     Running   0          15m    10.47.0.2   node2   <none>
nginx-deployment-5dd98684f6-sjf4f   1/1     Running   0          8m4s   10.44.0.4   node1   <none>
[root@master ~]#
```

Acceso a los servicios internamente via IP cluster interna
-----------------------------------------------------------
Creamos el servicio
```
[root@master ~]# more nginx-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
  labels:
    app: nginx-service
spec:
  ports:
  - port: 80
    protocol: TCP
  selector:
    app: nginx
```

```
[root@master ~]# kubectl create -f nginx-service.yaml
service/nginx-service created
```

Podemos ver la IP del cluster a la que podremos acceder internamente.
```
[root@master ~]# kubectl get services
NAME            TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
kubernetes      ClusterIP   10.96.0.1      <none>        443/TCP   2d22h
nginx-service   ClusterIP   10.107.30.96   <none>        80/TCP    22s
```

```
[root@master ~]# kubectl describe service nginx-service
Name:              nginx-service
Namespace:         default
Labels:            app=nginx-service
Annotations:       <none>
Selector:          app=nginx
Type:              ClusterIP
IP:                10.107.30.96
Port:              <unset>  80/TCP
TargetPort:        80/TCP
Endpoints:         10.44.0.4:80,10.47.0.2:80
Session Affinity:  None
Events:            <none>
```

```
[root@master ~]# curl http://10.107.30.96
{"hostname":"nginx-deployment-5dd98684f6-sjf4f","time":"2018-11-02T12:37:24Z","language":"Scala"}
[root@master ~]# curl http://10.107.30.96
{"hostname":"nginx-deployment-5dd98684f6-lh6zv","time":"2018-11-02T12:37:32Z","language":"Scala"}
```

Acceso externo via IP externa estatica y nodeport
-------------------------------------------------
```
[root@master ~]# more nginx-service-nodeport.yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service-nodeport
  labels:
    app: nginx-service-nodeport
spec:
  type: NodePort
  ports:
  - name: http
    nodePort: 31704
    port: 80
    targetPort: 80
    protocol: TCP
  selector:
    app: nginx
```

Abrimos el puerto 31704 por el firewall de Azure. No podemos ir por un puerto de rango normal ya que..
```
[root@master ~]# kubectl create -f nginx-service-nodeport-80.yaml
The Service "nginx-service-nodeport-80" is invalid: spec.ports[0].nodePort: Invalid value: 80: provided port is not in the valid range. The range of valid ports is 30000-32767
```

Podemos acceder con Nodeport desde cualquiera de las ips externas del master o de los nodos contra un puerto con rango grande
```
master: curl http://************:31704 && echo "\n"
node1:  curl http://************:31704 && echo "\n"
node2:  curl http://************:31704 && echo "\n"
```

```
[root@master ~]# curl http://*****************:31704 && echo "\n"
{"hostname":"nginx-deployment-5dd98684f6-lh6zv","time":"2018-11-02T13:02:37Z","language":"Scala"}\n
[root@master ~]# curl http://*****************:31704 && echo "\n"
{"hostname":"nginx-deployment-5dd98684f6-sjf4f","time":"2018-11-02T13:02:38Z","language":"Scala"}\n
[root@master ~]# curl http://*****************:31704 && echo "\n"
{"hostname":"nginx-deployment-5dd98684f6-sjf4f","time":"2018-11-02T13:02:40Z","language":"Scala"}\n
[root@master ~]# curl http://*****************:31704 && echo "\n"
{"hostname":"nginx-deployment-5dd98684f6-sjf4f","time":"2018-11-02T13:02:40Z","language":"Scala"}\n
```

Acceso a los servicios vía DNS
------------------------------
Cuando definimos un servicio kubernetes habilita automaticamente un servicio de dns que hace que haya resolucion de nombres de ese servicio. Es decir tenemos un "service discovery" automaticamente habilitado en kubernetes.

De esta forma hay visililidad entre los pods de un mismo cluster.
```
[root@master ~]# kubectl get services nginx-service
NAME            TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
nginx-service   ClusterIP   10.107.30.96   <none>        80/TCP    45h
```

Este servicio lo tendriamos accesible via IP desde cualquiera de nuestros nodos.
```
[root@master ~]# curl http://10.107.30.96 && echo "\n"
{"hostname":"nginx-deployment-5dd98684f6-84dzs","time":"2018-11-04T10:14:50Z","language":"Scala"}\n
```

Pero no via dns porque NO estamos dentro de un pod. Comprobamos que el servicio de kube-dns funciona correctamente.
```
[root@master ~]# kubectl get services kube-dns --namespace=kube-system
NAME       TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)         AGE
kube-dns   ClusterIP   10.96.0.10   <none>        53/UDP,53/TCP   4d19h
```

Lo que hacemos es acceder a un pod que nos permita entrada por interprete de comandos.
Ahi se abrirá una sesion unix y haremos un `nslookup` para ver donde resuelve.
```
[root@master ~]# kubectl run curl --image=radial/busyboxplus:curl -i --tty
kubectl run --generator=deployment/apps.v1beta1 is DEPRECATED and will be removed in a future version. Use kubectl create instead.
If you don't see a command prompt, try pressing enter.

[ root@curl-5cc7b478b6-n28vn:/ ]$ nslookup nginx-service
Server:    10.96.0.10
Address 1: 10.96.0.10 kube-dns.kube-system.svc.cluster.local

Name:      nginx-service
Address 1: 10.107.30.96 nginx-service.default.svc.cluster.local
```

Ahi podemos hacer llamadas al servicio que hemos habilitado y vemos como se va balanceando correctamente.
```
[ root@curl-5cc7b478b6-n28vn:/ ]$ curl http://nginx-service.default.svc.cluster.local
{"hostname":"nginx-deployment-5dd98684f6-5j2h4","time":"2018-11-04T10:09:54Z","language":"Scala"}[ root@curl-5cc7b478b6-n28vn:/ ]$
[ root@curl-5cc7b478b6-n28vn:/ ]$
[ root@curl-5cc7b478b6-n28vn:/ ]$ curl http://nginx-service.default.svc.cluster.local
{"hostname":"nginx-deployment-5dd98684f6-5j2h4","time":"2018-11-04T10:10:06Z","language":"Scala"}[ root@curl-5cc7b478b6-n28vn:/ ]$
[ root@curl-5cc7b478b6-n28vn:/ ]$
[ root@curl-5cc7b478b6-n28vn:/ ]$ curl http://nginx-service.default.svc.cluster.local
{"hostname":"nginx-deployment-5dd98684f6-qrd6q","time":"2018-11-04T10:10:08Z","language":"Scala"}[ root@curl-5cc7b478b6-n28vn:/ ]$
```

Podemos salir y volver a entrar en el pod de shell que habiamos creado.
```
[ root@curl-5cc7b478b6-n28vn:/ ]$ exit
Session ended, resume using 'kubectl attach curl-5cc7b478b6-n28vn -c curl -i -t' command when the pod is running
[root@master ~]# kubectl attach curl-5cc7b478b6-n28vn -c curl -i -t
If you don't see a command prompt, try pressing enter.
[ root@curl-5cc7b478b6-n28vn:/ ]$
```

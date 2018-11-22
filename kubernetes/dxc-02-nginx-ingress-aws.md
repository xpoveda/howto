Instalando nginx ingress
========================
Para poder acceder a los distintos endpoints que ofrece kubernetes podemos ir via `nodeport` con lo que
entrariamos por puerto a un determinado nodo de computo de nuestro cluster o por `loadbalancer` dando una
visión transparente de las llamadas, balanceando las peticiones entre los nodos de forma automatica.

En ambos casos se necesita instalar un `controlador ingress`, que se situa por encima de la capa de
servicios de kubernetes.

https://kubernetes.io/docs/concepts/services-networking/ingress/

En nuestro caso vamos a instalar la implementacion de ingress que ha realizado nginx.

Accedemos a su pagina de github https://github.com/nginxinc/kubernetes-ingress y hacemos 
click en la seccion de `Getting started` en `manifests`.

Con esto saltaremos a https://github.com/nginxinc/kubernetes-ingress/tree/master/deployments
y ahi nos indicará los pasos para poderlo instalar en https://github.com/nginxinc/kubernetes-ingress/blob/master/docs/installation.md

Primero de todo haremos un `git clone` del repositorio.
```
[ec2-user@ip-172-31-22-115 ~]$ git clone https://github.com/nginxinc/kubernetes-ingress.git
Cloning into 'kubernetes-ingress'...
remote: Enumerating objects: 63, done.
remote: Counting objects: 100% (63/63), done.
remote: Compressing objects: 100% (48/48), done.
remote: Total 14622 (delta 25), reused 29 (delta 15), pack-reused 14559
Receiving objects: 100% (14622/14622), 23.34 MiB | 4.13 MiB/s, done.
Resolving deltas: 100% (5639/5639), done.
```

Y accederemos a la carpeta `deployments` ejecutando despues los siguientes `yaml`
```
[ec2-user@ip-172-31-22-115 deployments]$ pwd
/home/ec2-user/kubernetes-ingress/deployments

kubectl apply -f common/ns-and-sa.yaml
kubectl apply -f common/default-server-secret.yaml
kubectl apply -f common/nginx-config.yaml
kubectl apply -f rbac/rbac.yaml
kubectl apply -f deployment/nginx-ingress.yaml
```

Viendo los pods
---------------
Con esto vemos los pods que estan corriendo en el namespace que nos interesa
```
[ec2-user@ip-172-31-22-115 deployments]$ kubectl get pods --namespace=nginx-ingress
NAME                            READY   STATUS    RESTARTS   AGE
nginx-ingress-89f96847c-5j5c5   1/1     Running   0          14s
```

Si queremos ver los pods de todos los namespaces lo hariamos con `kubectl get pods --all-namespaces`

Kubernetes cheatsheet
---------------------

https://kubernetes.io/docs/reference/kubectl/cheatsheet/

Servicios nodeport
------------------
Creamos el servicio para `nodeport`
```
kubectl create -f service/nodeport.yaml


[ec2-user@ip-172-31-22-115 deployments]$ kubectl get services --namespace=nginx-ingress
NAME            TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress   NodePort   100.64.55.91   <none>        80:32012/TCP,443:32604/TCP   2m
```

Podemos ver más información de la siguientes forma
```
[ec2-user@ip-172-31-22-115 deployments]$ kubectl describe services nginx-ingress --namespace=nginx-ingress
Name:                     nginx-ingress
Namespace:                nginx-ingress
Labels:                   <none>
Annotations:              <none>
Selector:                 app=nginx-ingress
Type:                     NodePort
IP:                       100.64.55.91
Port:                     http  80/TCP
TargetPort:               80/TCP
NodePort:                 http  32012/TCP
Endpoints:                100.96.2.2:80
Port:                     https  443/TCP
TargetPort:               443/TCP
NodePort:                 https  32604/TCP
Endpoints:                100.96.2.2:443
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
```


Vamos a los nodos del cluster y en el security group que comparten habilitamos los puertos de acceso para el nodeport.
Con esto ya podremos acceder yendo por cualquiera de las ips publicas

```
[ec2-user@ip-172-31-22-115 deployments]$ curl http://XXXXXXXXXXXX:32012
<html>
<head><title>404 Not Found</title></head>
<body>
<center><h1>404 Not Found</h1></center>
<hr><center>nginx/1.15.6</center>
</body>
</html>

[ec2-user@ip-172-31-22-115 deployments]$ curl http://XXXXXXXXXXXX:32604
<html>
<head><title>400 The plain HTTP request was sent to HTTPS port</title></head>
<body>
<center><h1>400 Bad Request</h1></center>
<center>The plain HTTP request was sent to HTTPS port</center>
<hr><center>nginx/1.15.6</center>
</body>
</html>
```

Loadbalancer
------------
Creamos el servicio de loadbalancer
```
[ec2-user@ip-172-31-22-115 deployments]$ kubectl apply -f service/loadbalancer-aws-elb.yaml
service/nginx-ingress configured
```

Y vemos como podemos acceder por nodeport a cada uno de los nodos del cluster pero aun no por `external ip` ya que
el controlador ingress no nos ha dado ninguna IP que haga de endpoint.
```
[ec2-user@ip-172-31-22-115 deployments]$ kubectl get services --all-namespaces
NAMESPACE       NAME            TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
default         kubernetes      ClusterIP      100.64.0.1     <none>        443/TCP                      26m
kube-system     kube-dns        ClusterIP      100.64.0.10    <none>        53/UDP,53/TCP                25m
nginx-ingress   nginx-ingress   LoadBalancer   100.64.55.91   <pending>     80:32012/TCP,443:32604/TCP   17m
```

Llegados a este punto cada proveedor tiene diferencias, en el caso de aws hemos de añadir los elementos a `data:`
que se pueden ver mas abajo.
```
[ec2-user@ip-172-31-22-115 deployments]$ cat common/nginx-config-2.yaml
kind: ConfigMap
apiVersion: v1
metadata:
  name: nginx-config
  namespace: nginx-ingress
data:
  proxy-protocol: "True"
  real-ip-header: "proxy_protocol"
  set-real-ip-from: "0.0.0.0/0"


[ec2-user@ip-172-31-22-115 deployments]$ kubectl apply -f common/nginx-config-2.yaml
configmap/nginx-config configured
```

Ademas hemos de crear un `Servicelinkedrole` para que la configuracion del loadbalancer del yaml pueda crear automaticamente un ELB (elastic load balancer) en nuestra cuenta de AWS.
Esto lo podemos ver en el siguiente enlace https://stackoverflow.com/questions/51597410/aws-eks-is-not-authorized-to-perform-iamcreateservicelinkedrole

En el que podemos ver que la creacion de ese rol ha de ser manual
```
[ec2-user@ip-172-31-22-115 ~]$ aws iam create-service-linked-role --aws-service-name "elasticloadbalancing.amazonaws.com"
{
    "Role": {
        "AssumeRolePolicyDocument": {
            "Version": "2012-10-17",
            "Statement": [
                {
                    "Action": [
                        "sts:AssumeRole"
                    ],
                    "Effect": "Allow",
                    "Principal": {
                        "Service": [
                            "elasticloadbalancing.amazonaws.com"
                        ]
                    }
                }
            ]
        },
        "RoleId": "XXXXXXXXXXXXXXXXXXXXXXXX",
        "CreateDate": "2018-11-20T13:06:02Z",
        "RoleName": "AWSServiceRoleForElasticLoadBalancing",
        "Path": "/aws-service-role/elasticloadbalancing.amazonaws.com/",
        "Arn": "arn:aws:iam::120619729666:role/aws-service-role/elasticloadbalancing.amazonaws.com/AWSServiceRoleForElasticLoadBalancing"
    }
}
```

Finalmente hemos borrado el servicio de balanceo con `kubectl delete -f service/loadbalancer-aws-elb.yaml` para crearlo de nuevo con `kubectl apply -f service/loadbalancer-aws-elb.yaml`.

Y ahora si que vemos que el balanceador se ha creado correctamente.
```
[ec2-user@ip-172-31-22-115 deployments]$ kubectl describe -f service/loadbalancer-aws-elb.yaml
Name:                     nginx-ingress
Namespace:                nginx-ingress
Labels:                   <none>
Annotations:              kubectl.kubernetes.io/last-applied-configuration:
                            {"apiVersion":"v1","kind":"Service","metadata":{"annotations":{"service.beta.kubernetes.io/aws-load-balancer-backend-protocol":"tcp","serv...
                          service.beta.kubernetes.io/aws-load-balancer-backend-protocol: tcp
                          service.beta.kubernetes.io/aws-load-balancer-proxy-protocol: *
Selector:                 app=nginx-ingress
Type:                     LoadBalancer
IP:                       100.65.77.245
LoadBalancer Ingress:     a2f716bdfecc511e8a8c012eeeb1f048-510663921.us-east-1.elb.amazonaws.com
Port:                     http  80/TCP
TargetPort:               80/TCP
NodePort:                 http  30696/TCP
Endpoints:                100.96.2.2:80
Port:                     https  443/TCP
TargetPort:               443/TCP
NodePort:                 https  32045/TCP
Endpoints:                100.96.2.2:443
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type    Reason                Age   From                Message
  ----    ------                ----  ----                -------
  Normal  EnsuringLoadBalancer  15s   service-controller  Ensuring load balancer
  Normal  EnsuredLoadBalancer   13s   service-controller  Ensured load balancer
```

Y nos ha creado un ELB
```
[ec2-user@ip-172-31-22-115 deployments]$ kubectl get services --all-namespaces
NAMESPACE       NAME            TYPE           CLUSTER-IP      EXTERNAL-IP                                                              PORT(S)                      AGE
default         kubernetes      ClusterIP      100.64.0.1      <none>                                                                   443/TCP                      1h
kube-system     kube-dns        ClusterIP      100.64.0.10     <none>                                                                   53/UDP,53/TCP                1h
nginx-ingress   nginx-ingress   LoadBalancer   100.65.77.245   a2f716bdfecc511e8a8c012eeeb1f048-510663921.us-east-1.elb.amazonaws.com   80:30696/TCP,443:32045/TCP   6m
```

Que balanceará entre las instancias de computo de nuestro cluster
```
[ec2-user@ip-172-31-22-115 deployments]$ nslookup a2f716bdfecc511e8a8c012eeeb1f048-510663921.us-east-1.elb.amazonaws.com
Server:         172.31.0.2
Address:        172.31.0.2#53

Non-authoritative answer:
Name:   a2f716bdfecc511e8a8c012eeeb1f048-510663921.us-east-1.elb.amazonaws.com
Address: XXXXXXXXXXXXXX
Name:   a2f716bdfecc511e8a8c012eeeb1f048-510663921.us-east-1.elb.amazonaws.com
Address: YYYYYYYYYYYYYY
```

```
[ec2-user@ip-172-31-22-115 ~]$ curl a2f716bdfecc511e8a8c012eeeb1f048-510663921.us-east-1.elb.amazonaws.com
<html>
<head><title>404 Not Found</title></head>
<body>
<center><h1>404 Not Found</h1></center>
<hr><center>nginx/1.15.6</center>
</body>
</html>
```

Instalando ejemplo tea y cafe
-----------------------------

https://github.com/nginxinc/kubernetes-ingress/issues/450


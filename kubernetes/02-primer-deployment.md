Nuestro primer deployment en kubernetes
=======================================
Creamos nuestro primer deployment a partir de la siguiente plantilla en yaml.
```
[root@master ~]# more nginx-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```

Lo ejecutamos asi
```
[root@master ~]# kubectl create -f nginx-deployment.yaml
deployment.apps/nginx-deployment created
```

Y podemos ver su configuración con la siguiente instrucción
```
[root@master ~]# kubectl describe deployment nginx-deployment
Name:                   nginx-deployment
Namespace:              default
CreationTimestamp:      Tue, 30 Oct 2018 15:16:38 +0000
Labels:                 <none>
Annotations:            deployment.kubernetes.io/revision: 1
                        kubectl.kubernetes.io/last-applied-configuration:
                          {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"nginx-deployment","namespace":"default"},"spec":{"replica...
Selector:               app=nginx
Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=nginx
  Containers:
   nginx:
    Image:        nginx:1.7.9
    Port:         80/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   nginx-deployment-5c689d88bb (2/2 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  73s   deployment-controller  Scaled up replica set nginx-deployment-5c689d88bb to 2
```
Y sus pods asociados
```
[root@master ~]# kubectl get pods -l app=nginx
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-5c689d88bb-2c9ch   1/1     Running   0          9m1s
nginx-deployment-5c689d88bb-bpswp   1/1     Running   0          9m1s
```

Para ver una replica en concreto
```
[root@master ~]# kubectl describe pod nginx-deployment-5c689d88bb-2c9ch
Name:               nginx-deployment-5c689d88bb-2c9ch
Namespace:          default
Priority:           0
PriorityClassName:  <none>
Node:               node2/10.0.1.7
Start Time:         Tue, 30 Oct 2018 15:16:39 +0000
Labels:             app=nginx
                    pod-template-hash=5c689d88bb
Annotations:        <none>
Status:             Running
IP:                 10.47.0.1
Controlled By:      ReplicaSet/nginx-deployment-5c689d88bb
Containers:
  nginx:
    Container ID:   docker://af4a2864a068c54333ce73c90fc708276ddd27af9122bc058b7a2c28f3466023
    Image:          nginx:1.7.9
    Image ID:       docker-pullable://docker.io/nginx@sha256:e3456c851a152494c3e4ff5fcc26f240206abac0c9d794affb40e0714846c451
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Tue, 30 Oct 2018 15:16:49 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-qsgvl (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  default-token-qsgvl:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-qsgvl
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  15m   default-scheduler  Successfully assigned default/nginx-deployment-5c689d88bb-2c9ch to node2
  Normal  Pulling    15m   kubelet, node2     pulling image "nginx:1.7.9"
  Normal  Pulled     14m   kubelet, node2     Successfully pulled image "nginx:1.7.9"
  Normal  Created    14m   kubelet, node2     Created container
  Normal  Started    14m   kubelet, node2     Started container
```

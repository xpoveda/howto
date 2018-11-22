Creando un cluster de kubernetes con kops en AWS
================================================

¿Que es kops?
-------------
Mediante kops podemos crear un cluster de kubernetes en AWS y operar con él.
La documentación principal de instalación se encuentra en: https://github.com/kubernetes/kops/blob/master/docs/install.md

En ella se comentan los siguientes pasos que tambien se comentan aquí a continuación, aunque en este documento la información es mucho más concreta.

Tenemos su repositorio principal en: https://github.com/kubernetes/kops

Preparando kops
===============

Preparando el entorno de instalación de kops
--------------------------------------------
Primero de todo partiremos de una AMI de Amazon que tenga linux instalado, de esta forma podremos atacar la maquina EC2 en remoto
y no tendremos problemas con el proxy. Aunque todo esto tambien lo podriamos hacer con maquinas virtuales en local con vagrant, vmware o hyperv
siempre que tengan acceso a internet o tengan configurado el proxy correctamente. Como ya hemos comprobado que la configuracion por proxy
es muy compleja y no está demasiado documentada en internet optamos por la instalación desde una EC2 directamente.

En esta maquina viene por defecto aws cli, java, docker, etc, por lo que tendremos que hacer muy pocas modificaciones, a parte del propio kops, para
que sea plenamente operativa.

En nuestro caso  hemos cogido la: `Amazon Linux 2 AMI (HVM), SSD Volume Type - ami-0a5e707736615003c` en la que se comenta:
Amazon Linux 2 comes with five years support. It provides Linux kernel 4.14 tuned for optimal performance on Amazon EC2, systemd 219, GCC 7.3, Glibc 2.26, Binutils 2.29.1, and the latest software packages through extras.

Instalaremos primero de el binario de kops y kubectl tal y como se comenta en: https://github.com/kubernetes/kops/blob/master/docs/install.md

En esta página tambien se explica como instalar el aws cli si nuestro host no lo tuviera instalado por defecto.

Esta maquina no formará parte del cluster de kubernetes, es simplemente donde instalaremos kops para poder operar con él.

Creando el usuario y grupo kops
-------------------------------
Accedemos a EC2 via dns, con el usuario `ec2-user` y la clave automatica que tiene alojada putty de la forma habitual.

Creamos en IAM de la consola de AWS un acces key en nuestro usuario de acceso a AWS y nos descargamos el csv con la key y el secret para poder
operar más tarde.

Configuramos el aws cli para que vaya contra esa key y secret.
En nuestro caso la key es XXXXXXXXXXXXXXXXXX y la secret YYYYYYYYYYYYYYYYYYYY.
```
[ec2-user@ip-172-31-22-115 ~]$ aws configure
AWS Access Key ID [None]: XXXXXXXXXXXXXXXXXX
AWS Secret Access Key [None]: YYYYYYYYYYYYYYYYYYYY
Default region name [None]:
Default output format [None]: json
```

Creamos el grupo kops
```
[ec2-user@ip-172-31-22-115 ~]$ aws iam create-group --group-name kops
```

Nuestro usuario en aws tiene que tener todos estos permisos dados de alta y se los transferiremos al grupo kops
```
[ec2-user@ip-172-31-22-115 ~]$ aws iam attach-group-policy --policy-arn arn:aws:iam::aws:policy/AmazonEC2FullAccess --group-name kops
[ec2-user@ip-172-31-22-115 ~]$ aws iam attach-group-policy --policy-arn arn:aws:iam::aws:policy/AmazonRoute53FullAccess --group-name kops
[ec2-user@ip-172-31-22-115 ~]$ aws iam attach-group-policy --policy-arn arn:aws:iam::aws:policy/AmazonS3FullAccess --group-name kops
[ec2-user@ip-172-31-22-115 ~]$ aws iam attach-group-policy --policy-arn arn:aws:iam::aws:policy/IAMFullAccess --group-name kops
[ec2-user@ip-172-31-22-115 ~]$ aws iam attach-group-policy --policy-arn arn:aws:iam::aws:policy/AmazonVPCFullAccess --group-name kops
```

Creamos tambien un usuario kops
```
[ec2-user@ip-172-31-22-115 ~]$ aws iam create-user --user-name kops
```

Al que le damos todos los permisos y lo asignamos al grupo kops tambien.
```
[ec2-user@ip-172-31-22-115 ~]$ aws iam add-user-to-group --user-name kops --group-name kops
```

Podemos comprobar los usuarios que estan dados de alta así..
```
[ec2-user@ip-172-31-22-115 ~]$ aws iam list-users
```

Generando la key y secret para el usuario kops
----------------------------------------------
Y generamos una key y su secret correspondiente que nos guardaremos en lugar seguro.
```
[ec2-user@ip-172-31-22-115 ~]$ aws iam create-access-key --user-name kops
{
    "AccessKey": {
        "UserName": "kops",
        "Status": "Active",
        "CreateDate": "2018-11-13T09:49:39Z",
        "SecretAccessKey": "BBBBBBBBBBBBBBBBBBBBBBBBB",
        "AccessKeyId": "AAAAAAAAAAAAAAAAAAAAAAA"
    }
}
```

Configurando el DNS
===================

Creando la hosted zone para nuestro cluster
-------------------------------------------
Podemos ver las hosted-zones que tenemos en AWS con el siguiente comando.
```
[ec2-user@ip-172-31-22-115 ~]$ aws route53 list-hosted-zones
```

Crearemos una hosted zone para el subdominio `k8s.escalamas.com` que es donde ubicaremos nuestro cluster.

Instalamos la herramienta `jq` que nos sirve para crear las hosted zones con cli
```
[ec2-user@ip-172-31-22-115 ~]$ sudo yum install jq
```

mediante el siguiente comando
```
[ec2-user@ip-172-31-22-115 ~]$ ID=$(uuidgen) && aws route53 create-hosted-zone --name k8s.escalamas.com --caller-reference $ID | jq .DelegationSet.NameServers
```

En nuestro caso tenemos un dominio comprado en godaddy. Lo que hacemos es mantener el dominio principal alli (por lo tanto no hay que modificar sus
nameservers) y lo que haremos es crear 4 registros de tipo NS con NAME "k8s" que apunten a cada uno de los 4 nameservers que nos ha devuelto la lista.

La propagación puede tardar entre 24 y 48 horas. Es importante que la propagación haya sido lo mas extensa posible porque sino, como trabajaremos
con dns publicos, el cluster caerá periodicamente cuando no se resuelva el subdominio correctamente.

Comprobamos que la propagación ha sido correcta con herramientas como https://whatsmydns.com o directamente con `dig`
```
[ec2-user@ip-172-31-22-115 ~]$ dig ns k8s.escalamas.com

; <<>> DiG 9.9.4-RedHat-9.9.4-61.amzn2.1.1 <<>> ns k8s.escalamas.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 63765
;; flags: qr rd ra; QUERY: 1, ANSWER: 4, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;k8s.escalamas.com.             IN      NS

;; ANSWER SECTION:
k8s.escalamas.com.      60      IN      NS      ns-1694.awsdns-19.co.uk.
k8s.escalamas.com.      60      IN      NS      ns-452.awsdns-56.com.
k8s.escalamas.com.      60      IN      NS      ns-520.awsdns-01.net.
k8s.escalamas.com.      60      IN      NS      ns-1277.awsdns-31.org.

;; Query time: 29 msec
;; SERVER: 172.31.0.2#53(172.31.0.2)
;; WHEN: Wed Nov 14 17:01:14 UTC 2018
;; MSG SIZE  rcvd: 183
```

Instalando el cluster
=====================
Instalaremos nuestro cluster en la region `us-east-1`. Podemos hacerla en aquella que creamos conveniente.

Aqui tenemos un listado con las regiones donde podemos ubicar los distintos servicios https://docs.aws.amazon.com/general/latest/gr/rande.html

Para kops creo que solo es valido en las regiones que marca el `Amazon Elastic Container Service for Kubernetes (Amazon EKS)`.

```
[ec2-user@ip-172-31-22-115 ~]$ aws configure
AWS Access Key ID [None]: XXXXXXXXXXXXXXXXXX
AWS Secret Access Key [None]: YYYYYYYYYYYYYYYYYYYY
Default region name [None]: us-east-1
Default output format [None]:
```

```
[ec2-user@ip-172-31-22-115 ~]$ bucket_name=k8s.escalamas.com
[ec2-user@ip-172-31-22-115 ~]$ aws s3api create-bucket --bucket ${bucket_name} --region us-east-1
[ec2-user@ip-172-31-22-115 ~]$ aws s3api put-bucket-versioning --bucket ${bucket_name} --versioning-configuration Status=Enabled
```

```
[ec2-user@ip-172-31-22-115 ~]$ export KOPS_CLUSTER_NAME=k8s.escalamas.com
[ec2-user@ip-172-31-22-115 ~]$ export KOPS_STATE_STORE=s3://${bucket_name}
[ec2-user@ip-172-31-22-115 ~]$ export AWS_ACCESS_KEY_ID=$(aws configure get aws_access_key_id)
[ec2-user@ip-172-31-22-115 ~]$ export AWS_SECRET_ACCESS_KEY=$(aws configure get aws_secret_access_key)
```

Modificaremos el `.bash_profile` para que cada vez que reiniciemos la EC2 tengamos el nombre del cluster y el bucket inicializados.
Sino siempre podemos inicializar esos dos export manualmente cada vez.
```
export bucket_name=k8s.escalamas.com
export KOPS_CLUSTER_NAME=k8s.escalamas.com
export KOPS_STATE_STORE=s3://k8s.escalamas.com
[ec2-user@ip-172-31-22-115 ~]$ more .bash_profile
```

Creamos el cluster. Nos dará un error por no tener definida una secret donde él se lo espera, lo arreglaremos a continuación.
```
[ec2-user@ip-172-31-22-115 ~]$ kops create cluster --node-count=2 --node-size=t2.medium --zones=us-east-1a --name=${KOPS_CLUSTER_NAME}
```

Creamos la secret mediante una clave publica que ya teniamos en ~/.ssh/authorized_keys.
Ahi tenemos la clave publica con la que generamos nuestra EC2 y a la que accedemos via putty con la clave privada.
En este caso la aprovechamos pero bien podriamos crear una nueva con herramientas como `putty-gen` o `ssh-keygen`.
```
[ec2-user@ip-172-31-22-115 .ssh]$ kops create secret --name k8s.escalamas.com sshpublickey admin -i ~/.ssh/authorized_keys
```

Actualizamos el cluster
```
[ec2-user@ip-172-31-22-115 ~]$ kops update cluster --name ${KOPS_CLUSTER_NAME} --yes
I1113 17:47:14.293168     902 executor.go:103] Tasks: 0 done / 73 total; 31 can run
I1113 17:47:15.566546     902 vfs_castore.go:735] Issuing new certificate: "apiserver-aggregator-ca"
I1113 17:47:15.627426     902 vfs_castore.go:735] Issuing new certificate: "ca"
I1113 17:47:16.562826     902 executor.go:103] Tasks: 31 done / 73 total; 24 can run
I1113 17:47:18.322195     902 vfs_castore.go:735] Issuing new certificate: "kube-controller-manager"
I1113 17:47:18.585751     902 vfs_castore.go:735] Issuing new certificate: "master"
I1113 17:47:18.766034     902 vfs_castore.go:735] Issuing new certificate: "kubelet"
I1113 17:47:18.897714     902 vfs_castore.go:735] Issuing new certificate: "kops"
I1113 17:47:19.107191     902 vfs_castore.go:735] Issuing new certificate: "kubelet-api"
I1113 17:47:19.245885     902 vfs_castore.go:735] Issuing new certificate: "apiserver-aggregator"
I1113 17:47:19.607054     902 vfs_castore.go:735] Issuing new certificate: "apiserver-proxy-client"
I1113 17:47:20.008528     902 vfs_castore.go:735] Issuing new certificate: "kube-scheduler"
I1113 17:47:20.074534     902 vfs_castore.go:735] Issuing new certificate: "kube-proxy"
I1113 17:47:20.548779     902 vfs_castore.go:735] Issuing new certificate: "kubecfg"
I1113 17:47:21.346090     902 executor.go:103] Tasks: 55 done / 73 total; 16 can run
I1113 17:47:22.885798     902 executor.go:103] Tasks: 71 done / 73 total; 2 can run
I1113 17:47:24.182630     902 executor.go:103] Tasks: 73 done / 73 total; 0 can run
I1113 17:47:24.182765     902 dns.go:153] Pre-creating DNS records
I1113 17:47:24.807157     902 update_cluster.go:290] Exporting kubecfg for cluster
kops has set your kubectl context to k8s.escalamas.com

Cluster is starting.  It should be ready in a few minutes.

Suggestions:
 * validate cluster: kops validate cluster
 * list nodes: kubectl get nodes --show-labels
 * ssh to the master: ssh -i ~/.ssh/id_rsa admin@api.k8s.escalamas.com
 * the admin user is specific to Debian. If not using Debian please use the appropriate user based on your OS.
 * read about installing addons at: https://github.com/kubernetes/kops/blob/master/docs/addons.md.
```

Pasaran unos cuantos minutos y kops generará automaticamente toda la infraestructura que compone el cluster.
En nuestro caso estará en la region de Virginia. 
```
[ec2-user@ip-172-31-22-115 ~]$ kops validate cluster
Validating cluster k8s.escalamas.com

INSTANCE GROUPS
NAME                    ROLE    MACHINETYPE     MIN     MAX     SUBNETS
master-us-east-1a       Master  m3.medium       1       1       us-east-1a
nodes                   Node    t2.medium       2       2       us-east-1a

NODE STATUS
NAME                            ROLE    READY
ip-172-20-34-139.ec2.internal   node    True
ip-172-20-48-98.ec2.internal    node    True
ip-172-20-55-7.ec2.internal     master  True

Your cluster k8s.escalamas.com is ready
```

Y al que podemos acceder normalmente con `kubectl`
```
[ec2-user@ip-172-31-22-115 ~]$ kubectl get nodes
NAME                            STATUS   ROLES    AGE   VERSION
ip-172-20-34-139.ec2.internal   Ready    node     1h    v1.10.6
ip-172-20-48-98.ec2.internal    Ready    node     1h    v1.10.6
ip-172-20-55-7.ec2.internal     Ready    master   1h    v1.10.6
```

Borrando el cluster
===================
Si queremos empezar desde cero la instalacion borraremos el cluster que hemos creado y el bucket que se necesita para almacenar su configuracion.
```
kops delete cluster --name k8s.escalamas.com --yes
aws s3 rm s3://k8s.escalamas.com --recursive
```

Recursos
========
* https://github.com/kubernetes/kops/blob/master/docs/aws.md
* https://aws.amazon.com/blogs/compute/kubernetes-clusters-aws-kops/
* https://medium.com/containermind/how-to-create-a-kubernetes-cluster-on-aws-in-few-minutes-89dda10354f4
* https://www.youtube.com/watch?v=IImQrJWbaDo
* https://www.youtube.com/watch?v=4YBzMZY4QX4

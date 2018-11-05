Creando nuestra infraestructura en Azure
========================================

* Creamos el resource manager
* Creamos la clave publicas con puttygen y tambien la privada con passphrase
* Nos las guardamos las dos

Creamos n-maquinas con el sistema operativo Centos.

* Creamos la maquina
* Creamos el dns
* Habilitamos el autoshutdown
* Accedemos a la maquina via putty

Configurando el cluster de kubernetes
=====================================
Entramos en los tres nodos como root con "sudo -i".

En todos los nodos realizar las siguientes acciones.....

Deshabilitar la swap
--------------------
```
Temporalmente: swapoff -a
Definitivamente: vi /etc/fstab y comentar la linea donde ponga swap
```

Establecer hostnames
--------------------
```
[root@master etc]# more hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

10.0.1.5 master
10.0.1.6 node1
10.0.1.7 node2
```

Deshabilitar bridge
-------------------
```
[root@master etc]# cat <<EOF > /etc/sysctl.d/k8s.conf
> net.bridge.bridge-nf-call-ip6tables = 1
> net.bridge.bridge-nf-call-iptables = 1
> EOF


[root@master etc]# sysctl --system
* Applying /usr/lib/sysctl.d/00-system.conf ...
* Applying /usr/lib/sysctl.d/10-default-yama-scope.conf ...
kernel.yama.ptrace_scope = 0
* Applying /usr/lib/sysctl.d/50-default.conf ...
kernel.sysrq = 16
kernel.core_uses_pid = 1
net.ipv4.conf.default.rp_filter = 1
net.ipv4.conf.all.rp_filter = 1
net.ipv4.conf.default.accept_source_route = 0
net.ipv4.conf.all.accept_source_route = 0
net.ipv4.conf.default.promote_secondaries = 1
net.ipv4.conf.all.promote_secondaries = 1
fs.protected_hardlinks = 1
fs.protected_symlinks = 1
* Applying /etc/sysctl.d/99-sysctl.conf ...
* Applying /etc/sysctl.d/k8s.conf ...
* Applying /etc/sysctl.conf ...
```

Deshabilitar SELINUX
--------------------
Modificar /etc/selinux/config y poner en lugar de `SELINUX=enforcing` poner `SELINUX=disabled` seguidamente hacer un "reboot"

Despues volvemos a entrar a la maquina y ha de aparecer
```
[root@master ~]# sestatus
SELinux status:                 disabled
```

Instalamos docker
-----------------
```
yum install -y docker


[root@master ~]# docker -v
Docker version 1.13.1, build 8633870/1.13.1


[root@master ~]# systemctl enable docker && systemctl start docker
Created symlink from /etc/systemd/system/multi-user.target.wants/docker.service to /usr/lib/systemd/system/docker.service.
```

Instalamos kubernetes
---------------------
```
[root@master ~]# cat <<EOF > /etc/yum.repos.d/kubernetes.repo
> [kubernetes]
> name=Kubernetes
> baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
> enabled=1
> gpgcheck=1
> repo_gpgcheck=1
> gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
> EOF

[root@master ~]# yum install -y kubelet kubeadm kubectl


[root@master ~]# systemctl enable kubelet && systemctl start kubelet
Created symlink from /etc/systemd/system/multi-user.target.wants/kubelet.service to /etc/systemd/system/kubelet.service.
```

Inicializamos el cluster
------------------------
Solo en el nodo master inicializamos el cluster
```
[root@master ~]# kubeadm init
[init] using Kubernetes version: v1.12.2
[preflight] running pre-flight checks
[preflight/images] Pulling images required for setting up a Kubernetes cluster
[preflight/images] This might take a minute or two, depending on the speed of your internet connection
[preflight/images] You can also perform this action in beforehand using 'kubeadm config images pull'
[kubelet] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[preflight] Activating the kubelet service
[certificates] Generated etcd/ca certificate and key.
[certificates] Generated etcd/peer certificate and key.
[certificates] etcd/peer serving cert is signed for DNS names [master localhost] and IPs [10.0.1.5 127.0.0.1 ::1]
[certificates] Generated etcd/server certificate and key.
[certificates] etcd/server serving cert is signed for DNS names [master localhost] and IPs [127.0.0.1 ::1]
[certificates] Generated etcd/healthcheck-client certificate and key.
[certificates] Generated apiserver-etcd-client certificate and key.
[certificates] Generated ca certificate and key.
[certificates] Generated apiserver-kubelet-client certificate and key.
[certificates] Generated apiserver certificate and key.
[certificates] apiserver serving cert is signed for DNS names [master kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local] and IPs [10.96.0.1 10.0.1.5]
[certificates] Generated front-proxy-ca certificate and key.
[certificates] Generated front-proxy-client certificate and key.
[certificates] valid certificates and keys now exist in "/etc/kubernetes/pki"
[certificates] Generated sa key and public key.
[kubeconfig] Wrote KubeConfig file to disk: "/etc/kubernetes/admin.conf"
[kubeconfig] Wrote KubeConfig file to disk: "/etc/kubernetes/kubelet.conf"
[kubeconfig] Wrote KubeConfig file to disk: "/etc/kubernetes/controller-manager.conf"
[kubeconfig] Wrote KubeConfig file to disk: "/etc/kubernetes/scheduler.conf"
[controlplane] wrote Static Pod manifest for component kube-apiserver to "/etc/kubernetes/manifests/kube-apiserver.yaml"
[controlplane] wrote Static Pod manifest for component kube-controller-manager to "/etc/kubernetes/manifests/kube-controller-manager.yaml"
[controlplane] wrote Static Pod manifest for component kube-scheduler to "/etc/kubernetes/manifests/kube-scheduler.yaml"
[etcd] Wrote Static Pod manifest for a local etcd instance to "/etc/kubernetes/manifests/etcd.yaml"
[init] waiting for the kubelet to boot up the control plane as Static Pods from directory "/etc/kubernetes/manifests"
[init] this might take a minute or longer if the control plane images have to be pulled
[apiclient] All control plane components are healthy after 32.002417 seconds
[uploadconfig] storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
[kubelet] Creating a ConfigMap "kubelet-config-1.12" in namespace kube-system with the configuration for the kubelets in the cluster
[markmaster] Marking the node master as master by adding the label "node-role.kubernetes.io/master=''"
[markmaster] Marking the node master as master by adding the taints [node-role.kubernetes.io/master:NoSchedule]
[patchnode] Uploading the CRI Socket information "/var/run/dockershim.sock" to the Node API object "master" as an annotation
[bootstraptoken] using token: 41xhre.danqz6huq85cavux
[bootstraptoken] configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
[bootstraptoken] configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
[bootstraptoken] configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
[bootstraptoken] creating the "cluster-info" ConfigMap in the "kube-public" namespace
[addons] Applied essential addon: CoreDNS
[addons] Applied essential addon: kube-proxy

Your Kubernetes master has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

You can now join any number of machines by running the following on each node
as root:

  kubeadm join 10.0.1.5:6443 --token 41xhre.danqz6huq85cavux --discovery-token-ca-cert-hash sha256:4d5c3aea238f237a76d7d58beebfb670bd1d8706a65569fcce648aaf99b9ceac
```

Y creamos la red de comunicacion
```
[root@master ~]# mkdir -p $HOME/.kube
[root@master ~]# sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
[root@master ~]# sudo chown $(id -u):$(id -g) $HOME/.kube/config

[root@master ~]# kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
serviceaccount/weave-net configured
clusterrole.rbac.authorization.k8s.io/weave-net configured
clusterrolebinding.rbac.authorization.k8s.io/weave-net configured
role.rbac.authorization.k8s.io/weave-net configured
rolebinding.rbac.authorization.k8s.io/weave-net configured
daemonset.extensions/weave-net configured
```

Si queremos ver todos los namespaces habilitados que forman el cluster
```
[root@master ~]# kubectl get pods --all-namespaces
NAMESPACE     NAME                             READY   STATUS    RESTARTS   AGE
kube-system   coredns-576cbf47c7-hbc5c         1/1     Running   0          8m10s
kube-system   coredns-576cbf47c7-hhm2l         1/1     Running   0          8m10s
kube-system   etcd-master                      1/1     Running   0          107s
kube-system   kube-apiserver-master            1/1     Running   0          108s
kube-system   kube-controller-manager-master   1/1     Running   0          106s
kube-system   kube-proxy-s6ltj                 1/1     Running   0          13m
kube-system   kube-scheduler-master            1/1     Running   0          105s
kube-system   weave-net-56bdj                  2/2     Running   0          2m15s
```


O queremos convertir master tambien a nodo de cómputo
```
kubectl taint nodes --all node-role.kubernetes.io/master-
```

Añadir nodos a nuestro cluster de kubernetes
--------------------------------------------

En cada nodo hemos de tenir instalado docker y kubernetes como hemos comentado anteriormente y a continuacion utilizar la sentencia que generó kubernetes al crear el cluster.
```
kubeadm join 10.0.1.5:6443 --token 41xhre.danqz6huq85cavux --discovery-token-ca-cert-hash sha256:4d5c3aea238f237a76d7d58beebfb670bd1d8706a65569fcce648aaf99b9ceac
```

De esta forma
```
[root@node1 ~]# kubeadm join 10.0.1.5:6443 --token 41xhre.danqz6huq85cavux --dis                                                                                                                                                             covery-token-ca-cert-hash sha256:4d5c3aea238f237a76d7d58beebfb670bd1d8706a65569f                                                                                                                                                             cce648aaf99b9ceac
[preflight] running pre-flight checks
        [WARNING RequiredIPVSKernelModulesAvailable]: the IPVS proxier will not                                                                                                                                                              be used, because the following required kernel modules are not loaded: [ip_vs_wr                                                                                                                                                             r ip_vs_sh ip_vs ip_vs_rr] or no builtin kernel ipvs support: map[ip_vs_rr:{} ip                                                                                                                                                             _vs_wrr:{} ip_vs_sh:{} nf_conntrack_ipv4:{} ip_vs:{}]
you can solve this problem with following methods:
 1. Run 'modprobe -- ' to load missing kernel modules;
2. Provide the missing builtin kernel ipvs support

[discovery] Trying to connect to API Server "10.0.1.5:6443"
[discovery] Created cluster-info discovery client, requesting info from "https:/                                                                                                                                                             /10.0.1.5:6443"
[discovery] Requesting info from "https://10.0.1.5:6443" again to validate TLS a                                                                                                                                                             gainst the pinned public key
[discovery] Cluster info signature and contents are valid and TLS certificate va                                                                                                                                                             lidates against pinned roots, will use API Server "10.0.1.5:6443"
[discovery] Successfully established connection with API Server "10.0.1.5:6443"
[kubelet] Downloading configuration for the kubelet from the "kubelet-config-1.1                                                                                                                                                             2" ConfigMap in the kube-system namespace
[kubelet] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet] Writing kubelet environment file with flags to file "/var/lib/kubelet/                                                                                                                                                             kubeadm-flags.env"
[preflight] Activating the kubelet service
[tlsbootstrap] Waiting for the kubelet to perform the TLS Bootstrap...
[patchnode] Uploading the CRI Socket information "/var/run/dockershim.sock" to t                                                                                                                                                             he Node API object "node1" as an annotation

This node has joined the cluster:
* Certificate signing request was sent to apiserver and a response was received.
* The Kubelet was informed of the new secure connection details.

Run 'kubectl get nodes' on the master to see this node join the cluster.
```

Despues en el nodo master veremos en aprox 1 minuto como los nodos estan activos
```
[root@master ~]# kubectl get nodes
NAME     STATUS   ROLES    AGE   VERSION
master   Ready    master   42m   v1.12.2
node1    Ready    <none>   21m   v1.12.2
node2    Ready    <none>   53s   v1.12.2
```

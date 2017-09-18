
```
#https://kubernetes.io/docs/tasks/tools/install-minikube/
#https://kubernetes.io/docs/tasks/tools/install-kubectl/
#curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl

#https://github.com/kubernetes/minikube/releases
curl -Lo minikube https://storage.googleapis.com/minikube/releases/v0.22.0/minikube-linux-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/

ubuntu@ubuntu:~$ minikube start

ubuntu@ubuntu:~$ minikube start
Starting local Kubernetes v1.7.5 cluster...
Starting VM...
Downloading Minikube ISO
 106.36 MB / 106.36 MB [============================================] 100.00% 0s
E0911 14:01:24.150463   60970 start.go:143] Error starting host: Error creating host: Error executing step: Running precreate checks.
: VBoxManage not found. Make sure VirtualBox is installed and VBoxManage is in the path.

 Retrying.
E0911 14:01:24.150732   60970 start.go:149] Error starting host:  Error creating host: Error executing step: Running precreate checks.
: VBoxManage not found. Make sure VirtualBox is installed and VBoxManage is in the path
================================================================================
An error has occurred. Would you like to opt in to sending anonymized crash
information to minikube to help prevent future errors?
To opt out of these messages, run the command:
        minikube config set WantReportErrorPrompt false
================================================================================
Please enter your response [Y/n]:
Y
```


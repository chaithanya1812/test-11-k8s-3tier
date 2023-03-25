# Project-11(Deploy 3 tier java-application into K8S)
![k8sproject](https://user-images.githubusercontent.com/111736742/227664484-d83d8f4e-8f91-4e8f-aa65-1239369df768.jpg)
# K8S-Cluster-Setup with RHEL
![k8s-installation](https://user-images.githubusercontent.com/111736742/227599293-3d912943-77a7-4cfe-9b6f-9558b998c2e0.jpg)
## K8S-Installation
#step-1

Note:- Installing container run-time in 3 nodes is required
```bash
  sudo su -
  yum update -y
  yum install docker -y
  systemctl start docker
  systemctl enable docker
  #turn-off swapmemory
  swapoff -a
  sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
  ###########################################
  Executing commands from k8S Offical page
  ############################################
  cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
 [kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-\$basearch
enabled=1
gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
exclude=kubelet kubeadm kubectl
EOF

# Set SELinux in permissive mode (effectively disabling it)
sudo setenforce 0
sudo sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config

sudo yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes

sudo systemctl enable --now kubelet

  ```
  #step-2
  Note:- Execute this command only in control-plane(master)
```bash
kubeadm init  
#After sometime you get the following details!
```
```bash
Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

mkdir -p $HOME/.kube sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config sudo chown 
(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster. Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at: https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 172.31.37.1:6443 --token uzta67.iuvv06gz4p99teoi
--discovery-token-ca-cert-hash sha256:1df96aa5f27ce442b1e060af881dcc74a007ea83a0cb493090c30efa6ff0cda2

```
#step-3
Note:- from that data execute the following commands in master itself !..
```bash
#To start using your cluster, you need to run the following as a regular user:

sudo su -

mkdir -p $HOME/.kube

sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config

sudo chown $(id -u):$(id -g) $HOME/.kube/config
#Extra one command for establishing any network(like weave-network)

kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml

```  
step-4
Note:- This generated token you have to run worker on nodes! 
```bash
#Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 172.31.37.1:6443 --token uzta67.iuvv06gz4p99teoi \
        --discovery-token-ca-cert-hash sha256:1df96aa5f27ce442b1e060af881dcc74a007ea83a0cb493090c30efa6ff0cda
```
#After Successfull installation of k8s type the following commands in master!
```bash
kubectl get  nodes
#you the get following data!
```
![k8sfirst](https://user-images.githubusercontent.com/111736742/227609817-a5d50e12-a950-4962-8f4a-d4fc8e6c3277.png)
```bash
kubectl get all -n kube-system
#you the get following data!
```
![k8ssecond](https://user-images.githubusercontent.com/111736742/227610202-6f5384ff-7b2e-4b74-bf35-3521bc5db4e5.png)

# K8s-Cluster setup is done!!!!!!!!!!!!!!!!
# --------------------------------------------------
# In k8s cluster we using storage classes resource for creating Persistent volumes dynamically provisioning in nfs(network file system).
#Launch  1-instance with required cpu,memory and make that as nfs-sever
![k8snfsserver](https://user-images.githubusercontent.com/111736742/227645773-bd7cca74-1175-4a87-9e12-66294c9ce10d.png)
## nfs-Server-Installation
```bash
    sudo su -
    yum update -y
    yum install nfs-utils -y
    systemctl start nfs-server.service
    mkdir -p  /nfs/nfs_tier
    chown -R nobody:nobody  /nfs/nfs_tier
    vi /etc/exports
    #------------------------------------------
    #in exports file place the following content and then exit
    /nfs/nfs_tier *(rw,sync,no_all_squash,no_root_squash)
    #------------------------------------------
    exportfs -arv
    exportfs -s
    systemctl restart nfs-server.service
```
## nfs-client-Installation
#Note:-Execute these coomand on k8s nodes
```bash
  yum install install nfs-utils nfs4-acl-tools -y
```
# Sonarqube download & Installation
```bash
yum update -y
yum install java -y
cd /opt
wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-8.9.10.61524.zip
unzip sonarqube-8.9.10.61524.zip
mv sonarqube-8.9.10.61524 sonarqube
useradd chaitu
passwd chaitu
chown -R chaitu:chaitu sonarqube
cd /opt/sonarqube/bin/linux-x86-64
vi sonar.sh 
--------------
edit this
#RUN_AS_USER=
RUN_AS_USER=chaitu
su - chaitu
[And start SonarQube]
cd /opt/sonarqube/bin/linux-x86-64
sh sonar.sh start
sh sonar.sh status
ip:9000
```
# Nexus download & Installation
```bash
yum install java-1.8* -y
cd /opt
wget https://download.sonatype.com/nexus/3/nexus-3.47.1-01-unix.tar.gz
tar -xvzf  nexus-3.47.1-01-unix.tar.gz
mv nexus-3.47.1-01 nexus
useradd chaitu
passwd chaitu
chown -R chaitu:chaitu nexus
chown -R chaitu:chaitu sonatype-work
cd /opt/nexus/bin
vi nexus.rc
--------edit---------
#run_as_user="chaitu"
----------edit--------
sh nexus start
sh nexus status
ip:8081
```
# Jenkins-Dashboard
![k8sjenkinsdashn](https://user-images.githubusercontent.com/111736742/227663263-ec5db860-46b3-48b9-99fc-d0720e656a60.png)
# Sonarqube-Report
![k8ssonarqube](https://user-images.githubusercontent.com/111736742/227663386-7edcd821-55e7-41b2-914a-8ae093f86aab.png)
# Nexus-Artifact Storge
![k8snexus](https://user-images.githubusercontent.com/111736742/227663432-e9f58003-f7d9-443c-b850-ce56b4375899.png)
# Docker-Hub Image
![k8sdockerhub](https://user-images.githubusercontent.com/111736742/227663608-34ae0c1a-7277-440b-ba22-90d270de9cc1.png)
# K8S-Control Plane
![k8ssecond](https://user-images.githubusercontent.com/111736742/227664236-21661d05-7a2c-4244-944d-9fca364b8906.png)
# nfs-server dynamically created persistent volume
![k8snfsvolume](https://user-images.githubusercontent.com/111736742/227664353-5e2759b8-105e-461b-acc3-ca15be059dbc.png)
# Final - Output without entering data!
![k8sbeforedat](https://user-images.githubusercontent.com/111736742/227663801-f1210588-6b6c-4098-81e4-ef56111c4740.png)
# Final - Output After entering data!
![k8sdataenter](https://user-images.githubusercontent.com/111736742/227663823-3316f58d-1d89-4ab7-840d-6fa70a20b58b.png)
# Even though our mongo database pod is delted we have our data
![k8snewpod](https://user-images.githubusercontent.com/111736742/227664108-8fe03eb2-b50a-417a-840e-889794075d34.png)
![k8sdataenter](https://user-images.githubusercontent.com/111736742/227664387-2e03ed27-b040-447e-84e3-c51b00ecc205.png)




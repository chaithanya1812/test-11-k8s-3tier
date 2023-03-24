# Project-11(Deploy 3 tier java-application into K8S)
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

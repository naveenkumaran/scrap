
# Setting up Kubernetes on RHEL using kubeadm 

## Setting up kubernetes master
### Add the Kubernetes repo
'''bash
cat << EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
exclude=kube*
EOF
'''

### Disable SELinux
'''bash
setenforce 0
'''

### Permanently disable SELinux:
'''bash
vi /etc/selinux/config
'''

### Change enforcing to disabled
'''bash
SELINUX=disabled
'''

### Install Kubernetes 1.11.3(At the time of this demo K1.11 was available)
'''bash
yum install -y kubelet-1.11.3 kubeadm-1.11.3 kubectl-1.11.3 kubernetes-cni-0.6.0 --disableexcludes=kubernetes
'''

### Start and enable the Kubernetes service
'''bash
systemctl start kubelet && systemctl enable kubelet
'''

### Create the k8s.conf file:
'''bash
cat << EOF >  /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
'''
'''bash
sysctl --system
'''

### Create kube-config.yml:
vi kube-config.yml
#### Add the following to kube-config.yml:
'''bash
apiVersion: kubeadm.k8s.io/v1alpha1
kind:
kubernetesVersion: "v1.11.3"
networking:
  podSubnet: 10.244.0.0/16
apiServerExtraArgs:
  service-node-port-range: 8000-31274
'''

### Initialize Kubernetes
kubeadm init --config kube-config.yml

### Copy admin.conf to your home directory
'''bash
mkdir -p $HOME/.kube
cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
chown $(id -u):$(id -g) $HOME/.kube/config
'''
### Install Calico
'''bash
curl https://docs.projectcalico.org/manifests/calico.yaml -O
kubectl apply -f calico.yaml
'''
### Restart kubelet
'''bash
systemctl restart kubelet 
'''

## Setting up the Kubernetes Worker
Now that the setup for the Kubernetes master is complete, we will begin the process of configuring the worker node. The following actions will be executed on the Kubernetes worker.

### Disable swap
'''bash
swapoff -a
'''

### Edit: /etc/fstab
'''bash
vi /etc/fstab
'''

### Comment out swap
'''bash
#/root/swap swap swap sw 0 0
'''

### Add the Kubernetes repo
'''bash
cat << EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
exclude=kube*
EOF
'''

### Disable SELinux
'''bash
setenforce 0
'''

### Permanently disable SELinux:
'''bash
vi /etc/selinux/config
'''

### Change enforcing to disabled
'''bash
SELINUX=disabled
'''

### Install Kubernetes 1.11.3
'''bash
yum install -y kubelet-1.11.3 kubeadm-1.11.3 kubectl-1.11.3 kubernetes-cni-0.6.0 --disableexcludes=kubernetes
'''

### Start and enable the Kubernetes service
'''bash
systemctl start kubelet && systemctl enable kubelet
'''

### Create the k8s.conf file:

'''bash
cat << EOF >  /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
'''

'''bash
sysctl --system
'''

### Use the join token to add the Worker Node to the cluster:
'''bash
kubeadm join < MASTER_IP >:6443 --token < TOKEN > --discovery-token-ca-cert-hash sha256:< HASH >
On the master node, test to see if the cluster was created properly.
'''

### Get a listing of the nodes:
'''bash
kubectl get nodes
'''
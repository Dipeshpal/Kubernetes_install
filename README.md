# Kubernetes_install
Kubernetes Installation guide


### Step 1: Install Docker on both the nodes

    sudo apt install docker.io
    
### Step 2: Enable Docker on both the nodes

    sudo systemctl enable docker

### Step 3: Add the Kubernetes signing key on both the nodes

    curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add

If Curl is not installed on your system, you can install it through the following command as root:

    sudo apt install curl
### Step 4: Add Xenial Kubernetes Repository on both the nodes

    sudo apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"

### Step 5: Install Kubeadm

    sudo apt install kubeadm

check version-

     kubeadm version

## Kubernetes Deployment

### Step 1: Disable swap memory (if running) on both the nodes

    sudo swapoff -a
    
### Step 2: Give Unique hostnames to each node

   Run the following command in the master node in order to give it a unique hostname:

    sudo hostnamectl set-hostname master-node

Run the following command in the slave node in order to give it a unique hostname:

    hostnamectl set-hostname slave-node


### Step3: Initialize Kubernetes on the master node

Run the following command as sudo on the master node:

    sudo kubeadm init --pod-network-cidr=10.244.0.0/16

The process might take a minute or more depending on your internet connection. The output of this command is very important:

![Initialize Kubernetes on the master node](https://vitux.com/wp-content/uploads/2018/10/word-image-215-1024x307.png)

Please note down the following information from the output:

To start using your cluster, you need to run the following as a regular user:

    mkdir -p $HOME/.kube

    sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config

    sudo chown $(id -u):$(id -g) $HOME/.kube/config

You can now join any number of machines by running the following on each node

as root:

    kubeadm join 192.168.100.6:6443 --token 06tl4c.oqn35jzecidg0r0m --discovery-token-ca-cert-hash sha256:c40f5fa0aba6ba311efcdb0e8cb637ae0eb8ce27b7a03d47be6d966142f2204c

Now run the commands suggested in the output in order to start using the cluster:

![Start Kubernetes Cluster](https://vitux.com/wp-content/uploads/2018/10/word-image-216.png)

You can check the status of the master node by running the following command:

`kubectl get nodes`

![Get list of nodes](https://vitux.com/wp-content/uploads/2018/10/word-image-217.png)

You will see that the status of the master node is “not ready” yet. It is because no pod has yet been deployed on the master node and thus the Container Networking Interface is empty.


### Step 4: Deploy a Pod Network through the master node

A pod network is a medium of communication between the nodes of a network. In this tutorial, we are deploying a Flannel pod network on our cluster through the following command:

`sudo kubectl apply -f` https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

### **![Deploy a Pod Network](https://vitux.com/wp-content/uploads/2018/10/word-image-218-1024x158.png)**

Use the following command in order to view the status of the network:

`kubectl get pods --all-namespaces`

![Check network status](https://vitux.com/wp-content/uploads/2018/10/word-image-219.png)

Now when you see the status of the nodes, you will see that the master-node is ready:

 `sudo kubectl get nodes`

![Get nodes](https://vitux.com/wp-content/uploads/2018/10/word-image-220.png)

### Step 5: Add the slave node to the network in order to form a cluster

On the slave node, run the following command you generated while initializing Kubernetes on the master-node:

    sudo kubeadm join 192.168.100.6:6443 --token 06tl4c.oqn35jzecidg0r0m --discovery-token-ca-cert-hash sha256:c40f5fa0aba6ba311efcdb0e8cb637ae0eb8ce27b7a03d47be6d966142f2204c

![Add the slave node to the network](https://vitux.com/wp-content/uploads/2018/10/word-image-221.png)

Now when you run the following command on the master node, it will confirm that two nodes, the master node, and the server nodes are running on your system.

 `sudo kubectl get nodes`

This shows that the two-node cluster is now up and running through the Kubernetes container management system.

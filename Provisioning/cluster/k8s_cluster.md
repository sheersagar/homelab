# Cluster_setup

# 🧩 Kubernetes Cluster Setup using Kubeadm + Containerd + Calico

This guide provides a **step-by-step** approach to setting up a Kubernetes cluster using **kubeadm**, **containerd (as CRI)**, and **Calico (as CNI)**.  
It assumes Ubuntu-based nodes and focuses on clean configuration and persistence.

#### And up until step 5 commands should be common for worker and master node. But from step 6, make sure you are implementing commands intended for their respective nodes.

---

## ⚙️ 1. Disable Swap

Kubernetes requires swap to be disabled for proper scheduling.

```bash
sudo swapoff -a

# Make this change permanent
sudo sed -i.bak '/ swap / s/^/#/' /etc/fstab

# Validate
swapon --show
```

## 🌐 2. Enable IPv4 Packet Forwarding
By default, Linux disables packet forwarding.
We’ll enable and persist this configuration:


### Enable packet forwarding
```bash
sudo sysctl -w net.ipv4.ip_forward=1

# Make it persistent across reboots
sudo sed -i.bak '/^net.ipv4.ip_forward/d' /etc/sysctl.conf && \
echo "net.ipv4.ip_forward = 1" | sudo tee -a /etc/sysctl.conf

# Reload configuration
sudo sysctl -p
```

## 📦 3. Install Container Runtime (Containerd)
#### I prefer Containerd as CRI for Kubernetes.

#### a. Add Docker Repository


```bash
sudo apt-get update
sudo apt-get install -y ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] \
  https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
```


#### b. Install Containerd

```bash
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Start and enable Docker (Containerd will run as part of Docker)
sudo systemctl start docker
sudo systemctl enable docker
sudo systemctl status docker
```


## ⚙️ 4. Configure Systemd Cgroup Driver
 🔥🔥🔥🔥🔥🔥🔥 
#### Most Important Step
🔥🔥🔥🔥🔥🔥🔥

This step is critical to get right. If configured incorrectly, it can lead to numerous issues that are difficult to troubleshoot later — often resulting in wasted time and effort without meaningful progress.

i. Configure Containerd to use the systemd cgroup driver by generating and editing the default config.toml file on all nodes.

```bash
sudo containerd config default | sed 's/SystemdCgroup = false/SystemdCgroup = true/' | sudo tee /etc/containerd/conf.toml
```

ii. Reload daemon and restart kubelet and containerd 

```bash
sudo systemctl daemon-reload 
sudo systemctl restart kubelet containerd
```



## ☸️ 5. Install Kubernetes Components
Install kubeadm, kubelet, and kubectl.

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gpg

# Add Kubernetes GPG key
sudo curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | \
sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

# Add Kubernetes repository
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] \
https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | \
sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
```


## 🚀 6. Initialize the Master Node
Run this command only on the control-plane (master) node:

```bash
sudo kubeadm init --pod-network-cidr=10.0.0.0/16
```

After initializing this, configure kubectl client. This will enable you to interact with API server using kubectl command. 

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```


## 🌐 7. Install Calico CNI Plugin
Apply the latest Calico manifest (for Kubernetes networking): This will also go only on master node.

```bash
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.27.0/manifests/calico.yaml
```

Verify the CNI pods:

```bash
kubectl get pods -n kube-system
```


## 👷‍♂️ 8. Join Worker Nodes
Generate a token on the master node:

```bash
kubeadm token create --print-join-command
```


Run the output command on each worker node to join the cluster.

✅ Validation
Once all nodes are joined and CNI is active:

```bash
kubectl get nodes -o wide
kubectl get pods -A -o wide
```

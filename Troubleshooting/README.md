# 🧠 Homelab — Kubernetes | CI/CD | Cloud-Native Troubleshooting

> *An evolving lab where I break, fix, and understand everything that makes cloud-native infrastructure work.*

---

## 🚀 Overview

This repository documents my **hands-on troubleshooting**, **deep dives**, and **experiments** in my **Homelab** environment.  
It covers everything from **Kubernetes cluster setup**, **networking**, and **CNI/CRI internals** to **application deployments**, **pipelines**, and **production-scenario debugging**.

Every issue, fix, and improvement is committed here — to serve as a personal knowledge base and a public reference for others who enjoy **solving real-world cluster chaos**.

---

## 🧩 Core Focus Areas

| Area | Description |
|------|--------------|
| 🏗️ **Kubernetes Infrastructure** | Cluster provisioning using `kubeadm`, `containerd`, and `Calico`. |
| 🔧 **Troubleshooting** | Deep system-level debugging (cgroups, CRI, DNS, BGP, pod networking). |
| 🌐 **Networking** | CNI internals, BGP (Calico), DNS resolution, iptables, routing. |
| 🧱 **Application Layer** | Deployments, Services, Ingress, and resource debugging. |
| ⚙️ **Pipelines** | CI/CD integration and GitOps-style workflows. |
| 🔍 **Observability** | Logs, metrics, events, and distributed tracing setup. |

---

## 🧠 Learning Philosophy

> “You don’t understand a system until you’ve broken it and fixed it.”

This repo is not a polished “tutorial” — it’s a **journey log**.  
It captures:
- **What broke**
- **Why it broke**
- **How I traced it**
- **And how it was fixed**

Each commit reflects a part of that learning process — not just commands, but reasoning.

---

## ⚡ Recent Work Highlights

### 🏗️ Cluster Setup & Networking
- Built a multi-node **Kubernetes cluster** from scratch with `kubeadm`.
- Integrated **Calico CNI** and debugged crash-looping `calico-node` DaemonSets.
- Investigated **kube-proxy failures** on worker nodes due to CRI socket issues.
- Fixed **etcd termination** and **cgroup driver mismatch** issues between `containerd` and `kubelet`.

### 🧩 System-Level Debugging
- Analyzed why a pod’s **sandbox re-creation** loop occurs in `containerd`.
- Deep-dived into **systemd-resolved** and how it handles upstream DNS.
- Explored **Calico’s BIRD/BGP** fundamentals and inter-node routing behavior.
- Fixed a misconfigured `/sys/fs/cgroup` leading to kubelet startup failures.

### 🧰 Pipelines & Automation (Coming Soon)
- Setting up **GitHub Actions** for automated validation of manifests.
- Exploring **ArgoCD / Flux** for GitOps-style deployments.

---

## 📘 Example: Kubernetes Setup Workflow

A structured guide to rebuild a cluster from scratch:  
👉 [Cluster Setup with Containerd + Calico](https://github.com/sheersagar/homelab/blob/main/Provisioning/cluster/k8s_cluster.md)

---

## 🧾 Repository Structure

```bash
.
├── Provisioning
│ ├── cluster
│ │ └── k8s_cluster.md # Complete cluster setup guide (kubeadm + containerd + Calico)
│ └── ss # Screenshots or supplementary setup files
├── Troubleshooting
│ ├── Cluster
│ │ └── 01_CrashLoopBackOff.md # Deep dive: CrashLoopBackOff analysis and resolution
│ ├── Images # Visuals used in troubleshooting docs
│ │ ├── crashloop.png
│ │ ├── describe-crashloop.png
│ │ └── normal_cluster.png
│ └── README.md # Index for all troubleshooting topics
└── README.md # (You are here)
```




---

## ⚙️ What’s Inside

### 🏗️ Provisioning
All cluster setup and infrastructure bring-up processes live here.  
Example:  
📄 [`k8s_cluster.md`](https://github.com/sheersagar/homelab/blob/main/Provisioning/cluster/k8s_cluster.md) — *Step-by-step setup of Kubernetes using kubeadm, containerd, and Calico CNI.*

### 🧰 Troubleshooting
A continuously growing knowledge base of issues, fixes, and insights from real Homelab debugging sessions.  
Example:  
📄 [`01_CrashLoopBackOff.md`](https://github.com/sheersagar/homelab/blob/main/Troubleshooting/Cluster/01_CrashLoopBackOff.md) — *Analysis of Calico and kube-proxy crashloops with detailed cause mapping.*

---

## 🧠 Core Themes

| Area | Focus |
|------|-------|
| ☸️ **Kubernetes** | Cluster lifecycle, kubeadm initialization, node joins |
| 🧱 **Container Runtime** | Containerd, cgroup driver configuration, CRI analysis |
| 🌐 **Networking** | CNI (Calico), BGP, iptables, inter-node communication |
| 🔧 **Troubleshooting** | Logs, events, kubelet internals, sandbox lifecycle |
| 🧰 **System Internals** | DNS (systemd-resolved), kernel networking, etcd health |
| ⚙️ **Automation** | Reproducible setup & teardown scripts (coming soon) |

---

## 📸 Visuals

Below: A healthy cluster vs. CrashLoopBackOff scenario (documented in [Troubleshooting/Cluster/01_CrashLoopBackOff.md](https://github.com/sheersagar/homelab/blob/main/Troubleshooting/Cluster/01_CrashLoopBackOff.md))

| Normal Cluster | CrashLoopBackOff |
|----------------|------------------|
| ![Normal Cluster](./Troubleshooting/Images/normal_cluster.png) | ![CrashLoopBackOff](./Troubleshooting/Images/crashloop.png) |

---

## 🧾 Example Highlight — Cluster Setup

> A complete, production-grade workflow to bootstrap Kubernetes with containerd and Calico CNI.

📄 [`k8s_cluster.md`](./Provisioning/cluster/k8s_cluster.md)

```bash
# Initialize master node
sudo kubeadm init --pod-network-cidr=10.0.0.0/16

# Configure kubectl
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

# Apply Calico networking
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.27.0/manifests/calico.yaml
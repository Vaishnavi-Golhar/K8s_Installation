# Kubernetes Installation Guide on Ubuntu EC2

## Step 1: Update System Packages
```bash
sudo apt update && sudo apt upgrade -y
```

## Step 2: Install Required Dependencies
```bash
sudo apt install -y apt-transport-https ca-certificates curl
```

## Step 3: Install Kubernetes Components

### 1. Install `kubectl`
```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
kubectl version --client
```

### 2. Install `kubeadm`, `kubelet`, and `kubectl`
```bash
sudo curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /usr/share/keyrings/kubernetes-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt update
sudo apt install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

### 3. Disable Swap (Required for Kubernetes)
```bash
sudo swapoff -a
sudo sed -i '/ swap / s/^/#/' /etc/fstab
```

## Step 4: Install Container Runtime (Docker)
```bash
sudo apt install -y docker.io
sudo systemctl enable --now docker
sudo usermod -aG docker $USER
```

## Step 5: Initialize Kubernetes Cluster (Master Node)
```bash
sudo kubeadm init --pod-network-cidr=192.168.0.0/16
```

## Step 6: Configure `kubectl` for Regular User
```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

## Step 7: Install Networking (Flannel)
```bash
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

## Step 8: Verify Cluster is Running
```bash
kubectl get nodes
kubectl get pods -A
```

## Step 9: Install Minikube
```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

## Step 10: Start Minikube
```bash
minikube start --driver=docker
```

## Step 11: Verify Installation
```bash
minikube status
kubectl get nodes
```


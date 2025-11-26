# AWX Installation Guide on Ubuntu (k3s + AWX Operator)

## 1) Prerequisites

* Ubuntu Server **22.04 / 24.04** (root or sudo required)
* Minimum (single node test):

  * **2 vCPU**
  * **4–8 GB RAM**
  * **20+ GB disk**
* Internet access (images pull karne ke liye)

---

## 2) Install Ansible

```bash
sudo apt update
sudo apt install -y software-properties-common
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install -y ansible
ansible --version
```

---

## 3) Install Lightweight Kubernetes (k3s)

```bash
sudo apt update
sudo apt install -y curl
curl -sfL https://get.k3s.io | sh -
```

Verify:

```bash
sudo systemctl status k3s
kubectl get nodes
```

(Optional) Set kubeconfig for your user:

```bash
mkdir -p ~/.kube
sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/config
sudo chown $(id -u):$(id -g) ~/.kube/config
```

---

## 4) Install Helm

```bash
curl -fsSL https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
helm version
```

---

## 5) Install AWX Operator

### A. Namespace create:

```bash
kubectl create namespace awx
```

### B. Install via Helm:

```bash
helm repo add awx-operator https://ansible.github.io/awx-operator/
helm repo update
helm install awx-operator awx-operator/awx-operator -n awx
```

### C. Verify:

```bash
kubectl get pods -n aw
```

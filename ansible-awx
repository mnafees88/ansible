✅ AWX Installation Guide on Ubuntu (k3s + AWX Operator)

GitHub-safe Markdown version — formatting won’t break

1) Prerequisites

Ubuntu Server 22.04 / 24.04 (root or sudo required)

Minimum (single node test):

2 vCPU

4–8 GB RAM

20+ GB disk

Internet access (images pull karne ke liye)

2) Install Ansible
sudo apt update
sudo apt install -y software-properties-common
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install -y ansible
ansible --version

3) Install Lightweight Kubernetes (k3s)
sudo apt update
sudo apt install -y curl
curl -sfL https://get.k3s.io | sh -


Verify:

sudo systemctl status k3s
kubectl get nodes


(Optional) Set kubeconfig for your user:

mkdir -p ~/.kube
sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/config
sudo chown $(id -u):$(id -g) ~/.kube/config

4) Install Helm
curl -fsSL https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
helm version

5) Install AWX Operator
A. Namespace create:
kubectl create namespace awx

B. Install via Helm:
helm repo add awx-operator https://ansible.github.io/awx-operator/
helm repo update
helm install awx-operator awx-operator/awx-operator -n awx

C. Verify:
kubectl get pods -n awx

6) Deploy AWX Instance (CustomResource)

Create file: awx-deploy.yaml

apiVersion: awx.ansible.com/v1beta1
kind: AWX
metadata:
  name: awx-demo
  namespace: awx
spec:
  service_type: nodeport
  ingress_type: none
  hostname: awx.local
  postgres_configuration_secret: ""
  admin_user: admin


Apply:

kubectl apply -f awx-deploy.yaml -n awx

7) Check Deployment Status
kubectl get all -n awx
kubectl get pods -n awx
kubectl describe awx awx-demo -n awx

8) Retrieve Admin Password
kubectl get secret awx-demo-admin-password -n awx -o jsonpath="{.data.password}" | base64 --decode ; echo


Default username → admin

9) Access AWX Web UI

Check NodePort:

kubectl get svc -n awx


Example:

awx-demo-service   NodePort   31280


Open in browser:

http://<server-ip>:<nodeport>


Login with:

Username: admin

Password: (retrieved from secret)

✔ Ready to paste into GitHub README — no formatting will break.

Agar chaho to main aap ke liye README.md file bhi generate kar doon.

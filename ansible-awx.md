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


## 5) Install AWX Operator

# Clone AWX Operator reop

````bash
git clone https://github.com/ansible/awx-operator.git
cd awx-operator
git checkout 2.19.1
````
# Install AWX Operator Manifests

````bash
export NAMESPACE=awx
kubectl create namespace $NAMESPACE
sudo apt install -y make
make deploy
````

### C. Verify:

```bash
kubectl get pods -n awx
```

## 6) Deploy AWX Instance (CustomResource)

Create file: **awx-deploy.yaml**

```yaml
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
```

Apply:

```bash
kubectl apply -f awx-deploy.yaml -n awx
```

---

## 7) Check Deployment Status

```bash
kubectl get all -n awx
kubectl get pods -n awx
kubectl describe awx awx-demo -n awx
```

---

## 8) Retrieve Admin Password

```bash
kubectl get secret awx-demo-admin-password -n awx -o jsonpath="{.data.password}" | base64 --decode ; echo
```

Default username → **admin**

---

## 9) Access AWX Web UI

Check NodePort:

```bash
kubectl get svc -n awx
```

Example:

```
awx-demo-service   NodePort   31280
```

Open in browser:

```
http://<server-ip>:<nodeport>
```

Login with:

* **Username:** admin
* **Password:** (retrieved from secret)

---
**once done you can access via browser**
<img width="903" height="205" alt="image" src="https://github.com/user-attachments/assets/ed25ce22-fb1e-40e2-92fa-1e466fb0b196" />

<img width="1005" height="784" alt="image" src="https://github.com/user-attachments/assets/921d8dc3-a28f-492b-8563-88569d2c945b" />

**create inventory**
`1. click resources>>inventories>>add inventories>>enter your inventory name`

**Add host**

`1. click on resources >> hosts>> simply name section add IP`

`2.select inventory for which inventory will use host`

<img width="1181" height="416" alt="image" src="https://github.com/user-attachments/assets/38be91cb-5c97-497f-9c60-578ba1ebffe6" />


**how to add multiplehost with inventory.ini**

`1. After adding inventories select the inventory and then select Sources tab then click on add`

<img width="806" height="468" alt="image" src="https://github.com/user-attachments/assets/bae2ce2f-a75b-46bd-ad0a-2911f9a3d8a4" />

`2. select your projects and from source select from a project make your inventory.ini should be create on you repo`

<img width="1555" height="583" alt="image" src="https://github.com/user-attachments/assets/f01f0680-8f47-4b9e-891e-dd55f681253c" />

`3. Now you can see these host in your inventory>> Hosts tab`

<img width="1111" height="466" alt="image" src="https://github.com/user-attachments/assets/e5c15461-6f30-4ffd-ac7a-57e3d1cfa6e8" />


`4. Inventory file multiple hosts adding`

<img width="387" height="265" alt="image" src="https://github.com/user-attachments/assets/82c1f7fd-564b-47ae-be03-756307c577d8" />


**then add server credentials**

`1. click on resources >> credentials>>name your server`

`2. credentials type >>Machine>> adduser name >>add SSH Private key`

<img width="885" height="712" alt="image" src="https://github.com/user-attachments/assets/a462303b-e33e-4252-a16c-049c04bae943" />


**Create Separate Credentials for Each Host**

`1. Create a folder in same repo with keys and add all .pems in that folder`

`2. Now your inventory file should b like this`

````bash
[web]
server1 ansible_host=10.0.0.1 ansible_user=ubuntu ansible_ssh_private_key_file=/var/lib/awx/projects/project/keys/master.pem
server2 ansible_host=10.0.0.2 ansible_user=ubuntu ansible_ssh_private_key_file=/var/lib/awx/projects/project/keys/node1.pem
````

<img width="1281" height="640" alt="image" src="https://github.com/user-attachments/assets/460e8180-e95b-48a6-889a-a0a6de68325f" />


**Add project to add playbook via git or manual**

`1. click resources>>projects>>add name>>source control if you have git select git>>repourl>>branchname>>`

<img width="1572" height="535" alt="image" src="https://github.com/user-attachments/assets/f9815014-a587-45a9-84b3-5226e9f4d995" />


**Create New Job Template**

`1. resources>>templates>>add new job templates>>name your template>>select your project>>once you select your project it will auto update you playbook from git`

<img width="1480" height="606" alt="image" src="https://github.com/user-attachments/assets/30db6744-dc31-436f-8b97-638c3b117772" />

`2. now click on template and lunch template`

-----------------------------------------------------------------------------------------
### Method 2 B. Install via Helm:


**Install Helm**

```bash
curl -fsSL https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
helm version
```

---


```bash
helm repo add awx-operator https://ansible.github.io/awx-operator/
helm repo update
helm install awx-operator awx-operator/awx-operator -n awx
```

### C. Verify:

```bash
kubectl get pods -n awx
```

---

## 6) Deploy AWX Instance (CustomResource)

Create file: **awx-deploy.yaml**

```yaml
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
```

Apply:

```bash
kubectl apply -f awx-deploy.yaml -n awx
```

---

## 7) Check Deployment Status

```bash
kubectl get all -n awx
kubectl get pods -n awx
kubectl describe awx awx-demo -n awx
```

---

## 8) Retrieve Admin Password

```bash
kubectl get secret awx-demo-admin-password -n awx -o jsonpath="{.data.password}" | base64 --decode ; echo
```

Default username → **admin**

---

## 9) Access AWX Web UI

Check NodePort:

```bash
kubectl get svc -n awx
```

Example:

```
awx-demo-service   NodePort   31280
```

Open in browser:

```
http://<server-ip>:<nodeport>
```

Login with:

* **Username:** admin
* **Password:** (retrieved from secret)

---
-------------------------

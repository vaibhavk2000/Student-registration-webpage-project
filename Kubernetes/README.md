# 🖥️ Setup EKS Cluster & MySQL Deployment

## 1. Launch EC2 Instance & Install Dependencies
Run the user data script below or execute these commands inside your EC2 instance to install AWS CLI, kubectl, and eksctl:

```bash
#!/bin/bash
sudo apt update -y
sudo apt install -y curl unzip

# Install AWS CLI
curl "[https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip](https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip)" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install

# Install kubectl
curl -LO "[https://dl.k8s.io/release/$(curl](https://dl.k8s.io/release/$(curl) -L -s [https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl](https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl)"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

# Install eksctl
curl --silent --location "[https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname](https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname) -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
sudo chmod +x /usr/local/bin/eksctl

```

Verify installations:

```bash
aws --version
kubectl version --client
eksctl version

```

---

## 2. Configure AWS CLI & Create Cluster

Configure your AWS credentials:

```bash
aws configure

```

Create the Amazon EKS cluster:

```bash
eksctl create cluster --name eks-student --region ap-south-1 --version 1.36 --nodegroup-name studentwebpage-nodes --node-type m7i-flex.large --nodes 1

```

Log into the EKS cluster:

```bash
aws eks update-kubeconfig --name eks-student

```

---

## 3. Configure Cluster Security Group

Once the cluster creation completes:

1. Open **AWS Console → EKS**.
2. Select **eks-student** → **Networking** tab.
3. Click the **Cluster security group** ID.
4. Click **Edit inbound rules** and add:
* **Type:** All traffic
* **Protocol:** All
* **Port:** All
* **Source:** `0.0.0.0/0`



---

## 4. Setup MySQL Database (RDS)

1. Create a MySQL database instance in AWS RDS.
2. Ensure the RDS Security Group allows **MySQL/Aurora (Port 3306)** inbound traffic from `0.0.0.0/0` or the EKS Cluster Security Group.
3. Install MariaDB/MySQL client on your EC2 instance:
```bash
sudo apt update -y && sudo apt install -y mariadb-client

```


4. Connect to your RDS MySQL instance:
```bash
mysql -h <rds-endpoint> -u admin -pVaibhav123

```


5. Initialize database and privileges:
```sql
CREATE DATABASE IF NOT EXISTS student_db;
USE student_db;
GRANT ALL PRIVILEGES ON student_db.* TO 'admin'@'%' IDENTIFIED BY 'Vaibhav123';
FLUSH PRIVILEGES;

```



---

## 5. Deploy Application to EKS

1. Apply Backend Deployment & Service:
```bash
kubectl apply -f Kubernetes/backend.yaml

```


2. Apply Frontend Deployment & Service:
```bash
kubectl apply -f Kubernetes/frontend.yaml

```


3. Get LoadBalancer public URL:
```bash
kubectl get svc

```



---

## 6. Cleanup Resources

To avoid ongoing AWS charges, delete the cluster when finished:

```bash
eksctl delete cluster --name eks-student --region ap-south-1

```

### Apply manifest file and copy frontend service link and check

<img width="1917" height="1020" alt="image" src="https://github.com/user-attachments/assets/7887b394-6608-4894-8a67-1b829d985d6e" />
<img width="1116" height="895" alt="image" src="https://github.com/user-attachments/assets/40b9ad2d-bd0d-40f5-bd8b-6be127999aac" />



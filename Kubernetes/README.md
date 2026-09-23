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



git clone https://github.com/Sourabhf4/student-app-k8s.git
    2  eksctl create cluster --name eks-student --region ap-south-1 --version 1.36 --nodegroup-name webapp-nodes --node-type m7i-flex.large --nodes 1
    3  aws eks update-kubeconfig --name eks-student
    4  ls
    5  cd student-app-k8s/
    6  ls
    7  cd  backend/
    8  ls
    9  cd src
   10  ls
   11  cd main
   12  ls
   13  cd resources/
   14  ls
   15  nano application.properties 
   16  ls
   17  cd 
   18  cd student-app-k8s/
   19  ls
   20  cd backend/
   21  ls
   22  docker login -u vaibhavkunghatkar
   23  docker pull vaibhavkunghatkar/student-backend
   24  docker images
   25  ls
   26  nano backend.yaml 
   27  kubectl get pods
   28  kubectl apply -f backend.yaml 
   29  kubectl get pods
   30  nano Dockerfile 
   31  docker build -t vaibhavkunghatkar/student-backend:latest
   32  docker build -t vaibhavkunghatkar/student-backend:latest .
   33  cat backend.yaml 
   34  kubectl apply -f backend.yaml 
   35  docker push vaibhavkunghatkar/student-backend
   36  kubectl apply -f backend.yaml 
   37  kubectl get pods
   38  ls
   39  cd src/main
   40  ls
   41  cd resources/
   42  ls
   43  nano application.properties 
   44  ls
   45  cd ..
   46  ls
   47  cd backend/
   48  ls
   49  kubectl get pods
   50  nano backend.yaml 
   51  kubectl apply -f backend.yaml 
   52  kubectl get pods
   53  kubectl delete pod backend-deploy-5fddc9f884-wz4zh --force --grace-period=0
   54  kubectl get pods
   55  kubectl get svc
   56  cd ..
   57  ls
   58  cd frontend/
   59  ls
   60  ls -a
   61  nano .env
   62  ls
   63  docker build -t vaibhavkunghatkar/student-frontend:latest .
   64* 
   65  nano frontend.yaml 
   66  docker images
   67  docker push vaibhavkunghatkar/student-frontend:latest 
   68  kubectl apply -f frontend.yaml 
   69  kubectl get svc
   70  kubectl get pods
   71  history


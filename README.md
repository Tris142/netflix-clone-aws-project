# 🎬 Netflix Clone on AWS (StreamBox)

A Netflix-style web application deployed on AWS using **CloudFormation**, **EC2**, **Application Load Balancer**, and **Amazon RDS**, with a **Node.js backend** serving a static frontend and fetching data from a MySQL database.

---

## 📌 Architecture Overview

```
User Browser
   ↓
Application Load Balancer (Public Subnet)
   ↓
EC2 Instance (Private Subnet)
   ↓
Node.js + Express Backend
   ↓
Amazon RDS (MySQL)
```

---

## 🛠️ Technologies Used

* AWS CloudFormation (Infrastructure as Code)
* Amazon VPC (Public & Private Subnets)
* EC2 (Amazon Linux)
* Application Load Balancer
* Amazon RDS (MySQL)
* Node.js & Express
* HTML, CSS, JavaScript
* Git & GitHub

---

## 📂 Project Structure

```
netflix-aws-clone/
│
├── frontend/
│   ├── index.html
│   ├── style.css
│   └── app.js
│
├── backend/
│   └── server.js
│
├── cfn.yaml
└── README.md
```

---

## 🚀 Deployment Steps (Step-by-Step)

### Step 1️⃣ – Create EC2 Key Pair

1. AWS Console → EC2 → Key Pairs
2. Create a key pair named:

   ```
   MytestKey
   ```
3. Download and save the `.pem` file

---

### Step 2️⃣ – Deploy Infrastructure using CloudFormation

1. AWS Console → CloudFormation → Create Stack
2. Upload the `cfn.yaml` file
3. Provide parameters:

   * **KeyName**: MytestKey
   * **DBUser**: admin
   * **DBPassword**: (strong password)
4. Acknowledge IAM capabilities
5. Create the stack and wait for `CREATE_COMPLETE`

This will create:

* VPC with public & private subnets
* Application Load Balancer
* EC2 instance (private subnet)
* NAT Gateway
* Amazon RDS (MySQL)

---

### Step 3️⃣ – Access EC2 Instance (Session Manager)

Since the EC2 instance is in a private subnet, access is done using **AWS Session Manager**:

1. EC2 → Instances
2. Select instance → Connect
3. Choose **Session Manager** → Connect

---

### Step 4️⃣ – Install Required Software

Run the following commands inside the EC2 instance:

```bash
sudo dnf update -y
sudo dnf install -y nodejs git
```

---

### Step 5️⃣ – Clone the Project Repository

```bash
cd /home/ec2-user
git clone https://github.com/tris142/netflix-clone-aws-project.git
cd netflix-clone-aws-project/backend
```

---

### Step 6️⃣ – Configure Backend Application

Edit the backend server file:

```bash
nano server.js
```

Update database details:

```js
const pool = mariadb.createPool({
  host: "<RDS-ENDPOINT>",
  user: "admin",
  password: "<DB-PASSWORD>",
  database: "streambox"
});
```

Save and exit (`CTRL + O`, `CTRL + X`).

---

### Step 7️⃣ – Install Node.js Dependencies

```bash
npm init -y
npm install express mariadb
```

---

### Step 8️⃣ – Start the Backend Server

```bash
sudo node server.js
```

The application now runs on **port 80**.

---

### Step 9️⃣ – Connect to Amazon RDS

Install MySQL client:

```bash
sudo dnf install -y mariadb105
```

Connect to RDS:

```bash
mysql -h <RDS-ENDPOINT> -u admin -p
```

---

### Step 🔟 – Create Database and Table

```sql
CREATE DATABASE streambox;
USE streambox;

CREATE TABLE movies (
  id INT AUTO_INCREMENT PRIMARY KEY,
  title VARCHAR(100),
  description TEXT
);

INSERT INTO movies (title, description) VALUES
('Stranger Things', 'Sci-fi thriller'),
('Money Heist', 'Crime drama'),
('Dark', 'Time travel mystery');
```

Exit MySQL:

```sql
exit;
```

---

### Step 1️⃣1️⃣ – Access the Application

1. AWS Console → EC2 → Load Balancers
2. Copy the **ALB DNS Name**
3. Open in browser:

```
http://<ALB-DNS-NAME>
```

🎉 Netflix Clone UI will load with movie data from RDS.

---

## 🔐 Security Best Practices Used

* EC2 and RDS deployed in private subnets
* ALB exposed to public traffic
* Security Groups allow only required traffic
* No SSH access from the internet

---

## 📈 Future Enhancements

* Use PM2 for process management
* Store secrets in AWS Parameter Store
* Enable HTTPS using ACM
* Add Auto Scaling policies
* Integrate CI/CD pipeline

---

## ✅ Conclusion

This project demonstrates a real-world AWS deployment using Infrastructure as Code, secure networking, and a full-stack application architecture following industry best practices.

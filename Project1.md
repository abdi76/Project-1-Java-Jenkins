Here is a phase-by-phase guide to your project, with text placeholders for screenshots to illustrate each step.

# 🔰 Phase 0: Environment Preparation & EC2 Provisioning Prerequisites

> 🎯 **Objective:**
> Prepare the foundational elements needed to provision your **single-node DevOps Project environment** on AWS. This includes IAM access, key pair creation, network configurations, and security group rules.

---

## 🧩 Why Start With Phase 0?

Before deploying and configuring any tools, it’s essential to:

* Set up **SSH credentials**
* Define **network security boundaries**
* Ensure **IAM access** is available
* Prepare for **EC2 provisioning** with correct configurations

Phase 0 is your **environment readiness checkpoint** to avoid roadblocks later.

---

## ✅ Step 1: Validate AWS Access

1. Log in to the AWS Console → [https://console.aws.amazon.com](https://console.aws.amazon.com)

2. Ensure your account is:
   
   * **Free Tier eligible** or **billing-enabled**
   * Has **EC2 launch, key pair, and SG permissions** under IAM

> 🔐 Optional: Use an IAM User with a custom policy allowing `ec2:*`, `iam:GetUser`, and `ec2:Describe*`

```
AWS Management Console dashboard after logging in
```

a6c3114b-cb98-44e0-a4f1-86999db5e0d2.png)

---

## ✅ Step 2: Create an SSH Key Pair (Manual)

Key pair enables secure SSH access into the EC2 server.

1. Navigate to: **EC2 → Key Pairs → Create Key Pair**

2. Choose:
   
   * **Name**: abdi-keypair
   * **Key type**: `RSA`
   * **Format**: `.pem`

3. Download the `.pem` file and store it securely (e.g., `~/.ssh/`)

```
Create Key Pair' page in the AWS EC2 console with the specified options filled in.
```

```bash
# Secure the key locally
mv ~/Downloads/devops-abdi.pem ~/.ssh/
chmod 400 ~/.ssh/abdi-keypair.pem
```

![5fdaa72a-2ae5-43ce-ae81-b88ba0e4a3b5](file:///C:/Users/abdi7/Pictures/Typedown/5fdaa72a-2ae5-43ce-ae81-b88ba0e4a3b5.png)

---

## ✅ Step 3: Create a DevOps-Friendly Security Group

Open required ports for upcoming tools.

Go to:
**EC2 → Security Groups → Create Security Group**

Add these **inbound rules**:

| Protocol | Port(s)     | Purpose                         |
| -------- | ----------- | ------------------------------- |
| TCP      | 22          | SSH                             |
| TCP      | 80, 443     | Web apps (HTTP/HTTPS)           |
| TCP      | 8080        | Jenkins                         |
| TCP      | 9999        | Tomcat                          |
| TCP      | 3000        | Grafana                         |
| TCP      | 4243        | Docker Remote API               |
| TCP      | 9090        | Prometheus                      |
| TCP      | 9000        | SonarQube                       |
| TCP      | 30000–32767 | Kubernetes NodePorts (optional) |

```
Inbound rules' section on the 'Create Security Group' page, showing all the specified ports and protocols.
```

![22694b07-a33d-44e7-8e0b-0975b29d795b](file:///C:/Users/abdi7/Pictures/Typedown/22694b07-a33d-44e7-8e0b-0975b29d795b.png)

Save the Security Group and **note the Group ID**.

---

## ✅ Step 4: Summary – Ready for Launch

| Component           | Status     |
| ------------------- | ---------- |
| AWS Account         | ✅ Verified |
| Key Pair            | ✅ Created  |
| Security Group      | ✅ Defined  |
| IAM Access/Policies | ✅ Enabled  |

🟢 Now you’re fully prepped to launch your EC2 instance in **Phase 1**.

---

# 🔧 Phase 1: Provision Your EC2 DevOps Project Server

> 🎯 **Objective:**
> Provision a **single, all-in-one EC2 instance** (Ubuntu 24.04 LTS) that will host the full DevOps stack. This instance will remain central to all future configurations and executions.

---

## 🧩 Why This Phase Matters

In contrast to traditional multi-node topologies, we’ll install and run everything — from Jenkins to Docker to Prometheus — on **one powerful EC2 node**. This is ideal for:

* Cost-effective training
* Local simulation
* One-box DevOps pipelines

---

## ✅ Step 1: Launch EC2 Instance (Ubuntu 24.04 LTS)

1. Navigate to AWS Console → **EC2 → Launch Instance**
2. Configure instance:

| Setting            | Value                             |
| ------------------ | --------------------------------- |
| **Name**           | devops-essentials-single-node     |
| **AMI**            | Ubuntu Server 24.04 LTS (HVM)     |
| **Instance Type**  | `t2.micro` (1 vCPU, 1 GB RAM)     |
| **Storage**        | 30 GB (General Purpose SSD - gp3) |
| **Key Pair**       | devops-abdi                       |
| **Security Group** | Use the group created in Phase 0  |
| **Subnet**         | Default VPC/Subnet is sufficient  |

3. Click **Launch Instance**
4. Wait for **Status: Running**

```
Summary' panel on the EC2 'Launch an instance' page, showing the selected configuration before launch.
```

![bb2b41fe-2ad8-42ea-8d3e-b2d18bab9271](file:///C:/Users/abdi7/Pictures/Typedown/bb2b41fe-2ad8-42ea-8d3e-b2d18bab9271.png)

![d045e8d3-5b07-4d29-a243-526aefd846c6](file:///C:/Users/abdi7/Pictures/Typedown/d045e8d3-5b07-4d29-a243-526aefd846c6.png)

![0dd801f1-ab2e-437e-8aa2-bdccba963626](file:///C:/Users/abdi7/Pictures/Typedown/0dd801f1-ab2e-437e-8aa2-bdccba963626.png)

---



## ✅ Step 2: Connect to Your Server via SSH

From your terminal (Git Bash, VS Code, MobaXterm, etc.):

```bash
ssh -i ~/.ssh/devops-abdi.pem ubuntu@<EC2-Public-IP
```

🧪 Example:

```bash
ssh -i ~/.ssh/devops-abdi.pem ubuntu@13.58.24.101
```

```
terminal window showing a successful SSH connection to the EC2 instance
```

![d6465f70-51c3-4fbf-be14-cc4de309c8b1](file:///C:/Users/abdi7/Pictures/Typedown/d6465f70-51c3-4fbf-be14-cc4de309c8b1.png)

---

## ✅ Step 3: Verify Connection & Set Host Identity (Optional)

### Confirm you’re inside the EC2:

```bash
whoami
```

Expected output:

```
ubuntu
```

### Set custom hostname (optional but helpful):

```bash
sudo hostnamectl set-hostname devops-node
exec bash
```

```
the terminal after running the hostname commands, showing the new hostname 'devops-node' in the command prompt.
```

![6699b948-6f09-4ac8-aa77-9aee13d78f3e](file:///C:/Users/abdi7/Pictures/Typedown/6699b948-6f09-4ac8-aa77-9aee13d78f3e.png)

---

## 🧪 Phase 1 Completion Checklist

| Task                                         | Status |
| -------------------------------------------- | ------ |
| EC2 instance launched successfully           | ✅      |
| SSH access established                       | ✅      |
| Hostname set for clarity                     | ✅      |
| Instance ready for DevOps tool installations | ✅      |

---

# 🔧 Phase 2: Install DevOps Tools on the Single Node

> 📍 Goal: Prepare your **single Ubuntu 24.04 EC2 instance** by installing all essential DevOps tools required for the project.

This phase ensures that **Terraform, Git, Docker, Jenkins, Ansible, AWS CLI**, and **Java (for Jenkins/Tomcat)** are installed and ready to use on the same server.

---

## 🗂️ Phase Overview

| Tool                  | Purpose                                                               |
| --------------------- | --------------------------------------------------------------------- |
| **Git**               | Source control for managing code                                      |
| **Terraform**         | Infrastructure provisioning automation                                |
| **Docker**            | Containerization for building & running apps in isolated environments |
| **Jenkins**           | CI/CD pipeline automation                                             |
| **Ansible**           | Configuration management for deployment automation                    |
| **AWS CLI**           | To interact with AWS resources from terminal                          |
| **Java (OpenJDK 17)** | Required by Jenkins and Tomcat                                        |

---

## 🖥️ Ensure System Is Updated First

```bash
sudo apt update && sudo apt upgrade -y
```

```
the terminal after the system update and upgrade commands have completed successfully.
```

![63404be3-e35a-49bd-a33a-5cf0cb098182](file:///C:/Users/abdi7/Pictures/Typedown/63404be3-e35a-49bd-a33a-5cf0cb098182.png)

---

## ✅ Step 1: Install Git

```bash
sudo apt install git -y
git --version```

```

the terminal displaying the output of 'git --version.

![42aac0c4-627b-4d79-b113-ca17723c8618](file:///C:/Users/abdi7/Pictures/Typedown/42aac0c4-627b-4d79-b113-ca17723c8618.png)

```

---

## ✅ Step 2: Install Terraform

### 📦 Install prerequisites

```bash
sudo apt install wget unzip -y
```

### 📥 Download & Install Terraform

```bash
# Add HashiCorp GPG key
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/hashicorp.gpg

# Add Terraform repo
echo "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
sudo tee /etc/apt/sources.list.d/hashicorp.list

# Install Terraform
sudo apt update
sudo apt install -y terraform
terraform -version
```

```
the terminal displaying the output of 'terraform -version.
```

![71a304e9-ffa9-4a37-b449-d1c0e3372abb](file:///C:/Users/abdi7/Pictures/Typedown/71a304e9-ffa9-4a37-b449-d1c0e3372abb.png)

---

## ✅ Step 3: Install AWS CLI

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
aws --version
```

### 🔐 Configure with IAM credentials

```bash
aws configure
```

📝 Provide:

* Access Key
* Secret Key
* Region (e.g. `eu-west-2`)
* Output format (`json`)

```
the terminal showing the prompts for 'aws configure' and the version output from 'aws --version.
```

![0d7baef9-6e1a-4b3a-96d1-1a06c6d168ea](file:///C:/Users/abdi7/Pictures/Typedown/0d7baef9-6e1a-4b3a-96d1-1a06c6d168ea.png)

---

## ✅ Step 4: Install Ansible

```bash
sudo apt install ansible -y
ansible --version
```

```
the terminal displaying the output of 'ansible --version.
```

![283c801c-9870-4d0a-95a9-88185af2b5f7](file:///C:/Users/abdi7/Pictures/Typedown/283c801c-9870-4d0a-95a9-88185af2b5f7.png)

---

## ✅ Step 5: Install Docker

### 📥 Install Docker Engine

```bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo usermod -aG docker $USER
newgrp docker
```

### 🟢 Start and Enable Docker

```bash
sudo systemctl start docker
sudo systemctl enable docker
```

### 🔍 Verify Docker

```bash
docker --version
```

**(Optional) we can run a Container and verify**

the terminal showing the 'Hello from Docker!' message after running the hello-world container.

![714d6daa-a707-41e0-8cc1-14c05d777c9a](file:///C:/Users/abdi7/Pictures/Typedown/714d6daa-a707-41e0-8cc1-14c05d777c9a.png)

```bash
sudo docker run hello-world
```

```


## ✅ Step 6: Configure Docker Remote Access (Port 4243)

To enable Docker API access:

```bash
sudo sed -i 's|ExecStart=.*|ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:4243 -H unix:///var/run/docker.sock|' /lib/systemd/system/docker.service
```

```bash
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl restart docker
```

```
the terminal after restarting the Docker service without any errors.
```

![cdee8894-0094-4745-8ce1-4e56bec08c77](file:///C:/Users/abdi7/Pictures/Typedown/cdee8894-0094-4745-8ce1-4e56bec08c77.png)

---

## ✅ Step 7: Install Java (OpenJDK 17)

```bash
sudo apt install openjdk-17-jdk -y
java -version
```

```
the terminal displaying the output of 'java -version.
```

![381a6a58-e5c6-4ead-8fab-698b735eca2c](file:///C:/Users/abdi7/Pictures/Typedown/381a6a58-e5c6-4ead-8fab-698b735eca2c.png)

---

## ✅ Step 8: Install Jenkins

### 🚀 Install Jenkins

```bash
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
```

### ▶️ Start & Enable Jenkins

```bash
sudo systemctl start jenkins
sudo systemctl enable jenkins
```

### 🌐 Access Jenkins

Open:

```
http://<EC2-Public-IP>:8080/
```

Get Admin password:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

```
the browser showing the 'Unlock Jenkins' page, asking for the administrator password.
```

![9e9a27af-5d47-4e3d-96a0-0e4e4fba2c17](file:///C:/Users/abdi7/Pictures/Typedown/9e9a27af-5d47-4e3d-96a0-0e4e4fba2c17.png)

Then proceed to:

* Install suggested plugins
* Create admin user
* Set Jenkins URL and complete setup

---

## ✅ Step 9: Post-Install Checks

| Command                    | Description         |
| -------------------------- | ------------------- |
| `git --version`            | Git installed       |
| `terraform -v`             | Terraform installed |
| `aws --version`            | AWS CLI installed   |
| `ansible --version`        | Ansible installed   |
| `docker --version`         | Docker installed    |
| `java -version`            | Java installed      |
| `systemctl status jenkins` | Jenkins is running  |

---

## 🎉 Summary of Installed Tools

* ✅ Git
* ✅ Terraform
* ✅ AWS CLI
* ✅ Ansible
* ✅ Docker with remote access
* ✅ Java (OpenJDK 17)
* ✅ Jenkins (Web UI: `http://<EC2-IP>:8080`)

---

# 🚀 Phase 3: GitHub + Git Project Setup

> 🎯 Goal: Set up a GitHub repository and practice **Git operations** from your EC2 server (your DevOps Project machine) to simulate real-world version control flows like cloning, committing, pushing, branching, and merging.

---

## 🧩 Why This Phase?

* You'll connect your local Git environment to a **remote GitHub repository**
* Manage your app's **source code lifecycle** via version control
* This step is essential before Jenkins CI/CD pipelines in the next phase

---

## ✅ Step 1: Create a GitHub Account & Repository

### 🔹 GitHub Signup (Skip if you already have an account)

* Go to [https://github.com/signup](https://github.com/signup)

### 🔹 Create a Repository

1. Go to [https://github.com](https://github.com)
2. Click ➕ → **New repository**
3. Repository name: Project-1-Java-Jenkins
4. Visibility: **Public**
5. Skip README file (we’ll add one manually)
6. Click **Create repository**

```
[Screenshot of the newly created empty 'hello-world' repository on GitHub.]
```

---

## ✅ Step 2: Generate GitHub Personal Access Token (PAT)

> GitHub no longer allows password-based pushes. You'll need a **token**.

1. Go to **GitHub → Settings → Developer Settings → Personal Access Tokens**

2. Choose **Fine-grained tokens** or **classic PAT**

3. Select scopes:
   
   * `repo` (full control)

4. Click **Generate Token**

5. **Copy and save it** (you won't see it again)

```
[Screenshot of the 'New personal access token' page on GitHub, highlighting the selected 'repo' scope.]
```

📌 Example:

```
ghp_abcdEfghIJKLmnoPQRStuvWxYz12345678
```

---

## ✅ Step 3: Set Up Git on Your EC2 Server

### 🧪 Check Git

```bash
git --version
```

If not installed:

```bash
sudo apt install git -y
```

### 🔧 Configure Global Identity

```bash
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
```

```
[Screenshot of the terminal after running the git config commands. No output is expected.]
```

---

## ✅ Step 4: Download & Prepare App Code (Java WAR)

We'll use a sample Java Maven project. Download and unzip it:

```bash
cd ~
git clone https://github.com/janjiralakirankumar/hello-world.git
cd hello-world
```

```
[Screenshot of the terminal showing the output from the 'git clone' command.]
```

---

## ✅ Step 5: Initialize Local Git Repo

```bash
git init .
git status
```

```
[Screenshot of the terminal showing the output of 'git status' with untracked files listed.]
```

---

## ✅ Step 6: Connect Local Repo to Remote (GitHub)

### 🔗 Add Remote Origin

Remove origin if it already exists

```bash
git remote remove origin
```

```bash
git remote add origin https://github.com/<your-username>/Project-1-Java-Jenkins.git
```

📌 Replace `<your-username>` with your GitHub handle
📌 You can verify:

```bash
git remote -v
```

```
[Screenshot of the terminal showing the output of 'git remote -v', displaying the fetch and push URLs.]
```

---

## ✅ Step 7: Commit & Push Code

### 🟢 Stage Files

```bash
git add .
git status
```

### 📦 Commit Files

```bash
git commit -m "Initial commit of hello-world Java app"
```

### ☁️ Push to Remote `master` or `main` branch

```bash
git push origin master
```

🧪 When prompted:

* **Username** → GitHub username
* **Password** → Paste your **PAT token**
  (*Note: PAT is invisible when pasting, that’s expected.*)

```
[Screenshot of the terminal showing the successful push to the remote repository.]
```

---

## ✅ Step 8: Git Branching Flow

### 🔀 Create Dev & Prod Branches

```bash
git branch dev
git branch prod
git branch         # Show current branches
```

### 🚀 Switch to Dev Branch

```bash
git checkout dev
```

### 📝 Add New File to Dev Branch

```bash
vi index.html
```

Paste below content and save:

```html
<html><body><h1>Hello from Dev Branch</h1></body></html>
```

### 🟢 Stage → Commit → Push

```bash
git add index.html
git commit -m "Added index.html in dev branch"
git push origin dev
```

```
[Screenshot of the terminal showing the successful push of the 'dev' branch to origin.]
```

---

## 🔁 Merge Dev → Prod → Master

### ⬅️ Switch & Merge

```bash
git checkout prod
git merge dev
git push origin prod
```

```bash
git checkout master
git merge prod
git push origin master
```

```
[Screenshot of the terminal showing the final successful push to the 'master' branch.]
```

---

## 🧪 Final Git Tree

```
master → main codebase  
dev    → feature/dev work  
prod   → staging before master
```

✅ Your GitHub repo now mirrors a **real CI/CD-ready code structure**

---

## 📦 Phase Completion Checklist

✅ Git installed and configured on EC2
✅ GitHub repo created and linked
✅ App source code downloaded and committed
✅ Dev/prod branches created and merged
✅ Remote pushes validated using Personal Access Token

---

# ⚙️ Phase 4: Jenkins CI Pipeline Setup — Build WAR from GitHub & Prepare for Deployment

> 🎯 **Goal:** Configure Jenkins to **pull code from GitHub**, **build a Maven-based Java project**, and generate a `.war` file — the foundation for automated deployments.

---

## 🧩 Why This Phase?

You're now connecting your GitHub repo to Jenkins, so every commit or push can:

* trigger an automated **build**
* generate a deployable `.war` file
* prepare you for **Docker** and **Tomcat** deployment in later phases

---

## ✅ Step 1: Access Jenkins UI

In your browser, open:

```
http://<EC2-Public-IP>:8080/
```

```
[Screenshot of the Jenkins login page or main dashboard.]
```

---

## ✅ Step 2: Complete Jenkins Initial Setup (if not done yet)

### 📌 Unlock Jenkins

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

### 🛠️ Install Plugins

* Choose **Install Suggested Plugins**

### 👤 Create Admin User

* Fill in: `Username`, `Password`, `Email`, etc.

```
[Screenshot of the Jenkins 'Create First Admin User' page.]
```

### 🌐 Set Jenkins URL

* Use: `http://<EC2-Public-IP>:8080/`

---

## ✅ Step 3: Install Required Plugins

From Jenkins home:

**Manage Jenkins → Plugin Manager → Available Tab**
Search and install:

* ✅ Maven Integration
* ✅ Pipeline Maven Integration
* ✅ GitHub Integration

📌 (Install without restart)

```
[Screenshot of the Jenkins Plugin Manager showing the successful installation of the required plugins.]
```

---

## ✅ Step 4: Configure Maven Tool in Jenkins

### 🔧 Global Tool Configuration

**Manage Jenkins → Tools → Maven Installations**

* Click “Add Maven”
  * Name: `maven`
  * Install automatically ✅ (or skip auto-install if Maven is already installed manually)
* Save

```
[Screenshot of the 'Maven Installations' section in Jenkins 'Global Tool Configuration' with the 'maven' tool configured.]
```

📌 Jenkins will download Maven when needed.

---

## ✅ Step 5: Create a Jenkins Maven Project

### ➕ New Item → Name: `hello-world` → Type: Maven Project → OK

---

### 🔍 Project Configuration

#### 1️⃣ General

✅ Tick: **GitHub project**
→ URL: `https://github.com/<your-username>/hello-world`

✅ Tick: **This project is parameterized** (Optional for future enhancements)

---

#### 2️⃣ Source Code Management

* Select: `Git`

* Repo URL:
  
  ```
  https://github.com/<your-username>/hello-world.git
  ```

* Credentials:
  
  * Click **Add → Jenkins → Username with password**
  * Username: Your GitHub username
  * Password: Your **Personal Access Token (PAT)**
  * ID: github-creds

✅ Choose `master` or `main` branch

```
[Screenshot of the 'Source Code Management' section in the Jenkins job configuration, showing the Git URL and selected credentials.]
```

---

#### 3️⃣ Build Triggers

* ✅ Tick: `Poll SCM` (Set schedule like `H/5 * * * *` for every 5 mins)
* (Optional in next phase: We'll add **GitHub Webhook** for real-time trigger)

---

#### 5️⃣ Build

* **Root POM:**
  
  ```
  pom.xml
  ```

* **Goals and options:**
  
  ```
  clean package
  ```

```
[Screenshot of the 'Build' section in the Jenkins job configuration, showing the Root POM and Goals.]
```

📌 This builds the `.war` and places it under:

```
target/hello-world-war-1.0.0.war
```

---

### ✅ Save the Job Configuration

---

## ✅ Step 6: Trigger the Build

From your project dashboard:

Click ➡️ **Build Now**

🕒 Wait for it to finish. Check the **Build Console Output** for logs.

```
[Screenshot of the Jenkins job dashboard showing a successful build (#1) in the 'Build History' panel.]
```

---

## 🧪 Step 7: Verify WAR Generation

SSH into the server and run:

```bash
ls /var/lib/jenkins/workspace/hello-world/target/
```

Expected output:

```
hello-world-war-1.0.0.war
```

```
[Screenshot of the terminal showing the output of the 'ls' command, confirming the presence of the .war file.]
```

✅ This confirms the build pipeline is working perfectly.

---

## 📦 Optional: Copy WAR for Use in Next Phases

```bash
cp /var/lib/jenkins/workspace/hello-world/target/hello-world-war-1.0.0.war ~/
```

---

## ✅ Phase 4 Completion Checklist

| Task                             | Status |
| -------------------------------- | ------ |
| Jenkins configured and running   | ✅      |
| GitHub repo connected to Jenkins | ✅      |
| Git + Maven plugins installed    | ✅      |
| Project set up to build `.war`   | ✅      |
| `.war` file generated            | ✅      |

---

# 🌐 Phase 5: Deploy WAR to Tomcat Server (on the Same EC2 Instance) — *(Optional)*

> 🎯 **Goal:** Install and configure **Tomcat 10** on the same EC2 instance where Jenkins is already running on port **8080**, and deploy the `.war` file generated by Jenkins.

---

## 🧩 Why This Phase?

Tomcat is a widely used Java servlet container for hosting `.war` files. In this phase, you'll:

* Install Tomcat
* Change Tomcat’s default port to avoid conflict with Jenkins
* Deploy the `.war` file
* Access the deployed application in your browser

---

## ✅ Install and Configure Tomcat

### ✅ Step 1: Update Package Index

```bash
sudo apt update
```

---

### ✅ Step 2: Install Tomcat 10 and Admin Webapps

```bash
sudo apt install tomcat10 tomcat10-admin -y
```

```
[Screenshot of the terminal after the tomcat10 installation is complete.]
```

> This installs:
> 
> * **Tomcat 10 core**
> * **Admin webapps** for `/manager` and `/host-manager` interfaces

---

### ⚠️ Important: Avoid Port Conflict with Jenkins

By default, **Jenkins uses port 8080**, which is **also the default Tomcat port**.

To avoid conflict, we’ll **change Tomcat’s port to `9999`** before starting the service.

---

### ✅ Step 3: Change Tomcat Port (8080 → 9999)

```bash
sudo vi /etc/tomcat10/server.xml
```

Inside `vi`, press `Esc+:`, then type:

```bash
g/8080/s//9999/g
```

Then save and exit:

```bash
ESC + :wq!
```

```
[Screenshot of the vi editor showing the line in server.xml where the port has been changed from 8080 to 9999.]
```

---

### ✅ Step 4: Start and Enable Tomcat Service

```bash
sudo systemctl start tomcat10
sudo systemctl enable tomcat10
```

---

### ✅ Step 5: Check Status

```bash
sudo systemctl status tomcat10
```

You should see:

```
Active: active (running)
```

---

### ✅ Step 6: Test Tomcat Web UI

Open your browser:

```
http://<EC2-Public-IP>:9999/
```

> Ensure port **9999** is allowed in your EC2 security group’s **inbound rules**.

```
[Screenshot of a browser showing the default Apache Tomcat welcome page on port 9999.]
```

---

## ✅ Deploy WAR File from Jenkins to Tomcat

### ✅ Step 7: Copy WAR File to Tomcat Webapps Directory

```bash
sudo cp /var/lib/jenkins/workspace/hello-world/target/hello-world-war-1.0.0.war /var/lib/tomcat10/webapps/
```

```
[Screenshot of the terminal after executing the 'cp' command to move the WAR file.]
```

> 🔁 Optionally, rename to `hello.war` for a cleaner URL:
> 
> ```bash
> sudo cp hello-world-war-1.0.0.war /var/lib/tomcat10/webapps/hello.war
> ```

---

### ✅ Step 8: Restart Tomcat

```bash
sudo systemctl restart tomcat10
```

---

### ✅ Step 9: Access Your Deployed App

Open in browser:

```
http://<EC2-Public-IP>:9999/hello-world-war-1.0.0/
```

or if renamed:

```
http://<EC2-Public-IP>:9999/hello/
```

**Expected Output:**

> Hello World...! Welcome to DevOps CICD training...
> It is now Fri May 30 12:41:53 UTC 2025
> You are coming from `<Your IP Address>`

```
[Screenshot of the browser showing the 'Hello World...!' application running successfully on Tomcat.]
```

🎉 Application is now deployed successfully!

---

## 🧹 (Optional) Uninstall Tomcat After Testing

If you wish to remove Tomcat after validating the deployment:

### 🛑 1. Stop Tomcat

```bash
sudo systemctl stop tomcat10
```

---

### 🗑️ 2. Remove Packages

```bash
sudo apt remove --purge tomcat10 tomcat10-admin -y
```

---

### 🧼 3. Delete Leftover Files

```bash
sudo rm -rf /etc/tomcat10 /var/lib/tomcat10 /var/log/tomcat10 /usr/share/tomcat10
```

---

### 🔄 4. Reload Daemon

```bash
sudo systemctl daemon-reload
```

---

### ✅ 5. Confirm Cleanup

```bash
ps aux | grep tomcat
```

```
[Screenshot of the terminal showing no output for the 'ps aux | grep tomcat' command, confirming cleanup.]
```

✅ No Tomcat processes should be running.

---

## ✅ Phase 5 Completion Checklist

| Task                                  | Status |
| ------------------------------------- | ------ |
| Tomcat installed and configured       | ✅      |
| Port changed to avoid conflict (9999) | ✅      |
| WAR file deployed to Tomcat           | ✅      |
| Application accessible in browser     | ✅      |

---

# 🐳 Phase 6: Dockerize and Deploy WAR in a Container

> 🎯 **Goal:** Build a Docker image using the generated `.war` and run it as a container exposing port `8080`.

---

## ✅ Step 1: Prepare Dockerfile

In your EC2 instance:

```bash
cd ~
vi Dockerfile
```

Paste this content:

```Dockerfile
# Use official Tomcat as base image
FROM tomcat:8-jre8

# Maintainer info
LABEL maintainer="devops-lab@example.com"

# Deploy WAR into Tomcat container
ADD hello-world-war-1.0.0.war /usr/local/tomcat/webapps/
```

💾 Save and exit: `ESC + :wq!`

```
[Screenshot of the terminal showing the contents of the Dockerfile in the vi editor.]
```

---

## ✅ Step 2: Copy WAR File (if not already there)

```bash
cp /var/lib/jenkins/workspace/hello-world/target/hello-world-war-1.0.0.war ~/
```

---

## ✅ Step 3: Build Docker Image

```bash
docker build -t helloworld-image .
```

```
[Screenshot of the terminal showing the output of the 'docker build' command, ending with 'Successfully tagged helloworld-image:latest'.]
```

---

## ✅ Step 4: Run Docker Container

Stop any previous container:

```bash
docker container stop devops-hello || true
docker container rm devops-hello || true
```

Then start fresh:

```bash
docker run -d -p 8081:8080 --name devops-hello helloworld-image
```

---

## ✅ Step 5: Verify Running Container

```bash
docker ps
```

Expected output:

```
CONTAINER ID   IMAGE             PORTS                    NAMES
xxxxx          helloworld-image  0.0.0.0:8081->8080/tcp   devops-hello
```

```
[Screenshot of the terminal showing the output of 'docker ps' with the 'devops-hello' container running.]
```

---

## ✅ Step 6: Access the App in Browser

Open:

```
http://<EC2-Public-IP>:8081/hello-world-war-1.0.0/
```

```
[Screenshot of the browser showing the 'Hello World...!' application running successfully from the Docker container on port 8081.]
```

✅ You should see the deployed Java app running inside a Docker container!

---

## ✅ Phase 6 Completion Checklist

| Task                                     | Status |
| ---------------------------------------- | ------ |
| Dockerfile created                       | ✅      |
| WAR copied and Docker image built        | ✅      |
| Container deployed on port 8080          | ✅      |
| Application accessible via Docker/Tomcat | ✅      |

---

## 🎯 You Now Have:

* ✅ Jenkins CI build
* ✅ Tomcat standalone deployment
* ✅ Dockerized deployment (containerized WAR)

---

# 🔔 Phase 7: GitHub Webhook Integration — Auto Build Jenkins Job on Code Push

> 🎯 **Goal:** Automatically trigger Jenkins builds whenever code is pushed to your GitHub repository — completing a real CI pipeline!

---

## 🧩 Why This Phase?

Until now, builds were triggered manually in Jenkins. This phase:

* Connects GitHub → Jenkins
* Ensures **push events automatically trigger Jenkins builds**
* Simulates a **real CI/CD workflow** in production setups

---

## ✅ Prerequisites Recap

Before proceeding, ensure:

✅ Jenkins is up and running at `http://<EC2-Public-IP>:8080/`

✅ You have an active GitHub repository (e.g., `hello-world`)

✅ Your Jenkins job is already configured with:

* Git repo URL
* GitHub credentials (username + PAT)

---

## ✅ Step 1: Install GitHub Plugin (if not already)

From Jenkins UI:

* Navigate to: **Manage Jenkins → Plugin Manager → Available**
* Search for: `GitHub Integration`
* ✅ Install **GitHub Integration plugin** (and **GitHub API plugin**, if prompted)
* Restart Jenkins if needed

---

## ✅ Step 2: Configure Jenkins Job to Listen for Webhooks

### 🔧 Go to:

**Jenkins → hello-world (Job) → Configure**

### Scroll to 👉 **Build Triggers**

✅ Enable:

```
☑️ GitHub hook trigger for GITScm polling
```

📌 This listens for real-time webhook payloads from GitHub

Click **Save**

```
[Screenshot of the 'Build Triggers' section in the Jenkins job configuration with 'GitHub hook trigger for GITScm polling' checked.]
```

---

## ✅ Step 3: Expose Jenkins to GitHub (Ensure Port 8080 is Public)

From your EC2 instance:

* Jenkins should be accessible publicly at:
  
  ```
  http://<EC2-Public-IP>:8080/
  ```

---

## ✅ Step 4: Create a Webhook in GitHub Repo

1. Go to your **GitHub repository** → `Settings` → `Webhooks` → **Add Webhook**

2. Fill out the form:

| Field               | Value                                         |
| ------------------- | --------------------------------------------- |
| Payload URL         | `http://<EC2-Public-IP>:8080/github-webhook/` |
| Content Type        | `application/json`                            |
| Secret (optional)   | *Leave blank*                                 |
| Just the push event | ✅ Default selected                            |

Click **Add Webhook**

```
[Screenshot of the 'Add webhook' page on GitHub with the Payload URL and other settings configured.]
```

---

## ✅ Step 5: Test Webhook Trigger

### 🔁 Make a change in GitHub

1. Edit any file in your GitHub repo (e.g., `README.md` or `index.html`)
2. Commit the change directly to `master` or `dev`

```
[Screenshot of making a small edit and committing the change directly in the GitHub web UI.]
```

---

## ✅ Step 6: Watch Jenkins Build Automatically

Return to Jenkins:

* Jenkins job (`hello-world`) will **auto-trigger**
* Monitor via **Build History** or **Console Output**

```
[Screenshot of the Jenkins job dashboard showing a new build automatically triggered by a GitHub push.]
```

🎉 You should see a new build has started *without manual trigger!*

---

## 🧪 Optional: Verify Webhook Status

Go to:
**GitHub Repo → Settings → Webhooks → Click your webhook**

You’ll see:

* ✅ “Last delivery was successful”
* Payload + Response logs from Jenkins

```
[Screenshot of the GitHub webhook deliveries page showing a recent delivery with a green checkmark and a 200 response code.]
```

---

## ✅ Phase 7 Completion Checklist

| Task                                             | Status |
| ------------------------------------------------ | ------ |
| GitHub plugin installed in Jenkins               | ✅      |
| Jenkins job configured to listen for GitHub push | ✅      |
| Webhook added in GitHub settings                 | ✅      |
| Push triggers Jenkins job automatically          | ✅      |

---

# 🔐 Phase 8: Image Vulnerability Scanning with **Trivy** + Code Quality Analysis with **SonarQube**

> 🎯 **Objective:**
> Secure and maintain clean, production-ready code by integrating:
> 
> * ✅ **Trivy**: Docker image vulnerability scanner
> * ✅ **SonarQube**: Static code analysis tool for Java apps

---

## 🧩 Why This Phase Matters

Integrating Trivy and SonarQube ensures:

* 🔍 Early detection of security flaws in container images
* 🧼 Clean code with enforced standards (bugs, code smells, vulnerabilities)
* 🔐 DevSecOps in action → continuous security + quality enforcement

---

## 🛡️ Part A: Vulnerability Scanning with Trivy

### ✅ Step 1: Install Trivy

```bash
sudo apt install wget apt-transport-https gnupg lsb-release -y

wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | \
  sudo gpg --dearmor -o /usr/share/keyrings/trivy.gpg

echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] \
  https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -cs) main" | \
  sudo tee /etc/apt/sources.list.d/trivy.list

sudo apt update
sudo apt install trivy -y
```

```bash
trivy --version```

```

[Screenshot of the terminal showing the output of 'trivy --version'.]

```

---

### ✅ Step 2: Scan Docker Image

Run Trivy against your image (replace with your actual image name):

```bash
trivy image --exit-code 1 --severity HIGH,CRITICAL helloworld-image
```

* 🚫 If high/critical vulnerabilities are found → exit code `1` will break your build (useful in CI/CD)

```
[Screenshot of the terminal showing the Trivy scan summary table with detected vulnerabilities.]
```

---

### ✅ Step 3: Export Report (Optional)

```bash
trivy image --format table --output trivy-report.txt helloworld-image
cat trivy-report.txt
```

---

## 🧾 Jenkins Integration (Optional)

In your Jenkins pipeline, add Trivy as a post-build step:

```bash
stage('Security Scan - Trivy') {
    steps {
        sh 'trivy image --exit-code 1 --severity HIGH,CRITICAL helloworld-image'
    }
}
```

---

## 📊 Part B: Static Code Analysis with SonarQube

### ✅ Step 1: Prerequisites

* ✅ Java 17 installed
* ✅ Port `9000` allowed in EC2 Security Group
* ✅ Jenkins or standalone terminal access

---

### ✅ Step 2: Install and Start SonarQube

```bash
# Install Java and unzip
sudo apt update && sudo apt install -y unzip wget openjdk-17-jdk tmux

# Create user & directory
sudo useradd -m -d /opt/sonarqube sonar
sudo mkdir -p /opt/sonarqube
sudo chown sonar:sonar /opt/sonarqube

# Switch to sonar user
sudo su - sonar << 'EOF'
cd /opt/sonarqube
wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-10.4.1.88267.zip
unzip sonarqube-10.4.1.88267.zip
mv sonarqube-10.4.1.88267 sonarqube

# Expose SonarQube on all interfaces
sed -i 's|#sonar.web.host=127.0.0.1|sonar.web.host=0.0.0.0|' sonarqube/conf/sonar.properties

# Start SonarQube in tmux to keep it running in background
tmux new -d -s sonar '/opt/sonarqube/sonarqube/bin/linux-x86-64/sonar.sh start'
EOF
```

```
[Screenshot of the terminal after running the tmux command to start SonarQube, showing the process has started.]
```

---

### 🌐 Step 3: Access SonarQube Dashboard

Visit in browser after \~1–2 min:

```text
http://<EC2-PUBLIC-IP>:9000/
```

**Login** with:

* **Username:** `admin`
* **Password:** `admin` (you'll be asked to reset)

```
[Screenshot of the SonarQube login page in a web browser.]
```

---

### 🔑 Step 4: Generate Token

#### 🛠️ Step-by-Step: Configure SonarScanner Authorization

---

### ✅ Step 1: Log in to SonarQube UI

1. Open your browser.

2. Visit your SonarQube server URL:
   📍 `http://<your-sonarqube-ip>:9000`
   Example: [http://35.178.198.214:9000](http://35.178.198.214:9000)

3. Log in:
   
   * Default admin user: `admin`
   * Default password: `admin` (change it on first login)

---

### ✅ Step 2: Create a **User Token**

1. Click on your username in the top-right corner → click **"My Account"**

2. Go to the **"Security"** tab

3. Under **"Generate Tokens"**:
   
   * Enter a name (e.g., `hello-world-token`)
   * Click **Generate**

4. **Copy the token** now (you won’t see it again)

```
[Screenshot of the SonarQube Security tab showing where to generate a new token.]
```

---

### ✅ Step 3: Use Token in `sonar-project.properties`

Edit your `sonar-project.properties` file in the project folder (`~/hello-world`):

```bash
nano sonar-project.properties
```

Ensure it contains the following:

```properties
sonar.projectKey=hello-world
sonar.projectName=hello-world
sonar.projectVersion=1.0
sonar.sources=.
sonar.host.url=http://35.178.198.214:9000
sonar.token=your_generated_token_here
```

> 🔒 **Important**: Replace `your_generated_token_here` with the real token. If you're sharing code, **don’t commit this file** — or use the token via CLI instead (see below).

Save and exit (`Ctrl + O`, then `Enter`, then `Ctrl + X`).

---

### 🛠️ Step 5: Install SonarScanner CLI

```bash
# Step 1: Go to home directory
cd ~

# Step 2: Download SonarScanner CLI
wget https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-5.0.1.3006-linux.zip

# Step 3: Unzip the downloaded archive
unzip sonar-scanner-cli-5.0.1.3006-linux.zip

# Step 4: Rename extracted folder
mv sonar-scanner-5.0.1.3006-linux sonar-scanner

# Step 5: Add sonar-scanner to PATH
echo 'export PATH=$PATH:$HOME/sonar-scanner/bin' >> ~/.bashrc
source ~/.bashrc

# Step 6: Verify installation
sonar-scanner -v
```

```
[Screenshot of the terminal showing the output of 'sonar-scanner -v'.]
```

---

### 📁 Step 6: Configure Java Project for SonarQube

Ensure your Maven build creates the `target/` directory:

```bash
cd ~/hello-world
sudo apt install maven
mvn clean install
```

Create SonarQube config file:

```bash
vi sonar-project.properties
```

Paste below content (replace IP/token):

```properties
sonar.projectKey=hello-world
sonar.projectName=HelloWorld Java App
sonar.projectVersion=1.0
sonar.sources=.
sonar.java.binaries=target
sonar.host.url=http://<EC2-PUBLIC-IP>:9000
sonar.login=<YOUR_SONAR_TOKEN>
```

---

### 🚦 Step 7: Run SonarScanner

```bash
sonar-scanner
```

```
[Screenshot of the terminal showing the 'EXECUTION SUCCESS' message after the SonarScanner finishes.]
```

---

### 📊 Step 8: View Code Quality Dashboard

Open:

```text
http://<EC2-PUBLIC-IP>:9000/dashboard?id=hello-world
```

```
[Screenshot of the SonarQube project dashboard for 'hello-world', showing a 'Passed' quality gate and analysis results.]
```

---

## ✅ Phase 8 Completion Checklist

| Task                                         | Status |
| -------------------------------------------- | ------ |
| 🔧 Trivy installed and scanned image         | ✅      |
| 🔁 Trivy used in pipeline (manual/automated) | ✅      |
| 🔧 SonarQube installed and running           | ✅      |
| ⚙️ SonarScanner installed and working        | ✅      |
| 🧪 Java project analyzed via SonarQube       | ✅      |
| 📊 Results viewable on SonarQube UI          | ✅      |

---

# 📈 Phase 9: Continuous Monitoring with **Prometheus + Grafana**

> 🎯 **Goal:** Deploy **Prometheus** and **Grafana** on your single EC2 server to monitor container performance and resource metrics in real-time.

---

## 🧩 Why This Phase?

Monitoring is a critical part of DevOps. With Prometheus and Grafana, you can:

* Scrape system and Docker metrics (Prometheus)
* Visualize them using powerful dashboards (Grafana)
* Gain real-time **observability** into your CI/CD and runtime infrastructure

---

## ⚙️ Architecture (Same Single Node):

```
[Docker Container]
    ↑
 [Prometheus] ← (Scrapes metrics from Docker, Node, etc.)
    ↑
 [Grafana] ← (Visualizes metrics with dashboards)
```

---

## ✅ Step 1: Create `monitoring` Directory

# 🚀 **Prometheus + Node Exporter Installation Commands (Ubuntu 24.04 EC2)**

### ✅ Step 1: Create `monitoring` Directory

```bash
cd ~
mkdir monitoring && cd monitoring
```

---

### ✅ Step 2: Install Prometheus

```bash
wget https://github.com/prometheus/prometheus/releases/download/v2.51.2/prometheus-2.51.2.linux-amd64.tar.gz
tar -xvf prometheus-2.51.2.linux-amd64.tar.gz
mv prometheus-2.51.2.linux-amd64.tar.gz prometheus
cd prometheus
```

---

### ✅ Step 3: Update Prometheus Configuration

```bash
vi prometheus.yml
```

Paste the following config:

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'node'
    static_configs:
      - targets: ['localhost:9100']

  - job_name: 'docker'
    static_configs:
      - targets: ['localhost:9323']
```

Save and exit.

```
[Screenshot of the vi editor showing the updated prometheus.yml configuration.]
```

---

### ✅ Step 4: Install Node Exporter

```bash
cd ~
wget https://github.com/prometheus/node_exporter/releases/download/v1.8.1/node_exporter-1.8.1.linux-amd64.tar.gz
tar -xvzf node_exporter-1.8.1.linux-amd64.tar.gz
cd node_exporter-1.8.1.linux-amd64
./node_exporter &
```

✅ Node Exporter runs at: `http://<EC2-Public-IP>:9100/metrics`

```
[Screenshot of the Node Exporter /metrics endpoint in a browser, showing text-based metrics.]
```

---

### ✅ Step 5: Enable Docker Metrics (port 9323)

```bash
sudo vi /lib/systemd/system/docker.service
```

Edit `ExecStart` as:

```
ExecStart=/usr/bin/dockerd -H fd:// -H tcp://0.0.0.0:4243 --metrics-addr 0.0.0.0:9323 --experimental
```

Then reload and restart Docker:

```bash
sudo systemctl daemon-reload
sudo systemctl restart docker
```

✅ Docker metrics available at: `http://<EC2-Public-IP>:9323/metrics`

```
[Screenshot of the Docker /metrics endpoint in a browser, showing text-based metrics.]
```

---

### ✅ Step 6: Start Prometheus

```bash
cd ~/monitoring/prometheus
./prometheus --config.file=prometheus.yml &
```

✅ Prometheus runs at: `http://<EC2-Public-IP>:9090`

```
[Screenshot of the Prometheus UI 'Targets' page, showing all three targets (prometheus, node, docker) with a state of 'UP'.]
```

---

## ✅ Step 6: Install Grafana

### 📥 Install Using APT

```bash
sudo apt install -y apt-transport-https software-properties-common
sudo mkdir -p /etc/apt/keyrings
wget -q -O - https://apt.grafana.com/gpg.key | \
  gpg --dearmor | sudo tee /etc/apt/keyrings/grafana.gpg > /dev/null

echo "deb [signed-by=/etc/apt/keyrings/grafana.gpg] \
https://apt.grafana.com stable main" | \
sudo tee /etc/apt/sources.list.d/grafana.list

sudo apt update
sudo apt install grafana -y
```

---

## ✅ Step 7: Start & Enable Grafana

```bash
sudo systemctl start grafana-server
sudo systemctl enable grafana-server
```

🖥️ Open Grafana:

```
http://<EC2-Public-IP>:3000/
```

Default credentials:

* Username: `admin`
* Password: `admin`

```
[Screenshot of the Grafana login page in a web browser.]
```

---

## ✅ Step 8: Add Prometheus as Data Source in Grafana

1. Login to Grafana
2. Go to `Settings → Data Sources → Add Data Source`
3. Choose **Prometheus**
4. Set URL: `http://localhost:9090`
5. Click **Save & Test**

```
[Screenshot of the Grafana data source configuration page for Prometheus, showing the green 'Data source is working' notification.]
```

---

## ✅ Step 9: Import Docker & Node Dashboards

1. In Grafana → `+ Create → Import`

2. Use Dashboard IDs:
   
   * Docker Monitoring: **193**
   * Node Exporter Full: **1860**

3. Set Prometheus as the data source and click **Import**

```
[Screenshot of an imported Grafana dashboard (e.g., Node Exporter Full) displaying various graphs and metrics for the EC2 instance.]
```

🎉 You’ll now see:

* Docker container metrics
* System CPU, memory, disk usage

---

## ✅ Phase 9 Completion Checklist

| Task                                       | Status |
| ------------------------------------------ | ------ |
| Prometheus installed and configured        | ✅      |
| Node exporter running                      | ✅      |
| Docker metrics exposed on port 9323        | ✅      |
| Grafana installed and running on port 3000 | ✅      |
| Dashboards imported successfully           | ✅      |

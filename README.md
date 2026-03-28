## ☕ Barista Cafe Website Deployment (Azure + Terraform) 

## 📌 Project Description
This project demonstrates the deployment of a static website on an Azure Virtual Machine by provisioning infrastructure using Terraform and configuring the Nginx web server.

## ⚙️ Infrastructure Setup
Using Terraform, the following Azure resources were created:
- Virtual Machine (VM)
- Virtual Network (VNet)
- Subnet
- Network Interface (NIC)
- Public IP Address
- Network Security Group (NSG)

## 🌐 Deployment Steps
1. Created infrastructure using Terraform
2. Connected to VM using SSH
3. Installed Nginx web server
4. Transferred website files using SCP
5. Configured Nginx to host the website
6. Enabled public access via port 80

## 📸 Output
The static website was successfully deployed and accessed using the public IP address.

## 💡 Key Learnings
- Infrastructure as Code (IaC) using Terraform
- Azure resource provisioning
- Linux server management
- Web server configuration (Nginx)
- File transfer using SCP

## 📎 Note
The VM  stopped to avoid charges. 

## 🚀 Technologies Used

* HTML, CSS, JavaScript
* Microsoft Azure (Virtual Machine, Networking)
* Terraform (Infrastructure as Code)
* Nginx (Web Server)
* Git & GitHub (Version Control)

---

## 🏗️ Project Architecture

1. Terraform is used to create:

   * Resource Group
   * Virtual Network
   * Subnet
   * Network Interface
   * Linux Virtual Machine

2. Nginx is installed on the VM

3. Website files are copied to:

   ```
   /var/www/html/
   ```

4. Website is accessed using Public IP

---

## 🌐 Live Website

Access the deployed website using:

```
http://<your-public-ip>
```

---



## ⚙️ How to Run (Terraform)

1. Initialize Terraform:

```
terraform init
```

2. Plan the deployment:

```
terraform plan
```

3. Apply the configuration:

```
terraform apply
```

---

## 👩‍💻 Author

**Siva Parvathi Ayinakota (Paru)**
 Aspiring Cloud & Data Professional

---

## ⭐ Key Highlights

* Hands-on Azure cloud deployment
* Infrastructure automation using Terraform
* Real-time website hosting using Nginx
* End-to-end DevOps workflow

---

## 📌 Note

Terraform state files and `.terraform` folders are excluded using `.gitignore` to avoid large file issues.

---

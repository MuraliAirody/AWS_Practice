# Amazon EC2 (Elastic Compute Cloud)

## 1. What is EC2?

Amazon EC2 is a service that provides **resizable virtual servers in the cloud**.

It allows developers to run applications without managing physical hardware.

Typical use cases:
- Hosting backend applications (Spring Boot, Node.js, Python)
- Running microservices
- Batch processing
- Hosting APIs and web applications

---

## 2. Key Features

- On-demand virtual servers
- Multiple instance types
- Auto scaling
- Load balancing support
- Secure access via key pairs
- Flexible storage options

---

## 3. Basic Architecture

Client  
↓  
Load Balancer  
↓  
EC2 Instance (Application Server)  
↓  
Database / Storage

Example:

User → API Gateway / Load Balancer → EC2 (Spring Boot App) → RDS / S3

---

## 4. EC2 Components

### Instance
A virtual server running your application.

Example:
Spring Boot JAR deployed on Linux EC2.

---

### AMI (Amazon Machine Image)

Template used to launch EC2 instance.

Contains:
- OS (Linux / Windows)
- Pre-installed software
- Application configuration

Example:
Amazon Linux AMI

---

### Instance Types

Different hardware configurations.

Examples:

| Type | Use Case |
|-----|------|
| t2 / t3 | Small apps, dev environments |
| m5 | General purpose workloads |
| c5 | CPU intensive workloads |
| r5 | Memory intensive workloads |

---

### Key Pair

Used to securely access EC2.

SSH authentication instead of password.

Example:
ssh -i mykey.pem ec2-user@public-ip
---

### Security Groups

Acts as a **virtual firewall**.

Controls:

- inbound traffic
- outbound traffic

Example:

Allow:
```
Port 22 → SSH
Port 80 → HTTP
Port 443 → HTTPS
Port 8080 → Spring Boot
```


---

### Elastic IP

A **static public IP address** that can be attached to an EC2 instance.

Used when public IP must not change.

---

## 5. Steps to Deploy Spring Boot Application on EC2

### Step 1
Launch EC2 instance.

Choose:
- AMI
- instance type
- key pair
- security group

---

### Step 2
Connect to server.
ssh -i key.pem ec2-user@public-ip


---

### Step 3
Install Java.
sudo yum install java-17-amazon-corretto


---

### Step 4
Upload JAR file.

Using SCP or WinSCP.
scp -i key.pem app.jar ec2-user@public-ip:/home/ec2-user


---

### Step 5
Run application.
java -jar app.jar


Application available at:
http://public-ip:8080


---

## 6. Scaling EC2 Applications

### Auto Scaling

Automatically adjusts number of EC2 instances based on traffic.

Example:

- Low traffic → 1 instance
- High traffic → 10 instances

---

### Load Balancer

Distributes incoming traffic across multiple EC2 instances.

AWS service used:

Elastic Load Balancer (ELB)

---

## 7. Storage Options

### EBS (Elastic Block Store)

Persistent storage attached to EC2.

Example:
Database files.

---

### Instance Store

Temporary storage that disappears if instance stops.

---

### S3 Integration

Applications running on EC2 often store files in S3.

Example:

EC2 → Upload files → S3

---

## 8. Common Production Architecture

User  
↓  
CloudFront (CDN)  
↓  
Load Balancer  
↓  
EC2 Instances (Auto Scaling Group)  
↓  
RDS / S3

---

## 9. Advantages

- Full server control
- Flexible configuration
- Supports any programming language
- Easy scaling
- Pay-as-you-go pricing

---

## 10. Interview Questions

### What is EC2?

EC2 is a cloud service that provides scalable virtual servers for running applications.

---

### Difference Between EC2 and S3

| Feature | EC2 | S3 |
|------|------|------|
| Type | Compute | Storage |
| Use | Run applications | Store files |
| Example | Spring Boot server | Image storage |

---

### What is Security Group?

A virtual firewall controlling inbound and outbound traffic to EC2 instances.

---

### What is AMI?

Template used to launch EC2 instances containing OS and configuration.

---

### How to deploy Spring Boot on EC2?

1. Launch EC2
2. Connect via SSH
3. Install Java
4. Upload JAR
5. Run application
# Auto Scaling Group with Application Load Balancer
## About Project

This project showcases how to build a **highly available, scalable web application infrastructure** on **Amazon Web Services (AWS)** using different cloud services.

Specifically, it involves setting up an **Auto Scaling Group (ASG)** integrated with an **Application Load Balancer (ALB)** to host a lightweight web server (Apache) on **Amazon Linux** EC2 instances.

The primary goal of this project:

- **Scalability**: The application can automatically scale up or down based on traffic (CPU utilization).
- **High Availability**: Traffic is distributed across multiple instances in different availability zones.
- **Fault Tolerance**: If an instance fails, the ASG automatically replaces it.
- **Cost-Efficiency**: Resources scale dynamically according to demand.

## **Project Architecture Overview**

- **Launch templates**
    - home-LT
    - laptop-LT
    - mobile-LT
- **Auto scaling group**
    - Launch
- **Load balancer.**
    - application-LB
- **Target groups.**
    - home-TG
    - laptop-TG
    - mobile-TG

![Project-IMG.jpg](attachment:b7a889b1-ff3d-4cff-a03b-aca8ce90149f:Project-IMG.jpg)

## **Tools and Technologies**

- AWS EC2
- AWS Auto Scaling
- AWS Application Load Balancer (ALB)
- AWS Target Groups
- Amazon Linux + Apache

# **Templates**

> What is an **AWS Template**?
> 

> An **AWS template** is a **blueprint written in code** that defines the infrastructure and services you want to deploy in your AWS environment. Templates are used in **Infrastructure as Code** to automate the creation, update, and deletion of AWS resources.
> 

> AWS templates help deploy environments consistently and automatically without manual setup. They also support scaling large systems and tracking changes using version control tools like Git.
> 

# **Step 1 : Launch Templates**

1. click on Instances 
2. click on **Launch templates - home, laptop, mobile**
- Create Launch templates (home-LT)
    - **Launch template name and description**
        - name: home-LT
        - description: This is my home Webserver
    - **Application and OS Images (Amazon Machine Image)**
        - AMI: Amazon Linux
    - **Instance type**
        - Type: t2.micro
    - **Key pair**
        - pem-key: pem_server_key
    - **Network settings**
        - Security groups: launch-wizard-1
    - **Advanced details**
        - User data Script:
        
        ```bash
        #!/bin/bash
        sudo yum update -y
        sudo yum install httpd -y
        sudo systemctl start httpd
        sudo systemctl enable httpd
        echo "<h1> This is Home Page from $(hostname -f)</h1>" > /var/www/html/index.html
        ```
        
    - **Create Launch Template**
        
        Now Launched “home-LT” Template   
        
- Create Launch templates (laptop-LT)
    - **Launch template name and description**
        - name: laptop-LT
        - description: This is my laptop Webserver
    - **Application and OS Images (Amazon Machine Image)**
        - AMI: Amazon Linux
    - **Instance type**
        - Type: t2.micro
    - **Key pair**
        - pem-key: pem_server_key
    - **Network settings**
        - Security groups: launch-wizard-1
    - **Advanced details**
        - User data Script:
        
        ```bash
        #!/bin/bash
        sudo yum update -y
        sudo yum install httpd -y
        sudo systemctl start httpd
        sudo systemctl enable httpd
        mkdir /var/www/html/laptop/
        echo "<h1> This is Laptop Page from $(hostname -f)</h1>" > /var/www/html/laptop/index.html
        ```
        
    - **Create Launch Template**
        
        Now Launched “laptop-LT” Template   
        
- Create Launch templates (mobile-LT)
    - **Launch template name and description**
        - name: mobile-LT
        - description: This is my mobile Webserver
    - **Application and OS Images (Amazon Machine Image)**
        - AMI: Amazon Linux
    - **Instance type**
        - Type: t2.micro
    - **Key pair**
        - pem-key: pem_server_key
    - **Network settings**
        - Security groups: launch-wizard-1
    - **Advanced details**
        - User data Script:
        
        ```bash
        #!/bin/bash
        sudo yum update -y
        sudo yum install httpd -y
        sudo systemctl start httpd
        sudo systemctl enable httpd
        mkdir /var/www/html/mobile/
        echo "<h1> This is Mobile Page from $(hostname -f)</h1>" > /var/www/html/mobile/index.html
        ```
        
    - **Create Launch Template**
        
        Now Launched “mobile-LT” Template   
        

# **Auto Scaling Group**

> What is **Auto Scaling Group**?
> 

> An **Auto Scaling Group (ASG)** is an AWS feature that **automatically manages a group of EC2 instances**. It can **launch or terminate instances** based on defined conditions to maintain the desired number of healthy instances and ensure your application stays available and scalable.
> 

> Features: Automatic scaling, Health checks, Minimum, maximum, and desired capacity, Integration with Load Balancer.
> 

# **Step 2 : Auto Scaling Group**

1. Click on Auto Scaling → Auto Scaling Group
2. Click on  **Create Auto Scaling group**
- create **home** Auto Scaling group
    - Choose launch template
        - Name: home-ASG
        - template: home-LT→
    - Choose instance launch options
        - Network → Availability Zones and subnets: select 3 zones →
    - Configure group size and scaling
        
        > **Scaling Policy →**  static → Not required policy
        > 
        - Group size → Desired capacity: 2
        - Scaling → Min & Max desired capacity: 2-2 →
    - Review
        - check all details & Create Auto Scaling Group
- create **laptop** Auto Scaling group
    - Choose launch template
        - Name: laptop-ASG
        - template: laptop -LT→
    - Choose instance launch options
        - Network → Availability Zones and subnets: select 3 zones →
    - Configure group size and scaling
        
        > **Scaling Policy →**  dynamic → Target tracking scaling policy
        > 
        
        > **Target value: 50**
        > 
        
        > **Instance warmup: 300 s**
        > 
        - Group size → Desired capacity: 2
        - Scaling → Min & Max desired capacity: 1-4 →
    - Review
        - check all details & Create Auto Scaling Group
- create **mobile** Auto Scaling group
    - Choose launch template
        - Name: mobile-ASG
        - template: mobile-LT→
    - Choose instance launch options
        - Network → Availability Zones and subnets: select 3 zones →
    - Configure group size and scaling
        
        > **Scaling Policy →**  dynamic → Target tracking scaling policy
        > 
        
        > **Target value: 50**
        > 
        
        > **Instance warmup: 300 s**
        > 
        - Group size → Desired capacity: 2
        - Scaling → Min & Max desired capacity: 1-4 →
    - Review
        - check all details & Create Auto Scaling Group
    - Under mobile-ASG → Automatic scaling → Create Scheduled actions
        - ****Name: greatindiansale
        - Desired capacity: 7
        - Min: 5
        - Max: 15
        - Recurrence: Corn → 30 4 21 9 *
        - Time zone: Etc/UTC
        - Specific start time: 2025/05/30 04:30

# **Target Group**

> What is **Target Group?**
> 

> A **Target Group** in AWS is a set of servers (like EC2 instances) that receive traffic from a Load Balancer. It tells the Load Balancer where to send incoming requests. Think of it as a list of servers that handle your application's work
> 

# **Step 3 : Create Target Group**

1. click Load Balancing
2. click on **Target Group → create Target Group**
- create **home** Target group
    - Choose a target type → instances
    - Target group name → home-TG
    - click → create target group
- create **laptop** Target group
    - Choose a target type → instances
    - Target group name → laptop-TG
    - Health check path → /laptop/
    - click → create target group
- create **mobile** Target group
    - Choose a target type → instances
    - Target group name → mobile-TG
    - Health check path → /mobile/
    - click → create target group

# **Step 4 : Attach Target Group to Auto Scaling Group**

1. Click on Auto Scaling → Auto Scaling Group
2. Click on  **Action → Edit** 
- Attach **home** target Group
    
    Load balancing → select target groups → home-TG
    
- Attach **laptop** target Group
    
    Load balancing → select target groups → laptop-TG
    
- Attach **mobile** target Group
    
    Load balancing **→** select target groups → mobile-TG
    

# **Application Load Balancer**

> What is **Application Load Balancer?**
> 

> An **Application Load Balancer (ALB)** in AWS is a service that helps distribute incoming traffic, like website or app requests, to different servers (such as EC2 instances, containers, or Lambda functions). It works at the application level, meaning it can look at things like the URL or headers and decide where to send the traffic.
> 

# **Step 5 : Create Application Load Balancer**

1. click Load Balancing 
2. Create Application Load Balancer → Click on Create 
- Basic configuration
    - Load balancer name: application-LB
- Network mapping
    - Availability Zones and subnets: select 3 zones
- Security groups
    - Security groups: launch-wizard-1
- Listeners and routing
    - Default action: select home-TG
    - click on create target Group

### **Add listener Rules**

- laptop
    - select rule-1
    - manage rule - add rule →
    - Name: laptop-rule
    - add conditon: click
    - select path  → /laptop/*
    - click confirm
    - action: laptop-TG
    - rule number → 1
    - click on create
    
     
    
- mobile
    - select rule-1
    - manage rule - add rule →
    - Name: mobile-rule
    - add conditon: click
    - select path  → /mobile/*
    - click confirm
    - action: mobile-TG
    - rule number → 2
    - click on create

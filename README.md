# High Availability & Auto Scaling Web Architecture Using Amazon EC2, ELB, and CloudWatch

A fault-tolerant, highly available web application architecture on AWS using **Amazon EC2**, **Application Load Balancer (ALB)**, **Auto Scaling Groups (ASG)**, and **Amazon CloudWatch**. This lab demonstrates dynamic horizontal scaling and automated traffic distribution across multiple Availability Zones in response to CPU utilization spikes.

---

## Architecture Overview

### How It Works
1. **Custom AMI & Launch Template:** A pre-configured web server Amazon Machine Image (AMI) is packaged into an **EC2 Launch Template** so every newly launched instance boots with identical application code, dependencies, and security group rules.
2. **Elastic Load Balancing (ALB):** An **Application Load Balancer** distributes incoming HTTP traffic across healthy EC2 targets registered in a **Target Group** spanning multiple Availability Zones.
3. **Auto Scaling Group (ASG):** An **Auto Scaling Group** maintains a minimum baseline of healthy instances across Availability Zones and automatically replaces any impaired instances.
4. **Dynamic Scaling Policy:** **Amazon CloudWatch** alarms monitor average CPU utilization and trigger **Auto Scaling** policies to scale out (add instances) during high load and scale in (terminate excess instances) once demand subsides.

---

## Repository Structure

```text
Autoscaling-Elastic-load-balancing/
├── images/
│   ├── 1793314823655.jpg
│   ├── 1793314823948.jpg
│   ├── 1793314824055.jpg
│   ├── 1793314824085.jpg
│   ├── 1793314824348.jpg
│   ├── 1793314824420.jpg
│   └── 1793314824732.jpg
└── README.md
```

---

## AWS Services Used

| Service | Component / Configuration | Purpose |
| :--- | :--- | :--- |
| **Amazon EC2** | Custom Web Server AMI & Launch Template | Defines standardized compute instances, security groups, and startup configuration for the web tier. |
| **Elastic Load Balancing** | Application Load Balancer (ALB) & Target Group | Routes incoming HTTP requests evenly across healthy Multi-AZ targets and performs continuous health checks. |
| **EC2 Auto Scaling** | Multi-AZ Auto Scaling Group (ASG) | Automatically launches and terminates EC2 instances to maintain availability and match traffic demand. |
| **Amazon CloudWatch** | CPU Utilization Alarms | Tracks aggregate EC2 CPU metrics and triggers scale-out / scale-in actions. |

---

## Setup & Deployment Walkthrough

### 1. Create a Custom Web Server AMI & Launch Template
1. Configure a baseline EC2 web server instance with the required web application files and HTTP security group rules, then create a custom **Amazon Machine Image (AMI)**.
2. Open the **Amazon EC2 Console** > **Launch Templates** and create a new template referencing the custom AMI, instance type, and web tier security group.

- **Screenshot:** [View 1793314823655.jpg](https://github.com/deeh-tech/Autoscaling-Elastic-load-balancing/blob/main/images/1793314823655.jpg)

### 2. Create a Target Group & Application Load Balancer (ALB)
1. Navigate to **EC2** > **Target Groups** and create an instance-based target group listening on **HTTP Port 80** with health checks enabled.
2. Navigate to **EC2** > **Load Balancers**, create an internet-facing **Application Load Balancer**, select at least two Availability Zones, and forward HTTP port 80 listeners to the target group.

- **Screenshot:** [View 1793314823948.jpg](https://github.com/deeh-tech/Autoscaling-Elastic-load-balancing/blob/main/images/1793314823948.jpg)
- **Screenshot:** [View 1793314824055.jpg](https://github.com/deeh-tech/Autoscaling-Elastic-load-balancing/blob/main/images/1793314824055.jpg)

### 3. Configure the Auto Scaling Group (ASG)
1. Open **EC2** > **Auto Scaling Groups** and create a new ASG using the Launch Template from Step 1.
2. Select multiple subnets across Availability Zones and attach the existing **ALB Target Group**.
3. Set desired, minimum, and maximum capacity limits and enable **ELB health checks**.

- **Screenshot:** [View 1793314824085.jpg](https://github.com/deeh-tech/Autoscaling-Elastic-load-balancing/blob/main/images/1793314824085.jpg)

### 4. Attach CloudWatch Dynamic Scaling Policies
1. Configure a dynamic scaling policy (Target Tracking or Step Scaling) tied to **Average CPU Utilization**.
2. Verify that CloudWatch alarms are created for high-CPU scale-out and low-CPU scale-in thresholds.

- **Screenshot:** [View 1793314824348.jpg](https://github.com/deeh-tech/Autoscaling-Elastic-load-balancing/blob/main/images/1793314824348.jpg)

---

## Load Testing & Verification

### 5. Simulate CPU Stress & Verify Auto Scaling Out
1. Access the application via the **ALB DNS Name** to verify load balancing across healthy instances.
2. Generate synthetic CPU load on the running web instances to breach the CloudWatch alarm threshold.
3. Confirm in **Auto Scaling Group** > **Activity** and **EC2 Instances** that new instances are automatically provisioned and registered as **Healthy** in the Target Group.

- **Screenshot:** [View 1793314824420.jpg](https://github.com/deeh-tech/Autoscaling-Elastic-load-balancing/blob/main/images/1793314824420.jpg)
- **Screenshot:** [View 1793314824732.jpg](https://github.com/deeh-tech/Autoscaling-Elastic-load-balancing/blob/main/images/1793314824732.jpg)

---

## Key Findings & Takeaways

- **Eliminating Single Points of Failure:** Distributing EC2 instances behind an Application Load Balancer across multiple Availability Zones ensures continuous uptime even if an instance or single AZ fails.
- **Elastic Cost & Capacity Management:** Binding Auto Scaling policies to CloudWatch CPU alarms allows infrastructure to expand automatically during peak traffic and scale back down during idle periods.
- **Automated Self-Healing:** Enabling ELB health checks inside the Auto Scaling Group automatically detects unhealthy targets and replaces them without manual intervention.

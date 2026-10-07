# Production-Grade Scalable Web Application Architecture on AWS

## 📌 Project Overview
This project demonstrates a highly available, fault-tolerant, and scalable multi-tier web application architecture deployed on Amazon Web Services (AWS). It adheres to the AWS Well-Architected Framework pillars, specifically operational excellence, security, reliability, and performance efficiency.

## 📐 Architecture Diagram
![AWS Architecture Diagram](./architecture-diagram.png)

## 🏗️ Architecture Highlights
- **Networking & Isolation:** Custom VPC configured across two Availability Zones (AZs) featuring public subnets for edge components, private compute subnets for web servers, and isolated database subnets.
- **Compute & Auto-Scaling:** Auto Scaling Group (ASG) maintaining EC2 instances in private subnets, dynamically scaling based on CPU utilization target tracking policies.
- **Traffic Management & Security:** Application Load Balancer (ALB) distributing incoming HTTP/HTTPS traffic, protected by AWS WAF rules and restricted Security Groups.
- **Edge Acceleration:** Amazon CloudFront distribution serving cached content globally with Amazon Route 53 managing DNS routing.
- **Database Layer:** Multi-AZ Amazon RDS deployment ensuring synchronous data replication and automated failover capabilities.
- **Secure Management:** AWS Systems Manager (SSM) Session Manager for shell access without public SSH ports or bastion hosts.
- **Observability:** Amazon CloudWatch metrics, alarms, and SNS notifications for threshold alerts.

## 🛠️ Deployment Steps
1. **VPC Provisioning:** Create VPC `10.0.0.0/16`, 2 Public Subnets, 4 Private Subnets, Internet Gateway, and NAT Gateways.
2. **Security Groups Setup:** Configure `ALB-SG` (0.0.0.0/80), `EC2-SG` (ingress from `ALB-SG` only), and `RDS-SG` (ingress from `EC2-SG` only).
3. **Database Launch:** Deploy Multi-AZ RDS instance in private DB subnet group.
4. **Compute Layer:**
   - Create IAM Role with `AmazonSSMManagedInstanceCore` policy.
   - Configure Launch Template with user data script and SSM IAM role.
   - Provision ALB with Target Group checking `/index.php`.
   - Create Auto Scaling Group attached to the Target Group across private compute subnets.
5. **Distribution & Monitoring:**
   - Attach CloudFront distribution in front of ALB.
   - Configure CloudWatch CPU utilization alarms with SNS topic subscriptions.

## 🧪 Verification & Testing
- **Load Balancing:** Refreshed web page via ALB DNS to confirm round-robin routing across instance IDs and AZs.
- **Auto Scaling Test:** Executed stress testing on EC2 instances (`stress --cpu 8`) to trigger target tracking scale-out events.
- **Failover Verification:** Simulated RDS primary instance reboot to verify seamless failover to secondary AZ.

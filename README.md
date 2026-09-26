# Highly Available AWS Web Application

## Project Overview

This project demonstrates the design and deployment of a highly available and scalable web application infrastructure on Amazon Web Services (AWS).

The application is being deployed across multiple Availability Zones using public and private subnets. An internet-facing Application Load Balancer distributes incoming HTTP traffic to EC2 web servers hosted in private subnets, providing a secure foundation for high availability and fault tolerance.

## Project Goals

- [x] Build a custom AWS VPC
- [x] Configure public and private subnets across multiple Availability Zones
- [x] Configure route tables and internet connectivity
- [x] Deploy an EC2 web server in a private subnet
- [x] Configure an internet-facing Application Load Balancer
- [x] Configure an ALB target group and health checks
- [x] Apply security groups to control traffic between the ALB and EC2
- [x] Configure temporary NAT Gateway access for EC2 software installation
- [x] Deploy a second EC2 web server in another Availability Zone
- [ x] Implement EC2 Auto Scaling
- [ ] Deploy an Amazon RDS database
- [ ] Configure monitoring with Amazon CloudWatch
- [ x] Test high availability and fault tolerance
- [ x] Complete architecture documentation and diagram

## Architecture
The architecture uses an internet-facing Application Load Balancer (ALB) as the public entry point for the application. The ALB is deployed across public subnets in two Availability Zones and distributes incoming HTTP traffic to healthy EC2 instances through the target group.

The EC2 web servers are deployed in private subnets across two Availability Zones and are managed by an EC2 Auto Scaling Group. The instances do not accept HTTP traffic directly from the internet. Their security group allows HTTP traffic from the ALB security group.

The Auto Scaling Group uses a Launch Template to automatically create EC2 instances with the required configuration. It is configured with a minimum capacity of 2 instances, a desired capacity of 2 instances, and a maximum capacity of 4 instances.

Current traffic flow:

Internet → Internet Gateway → Application Load Balancer → Target Group → Auto Scaling Group → EC2 Web Servers

The architecture was tested for fault tolerance by manually terminating an Auto Scaling-managed EC2 instance. The Auto Scaling Group detected the loss of capacity and automatically launched a replacement instance to restore the desired capacity.
### Architecture Diagram

![AWS Highly Available Web Application Architecture](aws-ha-web-app-architecture.png?raw=1)

## AWS Services

### Currently Implemented

- Amazon VPC
- Amazon EC2
- EC2 Launch Template
- EC2 Auto Scaling
- Application Load Balancer (ALB)
- Elastic Load Balancing Target Groups
- Internet Gateway
- NAT Gateway (temporarily used for EC2 outbound internet access)
- Security Groups
- Route Tables
- Public and Private Subnets across two Availability Zones
- Amazon Machine Image (AMI)

### Planned

- Amazon RDS
- Amazon CloudWatch
- Amazon Route 53
  
## EC2 Image and Auto Scaling Configuration

To automate the deployment of the web servers, an Amazon Machine Image (AMI) was created from a configured EC2 web server. The AMI contains the operating system, Apache web server, and application configuration required to serve the web application.

The AMI was then used in an EC2 Launch Template, which defines the configuration used when new EC2 instances are launched.

The Launch Template is connected to an EC2 Auto Scaling Group that deploys instances across private subnets in two Availability Zones.

The Auto Scaling Group is configured with:

- Minimum capacity: 2
- Desired capacity: 2
- Maximum capacity: 4

This allows the environment to maintain at least two EC2 web servers while providing the ability to scale up to four instances when additional capacity is required.

Deployment flow:

Configured EC2 → AMI → Launch Template → Auto Scaling Group → EC2 Instances

## High Availability and Fault Tolerance Testing

The architecture was tested to verify that the application could remain available when an EC2 instance failed.

An EC2 instance managed by the Auto Scaling Group was manually terminated to simulate an instance failure.

The Auto Scaling Group detected that the number of running instances had fallen below the desired capacity of 2 and automatically launched a replacement EC2 instance using the configured Launch Template.

During the replacement process, the Application Load Balancer continued routing HTTP traffic to healthy targets in the target group.

After the replacement instance passed its health checks, it became available to receive traffic through the load balancer.

The application was tested through the Application Load Balancer DNS name and remained accessible during the recovery process.

Test result:

- EC2 instance manually terminated
- Auto Scaling Group detected the loss of capacity
- Replacement EC2 instance automatically launched
- Desired capacity restored to 2 instances
- Target group returned to 2 healthy targets
- Web application remained accessible through the ALB

This test demonstrated the self-healing and fault-tolerant capabilities of the architecture.

## Project Status

🚧 In Progress

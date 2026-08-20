<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Launching VPC Resource

**Project Link:** [View Project](http://nextwork.ai/projects/aws-networks-ec2)

**Author:** Naga Sri Sai Chaitanya Kolluri  
**Email:** saichaitanya518@gmail.com

---

## Launching VPC Resources

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-ec2_8ee57662)

---

## Introducing Today's Project!

### What is Amazon VPC?

Amazon VPC (Virtual Private Cloud) provides an isolated virtual network environment where I can deploy AWS resources and control how they communicate with each other and with external networks. It allows me to configure components such as subnets, route tables, security groups, and network gateways to control network connectivity and security.

### How I used Amazon VPC in this project

I used Amazon VPC to create an isolated network environment, deploy EC2 instances in public and private subnets, control network communication between them, and securely manage their connectivity to the internet and other resources.

### One thing I didn't expect in this project was...

One thing I didn't expect in this project was how much the Amazon VPC Wizard could simplify VPC creation and configuration. Getting hands-on experience with public and private subnets, route tables, NAT gateways, and EC2 connectivity helped me better understand how AWS networking works in practice.

### This project took me...

This project took me 2hrs

---

## Setting Up Direct VM Access

Directly accessing an EC2 instance allows me to interact with the operating system and perform administrative tasks such as installing software, managing dependencies, uploading files, and modifying configurations.

### SSH is a key method for directly accessing a VM

SSH traffic means network traffic to securly communicate with remote servers with ssh protocal.

### To enable direct access, I set up key pairs

Key pairs are used to securely authenticate and access an EC2 instance without using a password.

A private key file format defines how the private key is stored and encoded. My EC2 private key is in .pem format. PEM originally stood for Privacy-Enhanced Mail, but the format is still widely used today for storing cryptographic keys and certificates, including EC2 key pairs.

---

## Launching a public server

I had to change my EC2 instance's networking settings by assigning it to the appropriate VPC and subnet, because the EC2 instance needs to be placed within the VPC network to communicate with other resources and to control how it accesses the internet.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-ec2_88727bef)

---

## Launching a private server

My private server has its own dedicated security group because it has different network access requirements from the public server. The public server may need to accept traffic from outside the VPC, while the private server should restrict access and primarily allow communication from trusted resources within the VPC, such as the public server.

My private server's security group's source is the public server's security group, which means only traffic originating from instances associated with that security group can reach my private server.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-ec2_4a9e8014)

---

## Speeding up VPC creation

I used an alternative way to set up an Amazon VPC! This time, I used the VPC Wizard to automatically create and configure the required networking components, such as public and private subnets, route tables, and an Internet Gateway, instead of configuring each component manually.

A VPC resource map is a visual representation of the relationships and connections between the resources within a VPC.

My new VPC has a CIDR block of 10.0.0.0/16. It is possible for my new VPC to have the same IPv4 CIDR block as my existing VPC because VPCs are isolated from each other by default. However, overlapping CIDR blocks can cause conflicts if the two VPCs need to communicate directly, such as through VPC peering or certain network connections.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-ec2_1cbb1b88)

---

## Speeding up VPC creation

### Tips for using the VPC resource map

When determining the number of public subnets in my VPC, the VPC Wizard provided two options: 0 or 2. This is because the wizard creates the subnets across multiple Availability Zones to provide redundancy and reduce the risk of a single point of failure. I chose 2 public subnets so that the VPC could use two Availability Zones. The wizard limits this initial setup to 2 for simplicity, but after the VPC is created, I can create additional subnets as needed, subject to AWS limits.

The setup page also offered to create NAT gateways, which are used by resources in private subnets when they need to communicate with the internet. A NAT Gateway allows private instances to make outbound internet connections without requiring public IP addresses, while preventing unsolicited inbound internet connections from reaching those private instances.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-ec2_8ee57662)

---

---

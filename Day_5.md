<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# VPC Traffic Flow and Security

**Project Link:** [View Project](http://nextwork.ai/projects/aws-networks-security)

**Author:** Naga Sri Sai Chaitanya Kolluri  
**Email:** saichaitanya518@gmail.com

---

## VPC Traffic Flow and Security

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-security_92b0b0b4)

---

## Introducing Today's Project!

### What is Amazon VPC?

Amazon VPC is a Virtual Private Cloud that provides a logically isolated network environment in AWS. It is useful because it allows us to control IP addresses, subnets, routing, and network security for the AWS resources we deploy.

### How I used Amazon VPC in this project

In today’s project, I used Amazon VPC to create and configure a secure network environment, including subnets, route tables, security groups, and Network ACLs, and to understand how they control and route network traffic.

### One thing I didn't expect in this project was...

One thing I didn’t expect in this project was how different AWS networking components work together to control and secure network traffic. I learned that route tables handle routing, security groups control traffic at the resource level, and Network ACLs control traffic at the subnet level.

### This project took me...

This project took me 2 hrs

---

## Route tables

Route tables are tables of rules, called routes, that determine where network traffic should go.

Route tables are needed to make a subnet public because the Internet Gateway is attached to the VPC, not directly to the subnet. We configure a route in the route table to send internet-bound traffic to the Internet Gateway, thereby providing internet access to resources in the subnet.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-security_0a07b191)

---

## Route destination and target

Routes are defined by two main components: destination and target.

Destination: The range of IP addresses where the network traffic should go.
Target: The next destination or path through which the traffic should be sent.

For example, 0.0.0.0/0 → Internet Gateway means send all IPv4 traffic to the Internet Gateway.

The route in my route table that directs internet-bound traffic to my Internet Gateway has a destination of 0.0.0.0/0 and the Internet Gateway as its target.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-security_0a07b191)

---

## Security groups

Security groups are virtual firewalls that control inbound and outbound traffic for AWS resources, such as EC2 instances. They operate at the resource level and are stateful, meaning return traffic is automatically allowed.

### Inbound vs Outbound rules

Inbound rules control the traffic that is allowed to enter a resource. I configured an inbound rule that allows specific incoming traffic to reach my resource from a defined source.

Outbound rules control the traffic that is allowed to leave a resource. By default, my security group’s outbound rule allows all outbound traffic to any destination.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-security_92b0b0b4)

---

## Network ACLs

Network ACLs are a security layer that filters inbound and outbound traffic to and from a particular subnet in a VPC.

### Security groups vs. network ACLs

The difference between a Security Group and a Network ACL is that a Security Group acts as a traffic filter for specific resources in a subnet, controlling inbound and outbound traffic. A Network ACL acts as a traffic filter for an entire subnet in a VPC, controlling inbound and outbound traffic.

---

## Default vs Custom Network ACLs

### Similar to security groups, network ACLs use inbound and outbound rules

By default, a custom Network ACL has inbound and outbound rules that allow all traffic. The default NACL for a VPC also allows all inbound and outbound traffic by default.

In contrast, a custom Network ACL’s inbound and outbound rules are automatically set to deny all traffic by default.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-security_4faeb056)

---

## Tracking VPC Resources



---

---

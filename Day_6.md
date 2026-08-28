<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Creating a Private Subnet

**Project Link:** [View Project](http://nextwork.ai/projects/aws-networks-private)

**Author:** Naga Sri Sai Chaitanya Kolluri  
**Email:** saichaitanya518@gmail.com

---

## Creating a Private Subnet

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-private_afe1fdbd)

---

## Introducing Today's Project!

### What is Amazon VPC?

Amazon VPC (Virtual Private Cloud) provides a logically isolated network space for AWS resources. It helps organize and control resources while providing network security by controlling how they communicate with the internet and other networks.

### How I used Amazon VPC in this project

In today’s project, I used Amazon VPC to create and configure public and private subnets, route tables, an Internet Gateway, and Network ACLs. I learned how these components work together to control network traffic and provide secure connectivity for AWS resources.

### One thing I didn't expect in this project was...

One thing I didn’t expect in this project is how many different components, such as subnets, route tables, Internet Gateways, and Network ACLs, work together to control network traffic within a VPC.

### This project took me...

This project took me 40 min's

---

## Private vs Public Subnets

The main difference between public and private subnets is their internet connectivity. Resources in both subnets can communicate within the VPC, but resources in a public subnet can also communicate with the internet through an Internet Gateway, while resources in a private subnet do not have a direct route to the internet.

Private subnets are useful for deploying databases and other resources that need to be isolated from direct internet access.

My private and public subnets cannot have the same CIDR block because each subnet must have a unique IP address range within the VPC. Overlapping CIDR blocks would cause routing conflicts and make it impossible to properly determine where network traffic should go.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-private_afe1fdbd)

---

## A dedicated route table

By default, my private subnet is associated with a route table that allows communication within the VPC.

I had to set up a new route table because I wanted to create a separate routing configuration for my private subnet and control how traffic is routed within the VPC.

My private subnet’s dedicated route table has a local route that allows inbound and outbound communication within the VPC, but it does not have a route to an Internet Gateway, keeping the subnet private.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-private_b4b904b5)

---

## A new network ACL

By default, my private subnet is associated with the default Network ACL, which allows all inbound and outbound traffic by default.

I set up a dedicated Network ACL for my private subnet to customize and control inbound and outbound network traffic within the VPC.

My new Network ACL has two simple rules: one to control inbound traffic and another to control outbound traffic for the private subnet.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-private_1ed2cb07)

---

---

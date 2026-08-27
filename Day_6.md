<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# VPC Peering

**Project Link:** [View Project](http://nextwork.ai/projects/aws-networks-peering)

**Author:** Naga Sri Sai Chaitanya Kolluri  
**Email:** saichaitanya518@gmail.com

---

## VPC Peering

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-peering_88727bef)

---

## Introducing Today's Project!

### What is Amazon VPC?

Amazon VPC (Virtual Private Cloud) is a logically isolated network in AWS where we can deploy and manage resources securely. It is useful because we can control how resources communicate with each other and with the outside internet using components like route tables, security groups, and internet gateways.

### How I used Amazon VPC in this project

In today’s project, I used Amazon VPC to create two isolated networks, connect them using VPC Peering, and enable communication between EC2 instances in different VPCs.

### One thing I didn't expect in this project was...

One thing I didn’t expect in this project was encountering a routing error when I accidentally added 10.1.0.0/16 to VPC2’s route table instead of 10.2.0.0/16. I realized that 10.1.0.0/16 belongs to VPC1, corrected the route, and resolved the issue. I also learned that AWS allows VPCs to be created with overlapping CIDR blocks, but overlapping CIDRs can cause routing and communication problems when connecting the VPCs.

### This project took me...

This project took me 2hrs

---

## In the first part of my project...

### Step 1 - Set up my VPC

In this step, I will create two VPCs because I want to learn how two separate networks can communicate with each other.

### Step 2 - Create a Peering Connection

In this step, I will set up a connection link between the two VPCs because it will allow network traffic to flow and enable communication between them.

### Step 3 - Update Route Tables

In this step, I will set up routes for traffic between VPC1 and VPC2 because the VPCs need routing rules to know where to send traffic through the peering connection.

### Step 4 - Launch EC2 Instances

In this step, I will launch a new EC2 instance in VPC1 because I will use it later to test communication between VPC1 and VPC2.

---

## Multi-VPC Architecture

I started my project by launching two Vpc's, with one public subnet foreach with internet gateway.

The CIDR blocks for VPC 1 and VPC 2 are 10.1.0.0/16 and 10.2.0.0/16. They must be unique and non-overlapping because overlapping CIDR blocks can cause routing conflicts and prevent proper communication between the VPCs.

### I also launched 2 EC2 instances

I didn't set up key pairs for these EC2 instances because I will use EC2 Instance Connect to access them instead of SSH using a key pair.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-peering_11111111)

---

## VPC Peering

A VPC peering connection is used to establish a private network connection between two VPCs, allowing them to communicate with each other.

VPCs can use a VPC peering connection to establish a private connection between them. Instead of sending traffic through the public internet, the peering connection allows the VPCs to communicate privately using AWS's network.

The difference between a Requester and an Accepter in a VPC peering connection is that the Requester initiates the peering connection, while the Accepter accepts the request to establish the connection.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-peering_1cbb1b88)

---

## Updating route tables

After accepting the peering connection, I need to update the route tables of both VPCs because each VPC needs a route to send traffic to the other VPC through the peering connection.

My VPCs' new routes have destinations of 10.1.0.0/16 and 10.2.0.0/16, respectively. The target for both routes is the VPC peering connection, which allows traffic to flow between the two VPCs.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-peering_4a9e8014)

---

## In the second part of my project...

### Step 5 - Use EC2 Instance Connect

In this step, I will use ec2 first instance to connect using ec2 instance connect. because to test the connectivity.

### Step 6 - Connect to EC2 Instance 1

In this step, I will try to connect with ec2 instance connect. because, to test connectivity btw ec2 and internet gateway.

### Step 7 - Test VPC Peering

In this step, I will send a message from Instance 1 to Instance 2 and verify whether Instance 2 receives it. I’m doing this to get hands-on experience with communication between EC2 instances in two different VPCs.

---

## Troubleshooting Instance Connect

Next, I used EC2 Instance Connect to access the EC2 instance and test its connectivity to the internet through the Internet Gateway.

I was unable to use EC2 Instance Connect because I had disabled Auto-assign Public IP when setting up the EC2 instance. Since the instance did not have a public IPv4 address, I encountered a “No public IPv4 address assigned” error.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-peering_7685490c)

---

## Elastic IP addresses

To resolve this error, I assigned an Elastic IP address to the EC2 instance. An Elastic IP is a static public IPv4 address that remains associated with the instance even when it is stopped and restarted.

Associating an Elastic IP address resolved the error because it assigned a static public IPv4 address to the EC2 instance, allowing me to connect to it using EC2 Instance Connect.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-peering_45663498)

---

## Troubleshooting ping issues

To test VPC peering, I ran the command ping 10.2.12.145

A successful ping test would validate my VPC peering connection because it confirms that network traffic can successfully travel between EC2 instances in the two different VPCs.

I had to update the security group of my second EC2 instance because it was not allowing ICMP traffic. I added a new rule to allow All ICMP - IPv4 traffic from VPC1’s CIDR block.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-peering_7a29d352)

---

---

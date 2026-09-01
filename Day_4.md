<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Build a Virtual Private Cloud

**Project Link:** [View Project](http://nextwork.ai/projects/aws-networks-vpc)

**Author:** Naga Sri Sai Chaitanya Kolluri  
**Email:** saichaitanya518@gmail.com

---

## Build a Virtual Private Cloud (VPC)

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-vpc_2facf927)

---

## Introducing Today's Project!

In this project, I will demonstrate Amazon VPC with subnet and internet gateway. I'm doing this project to learn Amazon VPC, create subnet and internet gateway

### What is Amazon VPC?

### Personal reflection

---

## Virtual Private Clouds (VPCs)

### What I did in this step

In this step, I will create VPC because, need a private network

### How VPCs work

A VPC is a private, isolated network space provided by AWS where you can create and use resources such as EC2 instances, databases, and other services.

### Why there is a default VPC in AWS accounts

AWS automatically creates a default VPC when an AWS account is created. It provides a ready-to-use network environment where resources such as EC2 instances and databases can be launched and communicate with each other.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-vpc_2facf927)

### Defining IPv4 CIDR blocks

To set up my VPC, I had to define an IPv4 CIDR block, which is 10.0.0.0/16

---

## Subnets

### What I did in this step

In this step, I will create subnets inside the VPC because we need to organize and operate different resources within separate network spaces.

### Creating and configuring subnets

Subnets are smaller, specific network spaces created inside a VPC. They can be public or private. In my AWS account, the default VPC already has subnets created, typically one subnet in each Availability Zone.

### Public vs private subnets

The difference between public and private subnets is based on their network connectivity. Resources that need to be directly accessible from the internet, such as web servers, can be placed in a public subnet. Resources that should not be directly accessible from the internet, such as databases, are usually placed in private subnets. For a subnet to be considered public, its route table must have a route to an Internet Gateway.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-vpc_157c4219)

### Auto-assigning public IPv4 addresses

Once I created my subnet, I enabled Auto-assign public IPv4 address. This setting automatically assigns a public IPv4 address to an EC2 instance when it is launched in this subnet, allowing the instance to communicate with the internet, provided the subnet has a route to an Internet Gateway.

---

## Internet gateways

### What I did in this step

In this step, I will create an Internet Gateway and attach it to the VPC. This allows resources in the subnet to access the internet and, with the appropriate route table configuration, makes the subnet public.

### Setting up internet gateways

An Internet Gateway is a key component that allows resources in a VPC to communicate with the internet. It is attached to the VPC and, through the appropriate route table, provides internet connectivity to resources in public subnets.

ttaching an Internet Gateway to a VPC provides a path for resources in the VPC to communicate with the public internet. If I skip this step, the subnets in the VPC cannot have internet connectivity through an Internet Gateway, so they cannot function as public subnets.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-vpc_4ae90410)

---

## Using the AWS CLI

### What I'm doing in this extension

In this project extension, I will create a VPC, a subnet, and an Internet Gateway, and attach the Internet Gateway to the VPC using AWS CloudShell. I am doing this to gain hands-on knowledge of creating AWS networking resources through the command line and to see how quickly these resources can be created.

### Exploring CloudShell and CLI

VPC resources can also be created using AWS CloudShell, which provides a command-line interface (CLI) to interact with AWS services using commands instead of the AWS Management Console.

### Debugging my setup

To set up a VPC or a subnet, you can use AWS CLI commands to create them. Make sure to specify the correct CIDR block in the commands to avoid configuration errors.

### Comparing CloudShell vs AWS Console

---

---

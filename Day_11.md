<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Access S3 from a VPC

**Project Link:** [View Project](http://nextwork.ai/projects/aws-networks-s3)

**Author:** Naga Sri Sai Chaitanya Kolluri  
**Email:** saichaitanya518@gmail.com

---

## Access S3 from a VPC

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-s3_3e1e79a2)

---

## Introducing Today's Project!

### What is Amazon VPC?

Amazon VPC is a logically isolated virtual network in AWS where we can launch and manage resources such as EC2 instances. It is useful because it provides control over networking, including IP addresses, subnets, route tables, and security, allowing resources to communicate securely with each other and with AWS services.

### How I used Amazon VPC in this project

In today’s project, I used Amazon VPC to create a network environment for my EC2 instance, connect to the instance using EC2 Instance Connect, configure AWS authentication, and interact with an S3 bucket using the AWS CLI.

### One thing I didn't expect in this project was...

One thing I didn't expect in this project was that configuring AWS credentials on the EC2 instance was necessary before I could use the AWS CLI to interact with my S3 bucket. This helped me understand how AWS authentication and permissions control access to AWS services.

### This project took me...

This project took me 2hrs

---

## In the first part of my project...

### Step 1 - Architecture set up

In this step, I will create a VPC from scratch and set up an EC2 instance inside it because I want to understand how EC2 instances are deployed within a VPC and how the VPC provides the networking environment for the instance.

### Step 2 - Connect to my EC2 instance

In this step, I will connect to the EC2 instance using EC2 Instance Connect because I want to securely access the instance through a browser-based SSH connection without manually managing an SSH key on my local machine.

### Step 3 - Set up access keys

In this step, I will give the EC2 instance access to the AWS environment because I want the instance to securely interact with AWS services such as S3 without storing long-term AWS access keys on the instance.

---

## Architecture set up

I started my project by launching Ec2 instance.

I also set up an S3 bucket with two files in AWS, which I will use to test S3 operations from my EC2 instance.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-s3_4334d777)

---

## Running CLI commands

AWS CLI is a command-line tool that allows us to interact with and manage AWS services using commands in a terminal.
I have access to the AWS CLI because it is installed and configured on my EC2 instance, allowing me to authenticate with AWS and perform actions on services such as S3.

The first command I ran was aws s3 ls. This command is used to list all the S3 buckets available to the authenticated AWS account.

The second command I ran was aws configure. This command is used to configure the AWS CLI with credentials and default settings, such as the AWS Access Key ID, Secret Access Key, default region, and output format.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-s3_e7fa8776)

---

## Access keys

### Credentials

To set up my EC2 instance to interact with my AWS environment, I configured AWS access credentials and the AWS CLI, which allow the instance to authenticate with AWS and interact with services such as S3.

Access keys are credentials used by the AWS CLI, applications, or other tools to authenticate and make API requests to AWS services.

They consist of an Access Key ID and a Secret Access Key.

The secret access key is like the password that pairs with your access key ID (your username). You need both to access AWS services.

### Best practice

Although I'm using access keys in this project, a best practice alternative is to use aws cloudshell, a browser-based CLI, to run commands, use aws cli and enable authentication through a user in IAM Identity center.

---

## In the second part of my project...

### Step 4 - Set up an S3 bucket

In this step, I will create an S3 bucket and upload files to it because I want to have data that my EC2 instance can access and interact with using the AWS CLI.

### Step 5 - Connecting to my S3 bucket

In this step, I will get the EC2 instance to interact with the S3 bucket because I want to verify that the EC2 instance has the required permissions to access and manage objects stored in S3 using the AWS CLI.

---

## Connecting to my S3 bucket

The first command I ran was aws s3 ls. This command is used to list all the S3 buckets available to the authenticated AWS account.

When I ran the command aws s3 ls again, the terminal responded with the name of my S3 bucket, nextwork-vpc-project-naga. This indicated that my EC2 instance was successfully authenticated with AWS and had permission to access and list my S3 buckets.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-s3_4334d778)

---

## Connecting to my S3 bucket

Another CLI command I ran was aws s3 ls s3://nextwok-vpc-project-naga which returned list of files in the s3 bucket: 
NextWork - Denzel is awesome.png
NextWork - Lelo is awesome.png

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-s3_4334d779)

---

## Uploading objects to S3

To upload a new file to my bucket, I first ran the command sudo touch /tmp/test.txt. This command creates empty file in ec2.

The second command I ran was aws s3 cp /tmp/test.txt s3://nextwork-vpc-project-naga This command will copy the file from ec2 to s3 bucket.

The third command I ran was aws s3 ls s3://nextwork-vpc-project-naga. which validated that 3 files listed, one of them is copied file to s3 bucket.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-s3_3e1e79a2)

---

---

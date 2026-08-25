<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# VPC Endpoints

**Project Link:** [View Project](http://nextwork.ai/projects/aws-networks-endpoints)

**Author:** Naga Sri Sai Chaitanya Kolluri  
**Email:** saichaitanya518@gmail.com

---

## VPC Endpoints

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-endpoints_09bcaa8a)

---

## Introducing Today's Project!

### What is Amazon VPC?

Amazon VPC is a virtual network that allows me to securely run and manage AWS resources. It is useful because it gives me control over network traffic, connectivity, subnets, routing, and security.

### How I used Amazon VPC in this project

In today's project, I used Amazon VPC to securely connect an EC2 instance to an S3 bucket through a VPC endpoint and control access using endpoint and bucket policies.

### One thing I didn't expect in this project was...

One thing I didn't expect in this project was that changing the endpoint policy to Deny immediately blocked the EC2 instance from accessing the S3 bucket.

### This project took me...

This project took me 1 hour and 50 minutes to complete.

---

## In the first part of my project...

### Step 1 - Architecture set up

In this step, I will create a VPC, launch an EC2 instance, and set up an S3 bucket because these resources are required to demonstrate secure private access to S3 using a VPC endpoint.

### Step 2 - Connect to EC2 instance

In this step, I will connect to the EC2 instance because I need to use it to access and test connectivity to the S3 bucket.

### Step 3 - Set up access keys

In this step, I will create access keys because they are used as credentials to authenticate the EC2 instance and access AWS services.

### Step 4 - Interact with S3 bucket

In this step, I will allow the EC2 instance to access the S3 bucket because I need to test whether the EC2 instance can securely access S3 through the VPC endpoint.

---

## Architecture set up

I started my project by creating a VPC and launching an EC2 instance to set up the network environment and test connectivity to S3.

I also set up s3 bucket and uploaded files in it.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-endpoints_4334d777)

---

## Access keys

### Credentials

To set up my EC2 instance to interact with my AWS environment, I configured the AWS CLI with my access keys.

Access keys are credentials for application and other servers to log into aws and talk to AWS services/resources.

Secret access keys are like the passwords that pairs with your access key ID 's (your username). You need both to access AWS services.

### Best practice

Although I'm using access keys in this project, a best practice alternative is to use an IAM role attached to the EC2 instance, because it provides temporary credentials without storing access keys on the instance.

---

## Connecting to my S3 bucket

The command I ran was aws s3 ls. This command is used to list all the S3 buckets associated with my AWS account.

The terminal responded with the S3 buckets associated with my AWS account. This indicated that the access keys were configured successfully and the EC2 instance was able to authenticate with AWS.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-endpoints_4334d778)

---

## Connecting to my S3 bucket

I also tested the command aws s3 ls s3://nextwork-vpc-project-naga. which returned, files present in specified s3 bucket.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-endpoints_4334d779)

---

## Uploading objects to S3

To upload a new file to my bucket, I first ran the command sudo touch /tmp/nextwork.txt This command creates empty file in tmp folder in ec2.

The second command I ran was aws s3 cp /tmp/nextwork.txt s3://nextwork-vpc-project-naga. This command will copy the file from ec2 to s3 bucket.

The third command I ran was aws s3 ls s3://nextwork-vpc-project-naga. This command validated that the file was uploaded successfully by displaying the files, including the uploaded file, in the specified S3 bucket.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-endpoints_3e1e79a2)

---

## In the second part of my project...

### Step 5 - Set up a Gateway

In this step, I will set up a VPC endpoint because it provides a secure way for resources in my VPC to communicate directly with S3 without routing traffic through the public internet.

### Step 6 - Bucket policies

In your own words, start this step by explaining what you're about to do.

### Step 7 - Update route tables

In this step, I will test the VPC endpoint because I want to verify that the EC2 instance can securely access the S3 bucket through the VPC endpoint.

### Step 8 - Validate endpoint conection

In this step, I will test the VPC endpoint because I want to verify that the EC2 instance can access the S3 bucket through the VPC endpoint and that the bucket policy allows only private access through the endpoint.

---

## Setting up a Gateway

I set up an S3 Gateway endpoint, which is a VPC endpoint that provides private access from resources in a VPC to Amazon S3 without using the public internet.

### What are endpoints?

An endpoint is a private connection point that allows resources in a VPC to access AWS services without going through the public internet.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-endpoints_09bcaa8a)

---

## Bucket policies

A bucket policy is a resource-based policy that defines who can access an S3 bucket and what actions they are allowed to perform.

My bucket policy will deny all S3 actions unless the request comes through my specified VPC endpoint (vpce-01e31c6ae4335d5a1). This ensures that the S3 bucket can only be accessed through the VPC endpoint.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-endpoints_7316a13d)

---

## Bucket policies

Right after saving my bucket policy, my S3 bucket page showed “Access Denied” warnings. This was because the policy denies all S3 requests that do not come through the specified VPC endpoint, including requests made from outside the VPC endpoint.

I also had to update my route table because it needs a route to the S3 Gateway VPC endpoint so that traffic from the VPC can reach the S3 bucket privately.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-endpoints_4ec7821f)

---

## Route table updates

To update my route table, I associated the S3 Gateway VPC endpoint with the route table used by my EC2 instance, so that S3 traffic is routed through the endpoint.

After updating my public subnet's route table, my terminal could successfully return the S3 bucket contents, confirming that the EC2 instance was able to access S3 through the VPC endpoint.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-endpoints_d116818e)

---

## Endpoint policies

An endpoint policy is a policy that controls which AWS resources and actions can be accessed through a VPC endpoint.

I updated my endpoint policy by setting the Effect to Deny. I could see the effect immediately because I was no longer able to access the S3 bucket through the VPC endpoint.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-endpoints_3e1e79a3)

---

---

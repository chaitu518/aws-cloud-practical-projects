<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Testing VPC Connectivity

**Project Link:** [View Project](http://nextwork.ai/projects/aws-networks-connectivity)

**Author:** Naga Sri Sai Chaitanya Kolluri  
**Email:** saichaitanya518@gmail.com

---

## Testing VPC Connectivity

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-connectivity_8ee57662)

---

## Introducing Today's Project!

### What is Amazon VPC?

Amazon VPC (Virtual Private Cloud) is a logically isolated network in AWS where you can launch and manage resources like EC2 instances.
It is useful because it gives you control over networking, security, subnets, routing, and internet connectivity.

### How I used Amazon VPC in this project

In today’s project, I used Amazon VPC to test communication between public and private EC2 servers, verify internet connectivity from the public server, and connect to the EC2 instance through the command line.

### One thing I didn't expect in this project was...

One thing I didn’t expect in this project was encountering connectivity issues due to the security group rules, which helped me understand how AWS controls network access.

### This project took me...

This project took me 1 hr

---

## Connecting to an EC2 Instance

Connectivity means the ability to establish a connection between two systems or resources so they can communicate with each other.

My first connectivity test was whether I could connect to public server ec2

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-connectivity_88727bef)

---

## EC2 Instance Connect

I connected to my EC2 instance using EC2 Instance Connect, which allows me to securely access and communicate with my EC2 instance through a command-line terminal.

My first attempt to access the public server directly resulted in an error because the security group associated with my EC2 instance did not have an inbound SSH rule. I added an inbound rule for SSH (TCP port 22) to allow the connection

I fixed this error by adding an inbound SSH rule (TCP port 22) to the security group associated with my EC2 instance, allowing SSH connections to the public server.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-connectivity_1cbb1b88)

---

## Connectivity Between Servers

Ping is used to test whether communication is working between two servers by checking their network connectivity. I used ping to test the connectivity between the public server and the private server.

The ping command I ran was ping 10.0.1.33

The first ping command returned “PING 10.0.1.33 (10.0.1.33) 56(84) bytes of data”, but there was no response from the private server. This indicated that there was a connectivity issue between the public server and the private server.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-connectivity_defghijk)

---

## Troubleshooting Connectivity

I troubleshooted this issue by checking the Network ACL and security group rules for the private server. I found that ICMP traffic was not being allowed. I added All ICMP rules for both inbound and outbound traffic, allowing communication from the public server. After updating these rules, the private server started responding to the ping requests.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-connectivity_4a9e8014)

---

## Connectivity to the Internet

curl is a command-line tool used to test or transfer data over a network using protocols such as HTTP, HTTPS, FTP.

I used curl to test the connectivity between the public server and an external internet website.

### Ping vs Curl

Ping checks basic network reachability using ICMP, whereas curl tests application-level connectivity by communicating with services such as HTTP or HTTPS.

---

## Connectivity to the Internet

I ran the curl example.com command, which successfully returned the HTTP response data, confirming that the public server has internet connectivity.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-networks-connectivity_8ee57662)

---

---

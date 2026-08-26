<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Aurora Database with EC2

**Project Link:** [View Project](http://nextwork.ai/projects/aws-databases-aurora)

**Author:** Naga Sri Sai Chaitanya Kolluri  
**Email:** saichaitanya518@gmail.com

---

## Connect a Web App to Amazon Aurora

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-databases-aurora_44443546)

---

## Introducing Today's Project!

### What is Amazon Aurora?

Amazon Aurora is a fully managed, MySQL- and PostgreSQL-compatible relational database service provided by AWS, and it is useful because it offers high performance, high availability, automatic backups, fault tolerance, and scalability for applications.

### How I used Amazon Aurora in this project

In today’s project, I used Amazon Aurora to create a highly available relational database and connect it to an EC2 instance hosting a web application.

### One thing I didn't expect in this project was...

One thing I didn’t expect in this project was that I needed to create the EC2 instance before creating the Aurora database so that I could select the EC2 instance under the compute resource configuration.

### This project took me...

It took me 1 hour to complete this project.

---

## In the first part of my project...

### Creating an Aurora Cluster

A relational database is a type of database that organizes data into tables, which are collections of rows and columns. Kind of like a spreadhsheet! We call it "relational" because the rows relate to the columns and vice-versa.



Aurora is a good choice when I need a high-performance, highly available, and scalable relational database for an application, especially when the workload requires automatic scaling, backups, and fault tolerance.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-databases-aurora_44443546)

---

## Halfway through I stopped!

I stopped creating my Aurora database because, during the Aurora database creation process, we need to select “Connect to an EC2 compute resource” under the Compute Resource configuration. However, I could not see any EC2 instances in the dropdown because I had not created one yet.

### Features of my EC2 instance

I created a new key pair for my EC2 instance because, the keys to access our EC2 instance. We need keys to our EC2 instance if we want to add, change, or update how our EC2 instance is running.

When I created my EC2 instance, I took particular note of public IPv4 DNS and key pair name.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-databases-aurora_91b9fd1g)

---

## Then I could finish setting up my database

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-databases-aurora_1fddb0b5)

Aurora Database uses clusters because they provide high availability, fault tolerance, scalability, and efficient data management by allowing multiple database instances to work with shared storage.

---

---

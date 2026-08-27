<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Connect a Web App with Aurora

**Project Link:** [View Project](http://nextwork.ai/projects/aws-databases-webapp)

**Author:** Naga Sri Sai Chaitanya Kolluri  
**Email:** saichaitanya518@gmail.com

---

## Connect a Web App to Amazon Aurora

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-databases-webapp_1709b26b)

---

## Introducing Today's Project!

### What is Amazon Aurora?

Amazon Aurora is a fully managed, MySQL- and PostgreSQL-compatible relational database engine built by AWS, and it is useful because it provides high performance, scalability, high availability, and automated database management without requiring you to manage the underlying infrastructure.

### How I used Amazon Aurora in this project

In today’s project, I used Amazon Aurora to store and manage data for my web application and connected it with an EC2-hosted PHP web app so that data entered through the website could be stored and retrieved from the database.

### One thing I didn't expect in this project was...

One thing I didn't expect in this project was how many configuration steps were required to connect the web application to Aurora, especially setting up the security groups, database connection, and verifying the data through the MySQL CLI.

### This project took me...

I took 2 hrs to complete this project.

---

## Creating a Web App

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-databases-webapp_b7999168)

To connect to my EC2 instance, I changed the permission of the PEM file to 400 and ran' ssh -i nextworkaurorakey'.pem ec2-user@16.176.194.96

To help me create my web app, I first installed httpd, PHP, php-mysqli, and MariaDB 10.5 because these packages provide the web server, PHP runtime, MySQL/MariaDB database connectivity, and database server components needed to build and run my web application on the EC2 instance.

---

## Connecting my Web App to Aurora

I set up my EC2 instance’s connection details to my Aurora database by creating a dbinfo.inc file and adding the required database details because this file stores the database connection information, such as the database endpoint, username, password, and database name, allowing my PHP application to securely connect to the Aurora database.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-databases-webapp_1709b25b)

---

## My Web App Upgrade

Next, I upgraded my web app by connecting it to the Amazon Aurora MySQL database, allowing the application to store and retrieve data dynamically instead of displaying only static content.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-databases-webapp_2709b25b)

---

## Testing my Web App

To make sure my web app was working correctly, I added data through the web app and verified that it was reflected on the page. I also installed the MySQL client on the EC2 instance, connected to the Aurora database, and confirmed that the data was successfully added to the SQL table.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-databases-webapp_1409z22b)

---

---

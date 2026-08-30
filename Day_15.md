<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Load Data into DynamoDB

**Project Link:** [View Project](http://nextwork.ai/projects/aws-databases-dynamodb)

**Author:** Naga Sri Sai Chaitanya Kolluri  
**Email:** saichaitanya518@gmail.com

---

## Load Data into a DynamoDB Table

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-databases-dynamodb_b481c730)

---

## Introducing Today's Project!

### What is Amazon DynamoDB?

Amazon DynamoDB is a fully managed NoSQL database service provided by AWS. It stores data as items and attributes and supports key-value and document data models.

### How I used Amazon DynamoDB in this project

In today's project, I used Amazon DynamoDB to create tables and manage their data. I also used the AWS CLI to create DynamoDB tables and load data into them using batch write operations.


### One thing I didn't expect in this project was...

One thing I didn't expect in this project is how flexible DynamoDB is, especially the ability for different items in the same table to have different attributes without requiring a fixed schema.

### This project took me...

This project took me 1hr

---

## Create a DynamoDB table

DynamoDB tables organize data using items and attributes, with a primary key used to uniquely identify each item and efficiently retrieve data.

An attribute is a piece of data about an item. In my case item is Naga and attribute is the number of projects Naga Completed.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-databases-dynamodb_a3cefee0)

---

## Read and Write Capacity

Read Capacity Units (RCUs) and Write Capacity Units (WCUs) are units used by Amazon DynamoDB to measure and manage the database’s read and write throughput.

RCU → measures how much data DynamoDB can read per second.
WCU → measures how much data DynamoDB can write per second.

In simple terms: RCUs = reading capacity, WCUs = writing capacity.

Amazon DynamoDB's Free Tier covers 25 GB of storage, 25 RCUs, and 25 WCUs per month for eligible usage.

I turned off auto scaling because this is a learning project with low and predictable traffic, so fixed capacity is sufficient and helps avoid unnecessary usage or charges from scaling beyond what I need.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-databases-dynamodb_ef47dd8f)

---

## Using CLI and CloudShell

AWS CloudShell is AWS CloudShell is shell in your AWS Management Console, which means it's a space for you to run code! The awesome thing about AWS CloudShell is that it already has AWS CLI pre-installed.

AWS CLI is software, let you create, update and delete aws resources through commands, instead of clicking through console

I ran CLI commands in AWS CloudShell to create four DynamoDB tables — Comment, ContentCatalog, Forum, and Post — with their respective attributes and primary key configurations.


![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-databases-dynamodb_81e0258b)

---

## Loading Data with CLI

I ran AWS CLI commands in AWS CloudShell using `aws dynamodb batch-write-item` to load data from the `ContentCatalog.json`, `Forum.json`, `Post.json`, and `Comment.json` files into their respective DynamoDB tables.


![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-databases-dynamodb_791c600b)

---

## Observing Item Attributes

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-databases-dynamodb_b481c731)

I checked a ContentCatalog item, which had the following attributes: Id as the partition key, along with a list and several additional attributes containing string, number, and Boolean values. This shows that DynamoDB items can contain different types of attributes within the same table.

I checked another ContentCatalog item, which had a different set of attributes, demonstrating that DynamoDB is flexible and allows different items in the same table to have different attributes.

---

## Benefits of DynamoDB

A benefit of DynamoDB over relational databases is flexibility, because DynamoDB is schemaless, allowing different items in the same table to have different attributes without requiring a fixed schema.

Another benefit over relational databases is speed, because DynamoDB automatically partitions data across multiple partitions, allowing it to distribute the workload and provide fast access to data.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-databases-dynamodb_b481c730)

---

---

<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Query Data with DynamoDB

**Project Link:** [View Project](http://nextwork.ai/projects/aws-databases-query)

**Author:** Naga Sri Sai Chaitanya Kolluri  
**Email:** saichaitanya518@gmail.com

---

## Query Data with DynamoDB

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-databases-query_733d9399)

---

## Introducing Today's Project!

### What is Amazon DynamoDB?

Amazon DynamoDB is a fully managed NoSQL database service provided by AWS. It stores data as items in tables and uses partition keys and optional sort keys to efficiently organize and retrieve data.

It is useful because:

Fast performance: It provides low-latency data access at scale.
Flexible data model: Items can have different attributes without requiring a fixed relational schema.
Scalability: DynamoDB can automatically scale to handle large amounts of traffic and data.
Serverless: AWS manages the underlying infrastructure, so there is no database server to maintain.
Efficient querying: Data can be retrieved using partition and sort keys.
Transactions: Related operations across multiple tables can be performed atomically, ensuring data remains consistent.

### How I used Amazon DynamoDB in this project

In today’s project, I used Amazon DynamoDB to create tables, load and update data, query items using partition and sort keys through the AWS Console and CloudShell, and perform transactions that updated related data across two tables atomically.


### One thing I didn't expect in this project was...

One thing I didn’t expect in this project is how important data modelling is in DynamoDB. I learned that choosing the right partition and sort keys is essential because they determine how easily and efficiently data can be queried.


### This project took me...

This project took me 1 hr 30 mins

---

## Querying DynamoDB Tables

A partition key is an attribute that DynamoDB uses to determine where an item is stored in the table. It uniquely identifies an item when used alone, or groups related items together when used with a sort key.

A sort key is an attribute that DynamoDB uses to organize and order items that share the same partition key. It allows you to store multiple related items under the same partition key and efficiently query them based on the sort key.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-databases-query_d105b0b0)

---

## Limits of Using DynamoDB

I ran into an error when I queried for data without using the `Id` partition key. This was because DynamoDB requires the partition key to be specified when using the `Query` operation. This taught me that data modelling is important in DynamoDB because the partition and sort keys should be designed based on the queries the application needs to perform.


Insights we could extract from our Comment table include:

* The comments posted for a specific topic or discussion.
* The date and time each comment was posted.
* The message content of each comment.
* The user who posted each comment.
* The number and order of comments for a specific `Id` using the partition key and sort key.

Insights we can't easily extract from the Comment table include:

* All comments posted by a particular user across different topics.
* All comments within a specific date range across the entire table.
* Aggregated information, such as the total number of comments per user or per topic, without additional queries or processing.
* Data based only on attributes that are not part of the table's key structure.


![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-databases-query_cb3e260c)

---

## Running Queries with CLI

A query I ran in CloudShell was the get-item command above. This query will retrieve the item with Id = 202 from the ContentCatalog DynamoDB table and return only the Title, ContentType, and Services attributes. It will also return information about the consumed read capacity.

Query options I could add to my query are `--consistent-read` to ensure I receive the latest data, `--projection-expression` to return only specific attributes, and `--return-consumed-capacity` to see how much read capacity the operation used. I could also use `--expression-attribute-names` when an attribute name conflicts with a DynamoDB reserved word.


![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-databases-query_733d9399)

---

## Transactions

A transaction is a group of operations that DynamoDB executes as a single atomic unit. In this example, the transaction adds a new comment to the `Comment` table and increases the `Comments` count in the `Forum` table. Both operations succeed together, or if one fails, neither change is applied.


I ran a transaction using the `aws dynamodb transact-write-items` command in AWS CloudShell. This transaction did two things: it added a new comment to the `Comment` table and increased the `Comments` count by 1 in the `Forum` table. Both operations were performed atomically as part of the same transaction.


![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-databases-query_2f65f83e)

---

---

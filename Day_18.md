<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Secure Secrets with Secrets Manager

**Project Link:** [View Project](http://nextwork.ai/projects/aws-security-secretsmanager)

**Author:** Naga Sri Sai Chaitanya Kolluri  
**Email:** saichaitanya518@gmail.com

---

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-security-secretsmanager_r7s8t9u0)

---

## Introducing Today's Project!

In this project, I will demonstrate how to securely manage AWS credentials in a web application. I’m doing this project to understand the risks of hardcoding AWS credentials in source code and how to protect them using AWS Secrets Manager.

I’ll start by exploring a simple web app that lists S3 buckets, clone it to my computer, and configure it with hardcoded AWS credentials. Then, I’ll push the code to GitHub to understand the security risks of exposing credentials publicly.

Next, I’ll store the credentials securely in AWS Secrets Manager and update the web app to retrieve them from Secrets Manager instead of hardcoding them. Finally, I’ll learn how to remove exposed credentials from GitHub history and keep the repository secure.

This project will help me understand AWS Secrets Manager, credential security, and best practices for protecting sensitive information.


### Tools and concepts

Services I used were AWS Secrets Manager, Amazon S3, and GitHub. Key concepts I learnt include securely storing AWS credentials in Secrets Manager, retrieving secrets from an application, avoiding hardcoded credentials, Git version control, GitHub secret scanning, and using Git rebase to remove sensitive credentials from Git history.


### Project reflection

This project took me approximately 2 hours. The most challenging part was resolving the Git merge conflict and removing the hardcoded AWS credentials from the Git history. It was most rewarding to successfully secure the credentials using AWS Secrets Manager and verify that the web app could still access the S3 buckets without exposing sensitive credentials in the source code.


I chose to do this project today because I wanted to strengthen my understanding of AWS security and learn how to protect sensitive credentials in real-world applications. Something that would make learning with NextWork even better is having more hands-on troubleshooting examples and additional real-world scenarios to practice solving common AWS and Git issues.


---

## Hardcoding credentials

In this project, a sample web app is exposing AWS credentials in `config.py`, where they are hardcoded. It is unsafe to hardcode credentials because the source code may be accessible to the public, allowing anyone to view and potentially misuse the exposed AWS credentials.


I've set up the initial configuration with AWS credentials hardcoded in the web app's `config.py` file. These credentials are just examples because this project is a security demonstration, and using real credentials could expose sensitive AWS resources and create a security risk.


![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-security-secretsmanager_j2k3l4m5)

---

## Using my own AWS credentials

As an extension for this project, I also decided to set up a Python virtual environment to keep the project's dependencies isolated. To set up my virtual environment, I installed the required Python packages and configured the environment needed to run the web application successfully.


When I first ran the app, I ran into an error because, we are using place holder credentials in config.py, which are not real AWS credentials.

To resolve the `InvalidAccessKeyId` error, I updated the application configuration with my valid AWS access key and secret access key. This allowed the application to authenticate successfully with AWS.


![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-security-secretsmanager_wghjteykut)

---

## Pushing Insecure Code to GitHub

Once I updated the web app code with credentials, I forked the repository because I wanted to create my own copy of the project on GitHub and safely test pushing the changes. A fork is different from a clone because a fork creates a copy of the repository under my GitHub account, while a clone downloads a repository from GitHub to my local computer so I can work on it.


To connect my local repository to the forked repository, I used `git init` to initialize the local Git repository and `git remote set-url` to point it to my forked GitHub repository. I then used `git add` to stage the changes and `git commit` to save the hardcoded `config.py` changes in Git history. Finally, `git push` attempted to upload the commit to GitHub, but GitHub's secret scanning detected the hardcoded AWS access key and secret key and blocked the push to prevent the credentials from being exposed.


GitHub blocked my push because it detected hardcoded AWS access key and secret key in my code using secret scanning. This is a good security feature because it prevents sensitive credentials from being publicly exposed and helps protect AWS resources from unauthorized access.


![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-security-secretsmanager_o2p3q4r5)

---

## Secrets Manager

AWS Secrets Manager is a service used to securely store, manage, and retrieve sensitive information such as passwords, API keys, and credentials. I'm using it to store my AWS credentials instead of hardcoding them in the application's source code. Other common use cases include storing database passwords, API keys, OAuth tokens, and other application secrets.


Another feature in Secrets Manager is secret rotation, which means automatically changing a secret's credentials at regular intervals. It's useful in situations where credentials need to be regularly updated to reduce the risk of unauthorized access if a secret is accidentally exposed or compromised.


Secrets Manager provides sample code in various languages, like Python, Java, JavaScript, and C#. This is helpful because it shows how to securely retrieve secrets from Secrets Manager in an application, making it easier to integrate the service without hardcoding sensitive credentials.


![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-security-secretsmanager_h2i3j4k5)

---

## Updating the web app code

I updated the `config.py` file to retrieve the AWS credentials from AWS Secrets Manager instead of storing them directly in the code. The `get_secret()` function will connect to Secrets Manager, retrieve the stored secret, and return the credentials so the web app can use them securely.


I also added code to `config.py` to extract the AWS access key and secret access key from the retrieved secret. This is important because the application needs these credentials to authenticate with AWS and access the S3 buckets without exposing them directly in the source code.


![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-security-secretsmanager_v0w1x2y3)

---

## Rebasing the repository

Git rebasing is a Git operation used to rewrite and reorganize commit history. I used it to replace `pick` with `d` (drop) during an interactive rebase. This was necessary because I wanted to remove the commit containing the hardcoded AWS credentials from my local Git history before pushing the repository again.


A merge conflict occurred during rebasing because Git found conflicting changes between the commits being replayed. I resolved the merge conflict by reviewing the conflicting sections, removing the hardcoded AWS credentials, keeping the correct code, and then continuing the rebase.


Once the merge conflict was resolved, I verified that the `config.py` file no longer contained hardcoded AWS credentials and that the application was retrieving the credentials securely from AWS Secrets Manager. I also checked the Git history to ensure the credential-containing changes were removed before pushing the repository.


![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-security-secretsmanager_t5u6v7w8)

---

---

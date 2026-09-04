<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Cloud Security with AWS IAM

**Project Link:** [View Project](http://nextwork.ai/projects/aws-security-iam)

**Author:** Naga Sri Sai Chaitanya Kolluri  
**Email:** saichaitanya518@gmail.com

---

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-security-iam_1c864649)

---

## Introducing Today's Project!

### Project overview

In this project, I will demonstrate IAM, and its policies, how they work on EC2's. I'm doing this project to learn about IAM.

### Tools and concepts

Services I used were EC2, IAM users, groups, policies, policy simulator, Key concepts I learnt include: IAM users and groups, IAM policies and permissions, tag-based access control, explicit Deny vs. Allow, AWS Account Aliases, and using the IAM Policy Simulator to test permissions safely.

### Project reflection

This project took me approximately 1hr.The most challenging part was doing it myself. It was most rewarding to results.

---

## Tags

### What I did in this step

In this step, I will launch two Amazon EC2 instances. because, that way we can increase computing power.

### Understanding tags

Tags are like label, they are used to identify same resources, like filtering.

### My tag configuration

The tags I’ve used on my EC2 instances are Name and Env. The Name values are nextwork-prod-naga and nextword-dev-naga, while the Env values are production and development.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-security-iam_2e0e5a5d)

---

## IAM Policies

### What I did in this step

In this step, I will create IAM policy for development instance. because, give access to everything will cause an issue.

### Understanding IAM policies

IAM Policies are like giving permissions to IAM users, groups, or roles to can or can't with certain resources.

### The policy I set up

For this project, I’ve set up a policy using JSON

### Policy effect

I’ve created an IAM policy that allows EC2 actions on resources tagged with Env=development. It also allows ec2:Describe* actions on all EC2 resources, while explicitly denying ec2:CreateTags and ec2:DeleteTags

### Understanding Effect, Action, and Resource

The Effect, Action, and Resource attributes of a JSON policy means, Effect have two values like allow or deny, Action, what action  to allow or deny, on which Resource, it is getting applied "*" means all.

---

## My JSON Policy

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-security-iam_1c864649)

---

## Account Alias

### What I did in this step

In this step, I will create an account alias because it will provide a simple and easy-to-remember URL for users to log in to my AWS account.

### Understanding account aliases

An account alias is which will helps to create new url with this alia name, helps to easy to remember and to share with some one.

### Setting up my account alias

Creating an account alias took me 1 min. Now, my new AWS console sign-in URL is ************

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-security-iam_0eb4439b)

---

## IAM Users and User Groups

### What I did in this step

In this step, I will create a dedicated IAM group for all NextWork interns and an IAM user for the new intern because this will allow me to manage the interns' permissions from one place and give the intern their own login credentials to access AWS.

### Understanding user groups

IAM user groups are collection of users, to give or manage access to all of them.

### Attaching policies to user groups

I attached the policy I created to this user group, which means the policy will applied to all the users in that group.

### Understanding IAM users

IAM users are individual identities in AWS that represent a person or application. They can have their own sign-in credentials and permissions to access AWS resources.

---

## Logging in as an IAM User

### Sharing sign-in details

The first way is to give the user the sign-in URL, username, and password directly.

The second way is to send the user the sign-in URL and let them set their own password using the credentials/instructions provided by AWS.

### Observations from the IAM user dashboard

Once I logged in as my IAM user, I noticed “Access denied to servicecatalog:ListApplications”. This was because my IAM user did not have permission to perform the servicecatalog:ListApplications action.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-security-iam_6f2ab446)

---

## Testing IAM Policies

### What I did in this step

In this step, I will using AWS intern IAM user creatials to login. because, to test the policy attacted him is working or not.

### Testing policy actions

I tested my JSON IAM policy by performing stoping instances with tag production and development.

### Stopping the production instance

When I tried to stop the production instance "Failed to stop the instance, you are not authorized to perform this operation. This was because, we donot permession to stop instance with tag production.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-security-iam_0e7a9d6a)

### Stopping the development instance

Next, when I tried to stop the development instance, it worked, but it did not work for the production instance. This was because the IAM user was added to the NextWork development group, which has a policy that allows the user to perform actions on resources tagged with Env=development.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-security-iam_1811801c)

---

## IAM Policy Simulator

To extend my project, I'm going to use IAM policy simulator, I'm doing this because, to test policy, shuttin down the development instance is not good way. so, it is best practice to run these tests in another way.

### Understanding the IAM Policy Simulator

The IAM Policy Simulator is used to test IAM policies. It is useful for testing whether a policy allows or denies specific actions without actually making changes to AWS resources or disrupting other users' work.

### How I used the simulator

I set up a simulation for deleteTag and stopInstance.The results were both are denied, I had to adjust stopInstance tag to development, now worked for stopInstance action.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-security-iam_069d8a621)

---

---

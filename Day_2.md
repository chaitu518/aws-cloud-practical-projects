<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Host a Website on Amazon S3

**Project Link:** [View Project](http://nextwork.ai/projects/aws-host-a-website-on-s3)

**Author:** Naga Sri Sai Chaitanya Kolluri  
**Email:** saichaitanya518@gmail.com

---

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-host-a-website-on-s3_5d4474f9)

---

## Introducing Today's Project!

### Project overview

In this project, I will demonstrate... I'm doing this project to learn hosting website with s3

### Tools and concepts

Services I used were s3 bucket. Key concepts I learnt include ACLs, policy bucket, enabling static web hosting, giving permissiong each specific object through ACLs, and previnting any to delete and object through bucket policies, how to upload a file, how to host it.

### Time, challenges, and wins

This project took me approximately 1hrThe most challenging part was talking time to understand and flow through, making sure, I understanding what I am doing. It was most rewarding to see the results.

---

## How I Set Up an S3 Bucket

### What I did in this step

In this step, I will openAmanon S3, because creation of a storage space for my website files.

### How long it took to create the bucket

Creating an S3 bucket took me 20 mins

### Region selection

The Region I picked for my S3 bucket was Hyderabad because, I am near to it.

### Understanding bucket name uniqueness

S3 bucket names are globally unique! This means no other aws account in the entire world can use my buckets name, unless I delete the bucket

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-host-a-website-on-s3_ba6d42ad)

---

## Upload Website Files to S3

### What I did in this step

In this step, I will create html file, and zip image. because, I have to upload them in s3 bucket.

### Files I uploaded

I uploaded two files to my S3 bucket - they were index.html, and other is resource for this html file.

### How the files work together

Both files are necessary for this project as index.html file depends on some resource to display to audience, they are present in the other file that's why.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-host-a-website-on-s3_a265af88)

---

## Static Website Hosting on S3

### What I did in this step

In this step, I will configure s3 bucket for static web hosting and visit my public website link.

### Understanding website hosting

Website hosting means, service store our files, make them accessible to people wordwide

### How I enabled website hosting

To enable website hosting with my S3 bucket, I enabled it by enablig static web hosting enabled, with Host a state website type, and entering my html page name there, Configure it and saved.

### Access Control Lists (ACLs)

An ACL is set of rules, who can get access to an resource, I enabled ACLs.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-host-a-website-on-s3_c22c54c0)

---

## Bucket Endpoints

### Understanding bucket endpoint URLs

Once static website is enabled, S3 produces a bucket endpoint URL, which is endpoint, help to access through worldwide.

### What I saw when I tested the endpoint

You first accessed the S3 bucket/object URL and received a 403 Forbidden error because ACL-based access control was enabled, and the object did not grant the required permission to the requester.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-host-a-website-on-s3_22ce4daf)

---

## Success!

### What I did in this step

In this step, I will make my website files in s3 publicly accessible, because website will go live on the internet

### How I resolved the 403 error

To resolve this 403 Forbidden error, I just enable, make it publicly availbe through the ACL permissions of each object.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-host-a-website-on-s3_5d4474f9)

---

## Bucket Policies

### What I did in this extension

In this project extension I'm about to create bucket policy and stop people from deleting my index.html file. I'm doing this so that, I will learn about bucket policy and test how it works.

### Understanding bucket policies

An alternative to ACLs is bucket policies, which are more powerful and flexible than ACLs. The main benefit of bucket policies is that they allow you to control who can perform specific actions, such as uploading, downloading, modifying, or deleting objects. ACLs, on the other hand, provide a simpler way to control access to buckets and individual objects, such as who can read or write them.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-host-a-website-on-s3_sm2sm2sm)

### What my bucket policy does

My bucket policy prevent everyone to delete an object. I tested this by deleting specific object and saw error saying " Failed to delete"

---

---

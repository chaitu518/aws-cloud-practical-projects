<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Encrypt Data with AWS KMS

**Project Link:** [View Project](http://nextwork.ai/projects/aws-security-kms)

**Author:** Naga Sri Sai Chaitanya Kolluri  
**Email:** saichaitanya518@gmail.com

---

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-security-kms_w0x1y2z3)

---

## Introducing Today's Project!

In this project, I will demonstrate how to create and configure **AWS Key Management Service (KMS)**, use KMS keys to encrypt **Amazon DynamoDB** data, add and retrieve data to verify encryption, and observe how AWS prevents unauthorized users from accessing the encrypted data. The goal is to understand how **AWS KMS integrates with DynamoDB to provide secure data encryption and access control**, ensuring that only authorized users and services can access sensitive data.


### Tools and concepts

Services I used include Amazon DynamoDB, AWS KMS, and AWS IAM. Key concepts I learned include encryption, KMS key management, IAM permissions, key policies, and access control.

### Project reflection

This project took me approximately 1–2 hours. The most challenging part was understanding the relationship between DynamoDB permissions and KMS key permissions. It was most rewarding to successfully control access to encrypted data and verify that unauthorized users could not decrypt it.

I chose to do this project today because I wanted to understand how AWS protects sensitive data using encryption and access control. Something that would make learning with NextWork even better is more hands-on troubleshooting scenarios and real-world project examples.

---

## Encryption and KMS

Encryption is the process of converting readable data (plaintext) into an unreadable form (ciphertext) using an encryption key. Companies and developers use encryption to protect sensitive data from unauthorized access and data breaches.

Encryption keys are cryptographic values used by encryption algorithms to transform plaintext into ciphertext and, when authorized, decrypt the ciphertext back into its original form. AWS Key Management Service (KMS) helps organizations create, manage, and control these encryption keys securely.


AWS KMS is a managed AWS service used to create, manage, and control encryption keys. Key management systems are important because they securely store and control encryption keys, ensuring only authorized users can use them to access encrypted data.

Encryption keys are broadly categorized as symmetric keys and asymmetric keys. I set up a symmetric key because it uses the same key for encryption and decryption, making it suitable for encrypting DynamoDB data efficiently.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-security-kms_a2b3c4d5)

---

## Encrypting Data

My encryption key will safeguard data in DynamoDB, which is stored securely and protected from unauthorized access.

The different encryption options in DynamoDB include AWS-owned KMS keys, AWS-managed KMS keys, and customer-managed KMS keys. Their differences are based on who manages the key and how much control you have over it. I selected a customer-managed KMS key because it provides greater control over key management and access permissions.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-security-kms_q8r9s0t1)

---

## Data Visibility

Rather than controlling who has access to the key directly, KMS manages user permissions by *using IAM policies and KMS key policies to control which users and roles can perform actions such as encrypt and decrypt.


Despite encrypting my DynamoDB table, I could still see the table's items because I have permission to access the data. DynamoDB uses transparent encryption, which automatically decrypts the data for authorized users when they access it.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-security-kms_c0d1e2f3)

---

## Denying Access

I configured a new IAM user to have full DynamoDB access. The permission policy I granted this user is AmazonDynamoDBFullAccess, but I did not grant any KMS permissions.

After accessing the DynamoDB table as the test user, I encountered “Access denied to kms:Decrypt” because the user did not have permission to use the KMS key. This confirmed that DynamoDB access alone is not sufficient when customer-managed KMS encryption is used.


![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-security-kms_w0x1y2z3)

---

## EXTRA: Granting Access

To allow my test user to use the encryption key, I added the test user as a key user. The KMS key policy was updated to include both users under the Principal section, granting the test user permission to use the KMS key for decryption.

Using the test user, I retried accessing the DynamoDB table. I observed that the data was now accessible, which confirmed that the test user had the required KMS permissions to decrypt the DynamoDB data.

Encryption secures data instead of leaving it exposed in plaintext. I could combine encryption with IAM access controls to ensure that only authorized users can access the data and use the encryption key.

![Image](http://nextwork.ai/sparkling_purple_peaceful_chinese_gooseberry/uploads/aws-security-kms_feffb2fb8)

---

---

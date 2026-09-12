# Day 21 — CDA: Security

## Topic Overview

- IAM programmatic access
- IAM Roles and EC2 Instance Profiles
- Cross-account access
- Parameter Store and Secrets Manager
- S3 bucket policies and least privilege
- S3 SSE-S3 / SSE-KMS / SSE-C
- KMS envelope encryption and data keys
- AWS CloudHSM
- ECS Task IAM Roles
- IAM Policy Simulator
- API Gateway Lambda Authorizers
- AWS Certificate Manager
- Parameter Store policies
- CloudFormation dynamic references
- Identity Broker + STS
- Cognito MFA
- RDS SQL Server TDE
- CloudFront signed URLs / signed cookies

---

# 1. IAM Programmatic Access

For an application running on an **on-premises Linux server**, the source uses programmatic credentials/access keys for AWS SDK access.

```text
On-Prem Application
    ↓
IAM User / Access Keys
    ↓
~/.aws/credentials
    ↓
AWS SDK
```

### Key distinction

```text
Application on EC2
→ IAM Role + Instance Profile

Application on-premises
→ Programmatic credentials
```

Username/password are for human console access; access keys are for programmatic AWS API/SDK/CLI access.

---

# 2. IAM Roles for EC2

For applications running on EC2, use an IAM Role instead of storing long-term access keys.

```text
EC2
 ↓
IAM Role
 ↓
Instance Profile
 ↓
Temporary Credentials
 ↓
AWS Services
```

### Benefits

- Temporary credentials
- Automatic credential rotation
- No hardcoded long-term keys
- Centralized permission management

### Exam trigger

> Application on EC2 needs the safest way to access S3/DynamoDB/etc.

**Answer: IAM Role**

---

# 3. Cross-Account Access

When a developer needs access to resources in multiple AWS accounts, use cross-account role access.

```text
Account A User
      ↓
AssumeRole
      ↓
Role in Account B/C
      ↓
Target Resources
```

### Important

- Avoid creating separate IAM users in every account.
- Users can assume roles in other accounts.
- MFA can be required when assuming the role.

### SCP trap

```text
SCP → Limits maximum permissions
IAM policy → Grants permissions
```

An SCP by itself does not grant access.

---

# 4. Systems Manager Parameter Store

Parameter Store provides centralized storage for configuration values, passwords, connection strings, and secure strings.

## SecureString

Use:

```text
SSM Parameter Store
→ SecureString
```

when multiple applications/Lambda functions need a centrally managed encrypted configuration value.

### Exam trigger

> Several Lambda functions need the same encrypted connection string.

**Answer: Parameter Store SecureString**

### Cheat Sheet

- [AWS Systems Manager Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/aws-systems-manager/)

---

# 5. AWS Secrets Manager

Secrets Manager stores secrets such as:

- Database credentials
- Passwords
- API keys

Its key exam feature is **automatic rotation**.

### Decision rule

```text
Secure shared configuration
→ Parameter Store SecureString

Secret + automatic rotation
→ Secrets Manager
```

### Cheat Sheets

- [AWS Secrets Manager — Tutorials Dojo](https://tutorialsdojo.com/aws-secrets-manager/)
- [Secrets Manager vs Parameter Store](https://tutorialsdojo.com/aws-secrets-manager-vs-systems-manager-parameter-store/)

---

# 6. S3 Bucket Policies and Least Privilege

Important policy elements:

| Element | Meaning |
|---|---|
| `Effect` | Allow / Deny |
| `Principal` | Who receives the permission |
| `Action` | API operation |
| `Resource` | Target ARN |

### Least Privilege

If the role only needs to read objects, use:

```text
s3:GetObject
```

not:

```text
s3:*
```

### Prefix restriction

```text
arn:aws:s3:::tdojo/*
→ All objects
```

```text
arn:aws:s3:::tdojo/qa/*
→ Only objects under qa/
```

### Exam trigger

> Developer reads everything; QA reads only `qa/*`.

Use separate policy statements with separate `Resource` ARNs.

### Cheat Sheet

- [Amazon S3 Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/amazon-s3/)

---

# 7. S3 Server-Side Encryption

Three encryption types in this set:

| Type | Key Control | Main Concept |
|---|---|---|
| SSE-S3 | Amazon S3 | S3-managed keys |
| SSE-KMS | AWS KMS | KMS keys |
| SSE-C | Customer | Customer-provided key |

---

# 8. SSE-S3

SSE-S3 uses Amazon S3-managed encryption keys.

Request header:

```text
x-amz-server-side-encryption
```

### Memory hook

```text
SSE-S3 → S3 manages key
```

---

# 9. SSE-KMS

SSE-KMS uses AWS KMS keys.

Request header:

```text
x-amz-server-side-encryption: aws:kms
```

Optional specific key header:

```text
x-amz-server-side-encryption-aws-kms-key-id
```

For the default KMS key in the practice scenario, the key-ID header is not required.

### Enforcing encryption

An S3 bucket policy can deny `s3:PutObject` requests that do not meet the required encryption condition/header.

### Cheat Sheets

- [AWS KMS Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/aws-key-management-service-aws-kms/)
- [Amazon S3 Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/amazon-s3/)

---

# 10. SSE-C

SSE-C means **Server-Side Encryption with Customer-Provided Keys**.

Required headers:

```text
x-amz-server-side-encryption-customer-algorithm
x-amz-server-side-encryption-customer-key
x-amz-server-side-encryption-customer-key-MD5
```

Algorithm in the source:

```text
AES256
```

### Critical behavior

S3 does not store the customer key itself. The same key is needed to retrieve/decrypt the object.

```text
Lose SSE-C key
→ Lose access to object
```

### Memory hook

```text
SSE-S3 → S3-managed
SSE-KMS → KMS-managed
SSE-C → Customer-provided
```

---

# 11. KMS Envelope Encryption

Envelope encryption uses a data key to encrypt the actual data, then protects the data key with another key.

```text
Plaintext Data
      ↓
Data Key
      ↓
Encrypted Data

Data Key
      ↓
KMS / Root Key
      ↓
Encrypted Data Key
```

### Core rule

```text
Data → Data Key
Data Key → KMS/root key
```

---

# 12. KMS `GenerateDataKey`

For local encryption:

1. Call `GenerateDataKey`.
2. Receive a plaintext data key and encrypted data key.
3. Encrypt data locally using the plaintext data key.
4. Erase the plaintext data key from memory.
5. Store the encrypted data key alongside encrypted data.

```text
GenerateDataKey
      ↓
Plaintext Key + Encrypted Key
      ↓
Encrypt locally
      ↓
Erase plaintext key
      ↓
Store encrypted key with encrypted data
```

## Local Decryption

1. Call KMS `Decrypt` on the encrypted data key.
2. Use the returned plaintext data key to decrypt locally.
3. Erase the plaintext data key from memory.

```text
Encrypted Data Key
      ↓
KMS Decrypt
      ↓
Plaintext Data Key
      ↓
Decrypt locally
      ↓
Erase plaintext key
```

### `GenerateDataKey` vs `GenerateDataKeyWithoutPlaintext`

| API | Plaintext key returned? |
|---|---|
| `GenerateDataKey` | ✅ |
| `GenerateDataKeyWithoutPlaintext` | ❌ |

### Cheat Sheet

- [AWS KMS Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/aws-key-management-service-aws-kms/)

---

# 13. AWS CloudHSM

Use CloudHSM when the requirement emphasizes dedicated hardware security modules under customer control.

Typical triggers:

- Dedicated HSM
- Third-party validated hardware
- Exclusive control of keys
- Specific compliance requirements
- PKCS#11 / JCE / CNG integration

### Memory hook

```text
KMS → Managed key service
CloudHSM → Dedicated HSM control
```

### Cheat Sheet

- [AWS KMS Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/aws-key-management-service-aws-kms/)
- [AWS CloudHSM Documentation](https://docs.aws.amazon.com/cloudhsm/)

---

# 14. ECS Task IAM Roles

For Fargate tasks that require AWS permissions, use task IAM roles.

```text
Fargate Task
   ↓
Task IAM Role
   ↓
AWS Resources
```

### Do not confuse

- IAM Group → cannot be attached directly to an ECS task.
- Container Instance Role → relevant to ECS EC2 launch type.
- Service-linked Role → used by the ECS service itself.

### Exam trigger

> Four Fargate tasks need different AWS permissions.

**Answer: separate IAM Task Roles as required by each task.**

### Cheat Sheet

- [Amazon ECS Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/amazon-elastic-container-service-amazon-ecs/)

---

# 15. IAM Policy Simulator

IAM Policy Simulator tests effective permissions before making changes to live resources.

It can test:

- IAM policies
- Resource-based policies
- SCP impact
- Specific actions/resources
- Policy conditions

### Important

```text
Simulator
→ Allow or Deny result
→ No real AWS action is executed
```

### Exam trigger

> Test IAM/resource policies or SCP impact before applying them.

**Answer: IAM Policy Simulator**

### Cheat Sheet

- [IAM Policy Simulator — AWS Documentation](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_testing-policies.html)

---

# 16. API Gateway Lambda Authorizers

A Lambda authorizer is a Lambda function used by API Gateway to control access to an API.

Two types are important here:

```text
TOKEN authorizer
REQUEST authorizer
```

---

# 17. TOKEN Lambda Authorizer

Used when the caller identity is supplied as a bearer token, such as:

- JWT
- OAuth token

### Exam trigger

> Custom authorization similar to OAuth/SAML using a bearer token.

**Answer: Token-based Lambda Authorizer**

---

# 18. REQUEST Lambda Authorizer

Uses combinations of:

- Headers
- Query-string parameters
- Stage variables
- `$context` variables

### Exam trigger

> Custom authorization using headers and query-string parameters.

**Answer: Request Parameter-based Lambda Authorizer**

### Quick Map

```text
Bearer Token / JWT / OAuth
→ TOKEN

Headers / Query Parameters
→ REQUEST
```

### Cheat Sheet

- [Amazon API Gateway Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/amazon-api-gateway/)

---

# 19. AWS Certificate Manager

ACM manages SSL/TLS certificates and can import certificates from external certificate authorities.

The source identifies:

- ACM
- IAM certificate store

as places for an imported third-party certificate in the scenario.

### Memory hook

```text
Third-party SSL/TLS certificate
→ ACM / IAM certificate store
```

### Cheat Sheet

- [AWS Certificate Manager Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/aws-certificate-manager/)

---

# 20. Parameter Store Policies

Parameter policies are available for **Advanced-tier parameters** in the source.

| Policy | Purpose |
|---|---|
| `Expiration` | Delete parameter at a specified date/time |
| `ExpirationNotification` | Event when expiration time is reached |
| `NoChangeNotification` | Event when parameter has not changed for a specified period |

### Scenario

> Notify when a sensitive parameter has not changed for 90 days.

```text
Advanced Parameter
→ NoChangeNotification
→ EventBridge
→ SNS
```

### Critical distinction

```text
No change for N days
→ NoChangeNotification

Parameter expiration
→ ExpirationNotification
```

### Cheat Sheet

- [AWS Systems Manager Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/aws-systems-manager/)

---

# 21. CloudFormation Dynamic References

Dynamic references let CloudFormation retrieve sensitive values from services such as Parameter Store and Secrets Manager.

For a SecureString in SSM Parameter Store:

```text
SecureString
    ↓
ssm-secure dynamic reference
    ↓
CloudFormation resource
```

Example:

```yaml
Password: '{{resolve:ssm-secure:ParameterName:Version}}'
```

### Exam trigger

> Secure value stored in Parameter Store must be retrieved by a CloudFormation template.

Think:

**`ssm-secure` dynamic reference**

### Cheat Sheet

- [AWS CloudFormation Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/aws-cloudformation/)

---

# 22. Custom Identity Broker + STS

When an on-premises identity store is not SAML-compatible, the source recommends a custom identity broker.

```text
On-Prem LDAP
     ↓
Identity Broker
     ↓
STS
     ↓
Temporary AWS Credentials
     ↓
AWS Resources
```

STS operations mentioned:

```text
AssumeRole
GetFederationToken
```

### Important

The application authenticates the user against the corporate identity system, requests temporary credentials, provides them to the application/user, and refreshes them when they expire.

### Exam trigger

> LDAP is not SAML-compatible but temporary AWS credentials are required.

**Answer: Custom Identity Broker + STS**

---

# 23. Cognito User Pool MFA

Cognito User Pools can add MFA as a second authentication factor.

The source mentions:

- SMS
- TOTP

### Exam trigger

> Financial application needs a second factor beyond username/password.

**Answer: Enable MFA on the Cognito User Pool**

### Cheat Sheet

- [Amazon Cognito Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/amazon-cognito/)

---

# 24. RDS SQL Server — TDE

Transparent Data Encryption (TDE) protects SQL Server data at the database level.

```text
Data
 ↓
TDE encrypts before storage
 ↓
Encrypted storage

Encrypted storage
 ↓
TDE decrypts when read
 ↓
Data
```

### Exam trigger

> SQL Server must automatically encrypt data before writing to storage and decrypt when reading.

**Answer: TDE**

### TDE vs RDS Encryption

```text
TDE
→ Database-level encryption

RDS encryption
→ Underlying storage encryption
```

### Cheat Sheet

- [Amazon RDS Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/amazon-relational-database-service-amazon-rds/)

---

# 25. CloudFront Signed URLs and Signed Cookies

CloudFront signed URLs/cookies control access to protected content.

## Signed URL

Useful for controlled access to specific content and can include an expiration time.

## Signed Cookies

Useful when granting access to multiple restricted files without changing the URLs.

### Exam trigger

> Prevent unauthorized use/hotlinking of protected S3 content and control viewer access at scale.

Think:

**CloudFront + signed URLs or signed cookies**

### CORS trap

CORS controls browser cross-origin requests. It is not the primary viewer-access control mechanism for protected content.

### Cheat Sheet

- [Amazon CloudFront Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/amazon-cloudfront/)

---

# 26. Security Decision Map

| Scenario | Think |
|---|---|
| On-prem application needs SDK access | Programmatic credentials |
| EC2 application needs AWS access | IAM Role |
| Access another AWS account | Cross-account Role / `AssumeRole` |
| Shared encrypted config | Parameter Store `SecureString` |
| Secret + automatic rotation | Secrets Manager |
| S3 least privilege | Specific `Action` + `Resource` |
| S3-managed encryption | SSE-S3 |
| KMS-backed S3 encryption | SSE-KMS |
| Customer-provided S3 key | SSE-C |
| Local KMS encryption | `GenerateDataKey` |
| Local KMS decryption | `Decrypt` |
| Dedicated HSM control | CloudHSM |
| Fargate permissions | Task IAM Role |
| Test IAM/resource policies | IAM Policy Simulator |
| OAuth/JWT/Bearer | TOKEN Authorizer |
| Header/query authorization | REQUEST Authorizer |
| Import third-party certificate | ACM / IAM certificate store |
| Parameter unchanged for 90 days | `NoChangeNotification` |
| CloudFormation secret | Dynamic Reference |
| Non-SAML LDAP federation | Identity Broker + STS |
| Second authentication factor | Cognito MFA |
| SQL Server data encryption | TDE |
| Protected CDN content | CloudFront signed URL/cookie |

---

# 27. Most Important Comparisons

## IAM

```text
EC2 → Role
On-Prem → Programmatic credentials
Cross-account → AssumeRole
Fargate → Task Role
```

## Secrets

```text
SecureString → Parameter Store
Automatic rotation → Secrets Manager
```

## S3 Encryption

```text
SSE-S3 → S3-managed key
SSE-KMS → KMS key
SSE-C → Customer-provided key
```

## Lambda Authorizers

```text
TOKEN → JWT / OAuth / Bearer token
REQUEST → Headers / Query / Stage Variables / $context
```

## KMS

```text
GenerateDataKey → Local encryption
Decrypt → Recover plaintext data key
```

## Policy Tools

```text
SCP → Limits permissions
IAM policy → Grants permissions
Policy Simulator → Tests permissions
```

---

# 28. Common Exam Traps

### Trap 1
EC2 application + hardcoded access keys.

**Use IAM Role instead.**

### Trap 2
`SecureString` automatically rotating credentials.

**Parameter Store does not provide the automatic-rotation answer in these scenarios; use Secrets Manager when rotation is required.**

### Trap 3
Using `s3:*` when only read access is needed.

**Use `s3:GetObject`.**

### Trap 4
Confusing SSE-KMS and SSE-C headers.

```text
SSE-KMS → x-amz-server-side-encryption: aws:kms
SSE-C → customer-algorithm + customer-key + customer-key-MD5
```

### Trap 5
Using encrypted data key directly for local decryption.

**First call KMS `Decrypt`, then use the plaintext data key.**

### Trap 6
Using `GenerateDataKeyWithoutPlaintext` when local encryption needs the plaintext key.

**Use `GenerateDataKey`.**

### Trap 7
Using an ECS container instance role for a Fargate task.

**Use a Task IAM Role.**

### Trap 8
Assuming an SCP grants access.

**SCPs limit; IAM policies grant.**

### Trap 9
TOKEN vs REQUEST authorizer.

```text
Bearer token → TOKEN
Header/query parameters → REQUEST
```

### Trap 10
`NoChangeNotification` vs `ExpirationNotification`.

```text
Not changed → NoChangeNotification
Expiring → ExpirationNotification
```

### Trap 11
CloudHSM vs KMS.

```text
Dedicated HSM under exclusive control → CloudHSM
Managed key service → KMS
```

### Trap 12
CORS vs CloudFront signed access.

```text
Cross-origin browser request → CORS
Protected viewer access → Signed URL/Cookie
```

---

# 29. Final Memory Map

```text
IAM
│
├── EC2 → Role + Instance Profile
├── On-Prem → Programmatic credentials
├── Cross-account → AssumeRole
├── Fargate → Task Role
└── Policy testing → IAM Policy Simulator
```

```text
SECRETS
│
├── SecureString → Parameter Store
├── Rotation → Secrets Manager
└── CloudFormation → Dynamic References
```

```text
S3 ENCRYPTION
│
├── SSE-S3 → S3-managed
├── SSE-KMS → KMS
└── SSE-C → Customer-provided
```

```text
KMS
│
├── GenerateDataKey → Local encryption
├── Decrypt → Local recovery of plaintext data key
└── CloudHSM → Dedicated HSM control
```

```text
API AUTH
│
├── TOKEN → JWT/OAuth/Bearer
└── REQUEST → Header/Query parameters
```

```text
SECURITY
│
├── ACM → Certificates
├── Cognito → User MFA
├── TDE → SQL Server database encryption
└── CloudFront → Signed URLs/Cookies
```

---

# 30. Cheat Sheets & Quick References

- [AWS IAM — Tutorials Dojo](https://tutorialsdojo.com/aws-identity-and-access-management-iam/)
- [AWS Systems Manager — Tutorials Dojo](https://tutorialsdojo.com/aws-systems-manager/)
- [AWS Secrets Manager — Tutorials Dojo](https://tutorialsdojo.com/aws-secrets-manager/)
- [Amazon S3 — Tutorials Dojo](https://tutorialsdojo.com/amazon-s3/)
- [AWS KMS — Tutorials Dojo](https://tutorialsdojo.com/aws-key-management-service-aws-kms/)
- [Amazon ECS — Tutorials Dojo](https://tutorialsdojo.com/amazon-elastic-container-service-amazon-ecs/)
- [Amazon API Gateway — Tutorials Dojo](https://tutorialsdojo.com/amazon-api-gateway/)
- [AWS CloudFormation — Tutorials Dojo](https://tutorialsdojo.com/aws-cloudformation/)
- [AWS Certificate Manager — Tutorials Dojo](https://tutorialsdojo.com/aws-certificate-manager/)
- [Amazon Cognito — Tutorials Dojo](https://tutorialsdojo.com/amazon-cognito/)
- [Amazon RDS — Tutorials Dojo](https://tutorialsdojo.com/amazon-relational-database-service-amazon-rds/)
- [Amazon CloudFront — Tutorials Dojo](https://tutorialsdojo.com/amazon-cloudfront/)

---

# Review of What Was Covered

The main security decision pattern in this set is:

```text
Identify the asset
      ↓
Identify who/what needs access
      ↓
Identify where the application runs
      ↓
Identify whether the requirement is access, secret management, encryption, or authentication
      ↓
Choose the narrowest AWS security mechanism
```

### Highest-value exam triggers

```text
EC2 app → IAM Role
On-prem app → Programmatic credentials
Shared SecureString → Parameter Store
Automatic rotation → Secrets Manager
Least privilege → Specific IAM/S3 actions
SSE-S3 → S3-managed keys
SSE-KMS → KMS
SSE-C → Customer key
Local encryption → GenerateDataKey
Local decryption → KMS Decrypt
Dedicated HSM → CloudHSM
Fargate → Task Role
Policy testing → IAM Policy Simulator
OAuth/JWT/Bearer → TOKEN authorizer
Header/query → REQUEST authorizer
No change for N days → NoChangeNotification
LDAP without SAML → Identity Broker + STS
Second factor → Cognito MFA
SQL Server data-level encryption → TDE
Protected CDN content → CloudFront signed URL/cookie
```

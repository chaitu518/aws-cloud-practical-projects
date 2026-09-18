# CDA — Review Mode Set 4

> **AWS Certified Developer – Associate (DVA-C02)**  
> Structured Revision Notes · Tutorials Dojo Practice Review

## Practice Result

| Category | Score |
|---|---:|
| CDA - Deployment | 5/5 (100%) |
| CDA - Development with AWS Services | 36/36 (100%) |
| CDA - Security | 11/11 (100%) |
| CDA - Troubleshooting and Optimization | 13/13 (100%) |
| **Total** | **65/65 (100%)** |

> **Review position:** 100% in this practice set. Use these notes for retention, rapid service selection, command/property recall, and exam-trap avoidance.

## Contents

1. [Practice Result](#practice-result)
2. [Deployment](#1-cda---deployment)
3. [Development with AWS Services](#2-cda---development-with-aws-services)
4. [Security](#3-cda---security)
5. [Troubleshooting and Optimization](#4-cda---troubleshooting-and-optimization)
6. [High-Yield Exam Memory Map](#5-high-yield-exam-memory-map)
7. [Fast Exam Triggers](#6-fast-exam-triggers)
8. [Tutorials Dojo Cheat Sheets](#7-tutorials-dojo-cheat-sheets)
9. [Final Review Focus](#8-final-review-focus)

---


---


---

## 1. CDA - Deployment

### 1. AWS SAM Deployment Workflow

#### Core flow

A typical local SAM deployment follows:

```text
Local SAM application
        ↓
sam build
        ↓
Package / upload deployment artifacts to S3
        ↓
sam deploy
        ↓
CloudFormation creates/updates the stack
```

#### Important commands

```bash
sam build
sam package
sam deploy
sam publish
```

🎯 **Exam Triggers**

- **Build the application locally** → `sam build`
- **Package/upload artifacts** → `sam package`
- **Package + deploy the SAM application** → `sam deploy`
- **Publish to Serverless Application Repository** → `sam publish`

📌 **Practice-Set Note**

The source explains the historical `sam package` step but also notes that modern `sam deploy` can handle packaging/uploading implicitly.

**Trap:** CodePipeline can automate SAM deployments, but it is not required for a local SAM deployment.

**Nested SAM applications:** include `CAPABILITY_AUTO_EXPAND` when required.

---

### 2. Elastic Beanstalk Deployment Strategies

#### Deployment options

| Strategy | Main idea |
|---|---|
| All at once | Update all instances together; capacity is unavailable briefly |
| Rolling | Update instances in batches; temporary capacity reduction |
| Rolling with additional batch | Launch an additional batch first; maintain capacity |
| Immutable | Deploy to fresh instances |
| Traffic splitting | Temporarily send a percentage of traffic to the new version |
| Blue/Green | Deploy to a separate environment, then swap CNAMEs |

🎯 **Exam Trigger**

Requirement:

> "Failed deployment should have the least impact on availability and rollback should be fast."

→ **Blue/Green**

Why?

- New version is deployed separately.
- Existing working environment remains available.
- Rollback can be done by switching traffic/CNAME back to the old environment.

**Trap:** Blue/green is different from rolling/immutable because the new environment is separate.

---

### 3. CloudFormation Lambda Inline Code

For an `AWS::Lambda::Function` resource:

```yaml
Code:
  ZipFile: |
    def lambda_handler(event, context):
        return "Hello"
```

🧠 **Remember**

- `Code` → parent property
- `ZipFile` → inline Python/Node.js source
- `CodeUri` → SAM `AWS::Serverless::Function`, not `AWS::Lambda::Function`
- `Handler` identifies the handler function; it is not the property used to place inline code

🎯 **Exam Trigger**

> **Question cue:** "Tiny Python/Node.js function can be written directly inside the CloudFormation template."

→ **`Code.ZipFile`**

---

### 4. CloudFormation Helper Scripts

| Helper | Purpose |
|---|---|
| `cfn-init` | Install packages, create files, start services, interpret metadata |
| `cfn-signal` | Signal CreationPolicy/WaitCondition that setup is complete |
| `cfn-get-metadata` | Retrieve resource metadata |
| `cfn-hup` | Detect metadata changes and execute hooks |

🎯 **Exam Trigger**

> **Question cue:** "Install software, create files, and start services during EC2 stack creation."

→ **`cfn-init`**

---


---

## 2. CDA - Development with AWS Services

### 5. S3 Event Notifications → Lambda

To execute processing automatically after an object upload:

```text
S3 ObjectCreated:Put
        ↓
S3 Event Notification
        ↓
Lambda
```

S3 event notifications can target:

- Lambda
- SQS
- SNS
- EventBridge

🎯 **Exam Trigger**

> **Question cue:** "Run Lambda whenever an object is uploaded."

→ **S3 Event Notification + `ObjectCreated:Put`**

**Trap:** S3 Object Lambda transforms objects on retrieval; it is not the normal answer for an upload-triggered workflow.

---

### 6. Lambda Access to Private RDS

If RDS is in a private subnet:

```text
Lambda
   ↓
VPC configuration
   ↓
Private subnet
   ↓
RDS
```

Configure Lambda with:

- VPC
- Appropriate subnets
- Security groups

🎯 **Exam Trigger**

> **Question cue:** "Lambda must access a private RDS/database/cache/internal service."

→ **Connect Lambda to the VPC**

**Trap:** IAM permissions alone do not establish network connectivity.

---

### 7. API Gateway: Lambda Proxy vs Custom Integration

#### Lambda proxy integration

API Gateway passes the request to Lambda in a standard proxy event structure.

Less mapping configuration is required.

#### Lambda custom/non-proxy integration

You explicitly configure:

- Method request → integration request mapping
- Integration response → method response mapping

🎯 **Exam Trigger**

> **Question cue:** "Specify how incoming request data is mapped to the integration request and how the response is mapped back."

→ **Lambda custom integration**

---

### 8. Write-Through Caching

#### Goal

Keep cache data current whenever the database is updated.

#### Pattern

```text
Update database
      ↓
Update cache
      ↓
Apply TTL
```

Example:

```text
save_item(item_id, item_value):
    ttl = 500
    database.UPDATE(item_id, item_value)
    cache.set(item_id, item_value, ttl)
```

🧠 **Key Idea**

**Write-through = DB write + cache write**

Adding TTL helps remove entries after a period and limits unnecessary cache retention.

**Trap:** DB update followed by `cache.delete()` is not write-through caching.

---

### 9. DynamoDB Optimistic Locking

When multiple clients may update the same item:

```text
Item has version number
       ↓
Read item + version
       ↓
Update only if version is still the expected version
       ↓
Version mismatch → update fails
```

#### Goal

Prevent one user's changes from silently overwriting another user's changes.

🎯 **Exam Trigger**

> **Question cue:** "Concurrent updates are overwriting one another."

→ **Optimistic locking with a version attribute**

**Important source note:** DynamoDB global tables use last-writer-wins reconciliation, so the practice explanation warns against relying on global tables for this locking requirement.

---

### 10. Secrets Manager for Rotating Credentials

For credentials/API keys/secrets that must be:

- encrypted
- regularly rotated
- automatically updated

→ **AWS Secrets Manager + automatic rotation**

#### Parameter Store vs Secrets Manager

| Requirement | Service |
|---|---|
| Secure configuration/value storage | Parameter Store |
| `SecureString` | Parameter Store |
| Automatic secret rotation | **Secrets Manager** |

---

### 11. Athena for Querying S3

#### Use Athena when

- Data already resides in S3
- Need ad-hoc SQL queries
- Do not want to load data into a database
- Want a serverless approach

🎯 **Exam Trigger**

> **Question cue:** "Run SQL directly against data in S3 without managing servers."

→ **Amazon Athena**

**Trap:** Redshift Spectrum requires Redshift; EMR requires cluster/infrastructure management.

---

### 12. DynamoDB Global Secondary Index (GSI)

#### Important properties

A GSI can have:

- Different partition key from base table
- Different sort key from base table

#### Capacity

Queries/scans on the GSI consume capacity from the **GSI**, not the base table.

#### Consistency

GSI queries support **eventual consistency only**.

#### GSI vs LSI

| Property | GSI | LSI |
|---|---|---|
| Partition key | Can differ | Same as base table |
| Sort key | Can differ | Alternate sort key |
| Consistency | Eventual only | Eventual or strong |
| Capacity | Separate index capacity | Uses base-table capacity |
| Size limit per partition key | No LSI-style 10 GB restriction from this question | 10 GB limit |

---

### 13. GSI WCU Planning

For a provisioned table:

- Base table writes consume base-table WCU.
- Updates to a GSI also consume GSI WCU.

#### Practice-set rule

To avoid throttling caused by index write activity:

> GSI provisioned **WCU should be equal to or greater than** the base table WCU.

---

### 14. DynamoDB Capacity Calculations

#### Writes

**1 WCU = 1 write/sec for an item up to 1 KB.**

For item size `S` KB:

```text
WCU per write = ceil(S / 1 KB)
Total WCU = writes/sec × WCU per write
```

Example from the set:

```text
10 writes/sec × 2 KB
= 10 × 2
= 20 WCU
```

#### Strongly consistent reads

**1 RCU = 1 strongly consistent read/sec for up to 4 KB.**

```text
RCU per read = ceil(item size / 4 KB)
Total RCU = reads/sec × RCU per read
```

Example:

```text
320 reads/sec × ceil(17/4)
= 320 × 5
= 1600 RCU
```

#### Eventually consistent reads

1 RCU supports **2 eventually consistent reads/sec** for an item up to 4 KB.

```text
Eventually consistent RCU
= (reads/sec × ceil(item size / 4 KB)) / 2
```

Example:

```text
20 reads/sec
2 KB item

ceil(2/4) = 1
20 × 1 / 2 = 10 RCU
```

⚠️ **Common Exam Traps**

- Do not multiply raw KB directly for RCU without rounding to 4 KB units.
- Eventual consistency requires half the RCU of strong consistency for the same rounded read unit.
- Do not use WCU logic for reads.

---

### 15. S3 + CloudFront for Global Static Content

For static:

- Images
- Videos
- HTML
- JavaScript

Use:

```text
S3 → CloudFront → Global users
```

💡 **Why?**

- S3 stores the static assets.
- CloudFront caches them close to users at edge locations.

🎯 **Exam Trigger**

> **Question cue:** "Static website/files + lowest global latency."

→ **Amazon S3 + Amazon CloudFront**

---

### 16. Lambda Custom Runtime

A **custom runtime** lets Lambda run code using a runtime implemented by the developer.

The runtime:

- Runs setup code
- Reads invocation events from the Lambda Runtime API
- Invokes the handler
- Returns the response to Lambda

The runtime executable is commonly supplied as:

```text
bootstrap
```

🎯 **Exam Trigger**

> **Question cue:** "Need a language/runtime not provided natively by Lambda."

→ **Custom Runtime**

Examples in the set include Rust.

---

### 17. Lambda Best Practices

High-value practices from the set:

- Reuse the **execution context** when possible.
- Put reusable initialization outside the handler.
- Use environment variables for operational parameters.
- Keep deployment packages focused and small.
- Minimize dependency complexity.
- Avoid recursive invocation patterns unless intentionally designed.

⚠️ **Common Trap**

Do not put all initialization and core logic unnecessarily inside the handler.

---

### 18. ECS Task Placement

When scheduling tasks across EC2 container instances, the set identifies the **random task placement strategy** for the stated requirement.

🎯 **Exam Trigger**

When the question explicitly asks for the placement strategy specified in this scenario:

→ **`random` placement strategy**

---

### 19. AppSync for Mobile Application Sync

For an application needing:

- synchronized app/game data
- cross-device state
- mobile/web clients
- managed synchronization

→ **AWS AppSync**

#### Distinguish from Cognito Sync

- **AppSync** → application data synchronization/API layer
- **Cognito Sync** → synchronize per-user application-related data across devices

---

### 20. AWS CLI Profiles

When using different IAM roles/credentials from the same machine:

```bash
aws <command> --profile <profile-name>
```

#### Pattern

```text
Create profile in AWS CLI config
        ↓
Use --profile
        ↓
Run command with that identity
```

---

### 21. DynamoDB Conditional Writes

When an update must happen only if a condition is true:

→ **Conditional write**

Typical purpose:

- prevent accidental overwrites
- enforce application-level conditions
- coordinate concurrent updates

🎯 **Exam Trigger**

> **Question cue:** "Only update the item if the expected condition is satisfied."

→ **Conditional writes**

---

### 22. DynamoDB `ReturnConsumedCapacity`

When troubleshooting/throttling and wanting consumed capacity information:

```text
ReturnConsumedCapacity = INDEXES
```

🧠 **Key Idea**

`INDEXES` requests consumed capacity for:

- table
- indexes involved

Useful when determining how capacity is being consumed by base table/GSI operations.

---

### 23. DynamoDB Streams + Lambda

To process newly written/changed items:

```text
DynamoDB Streams
      ↓
Lambda event source mapping
      ↓
Lambda function
```

#### IAM

The source answer uses:

```text
AWSLambdaDynamoDBExecutionRole
```

🎯 **Exam Trigger**

> **Question cue:** "Run Lambda when DynamoDB stream records are available."

→ **Lambda event source mapping**

---

### 24. AWS SAM for Serverless Applications

For a serverless application with Lambda/API Gateway/etc. where the deployment definition and reusable application structure are needed:

→ **AWS SAM**

SAM provides serverless-specific abstractions on top of CloudFormation.

---

### 25. DynamoDB Local Secondary Index (LSI)

An LSI:

- Must use the **same partition key** as the base table.
- Provides an alternative sort key.

The practice scenario requires creating a new table when an LSI is needed after the original table already exists.

🎯 **Exam Trigger**

> **Question cue:** "Need an LSI on an existing table."

→ You cannot simply add the LSI to the existing table; create a new table with the LSI and migrate data.

---

### 26. S3 Enforce Server-Side Encryption

The practice scenario requires forcing SSE-S3 encryption for uploads.

An S3 bucket policy can deny uploads unless the request contains:

```text
x-amz-server-side-encryption: AES256
```

🎯 **Exam Trigger**

> **Question cue:** "Reject unencrypted uploads."

→ Bucket policy checking the `x-amz-server-side-encryption` header.

---

### 27. EC2 Instance Metadata Service

EC2 instance metadata endpoint in the practice set:

```text
http://169.254.169.254/latest/meta-data/
```

Used to retrieve instance information such as:

- public IP
- private IP
- instance metadata

🎯 **Exam Trigger**

> **Question cue:** "Shell script on EC2 needs instance information without an external service."

→ **Instance Metadata Service (IMDS)**

---

### 28. Lambda Concurrency

Suppose account concurrency limit:

```text
2000
```

Two functions reserve:

```text
400 + 200 = 600
```

Remaining shared unreserved concurrency:

```text
2000 - 600 = 1400
```

That remaining capacity is shared by functions using unreserved concurrency.

A third function can still reserve capacity as long as the total reservation constraints allow it.

#### Key distinction

- **Reserved concurrency** → protects/limits a specific function.
- **Unreserved concurrency** → shared account pool.

---

### 29. Lambda Performance: Memory, CPU, Concurrency

#### Memory and CPU

Increasing Lambda memory also gives the function proportionally more CPU according to the Lambda resource model used in the practice explanation.

#### Account concurrency

The account concurrency limit is enforced against the sum of concurrent executions across functions.

🎯 **Exam Trigger**

> **Question cue:** "Improve CPU by changing Lambda configuration."

→ Increase **memory**, not a separate CPU quota request.

---

### 30. API Gateway Cache Invalidation

When API caching is enabled and per-key invalidation is configured, the client can invalidate a cache entry with:

```http
Cache-Control: max-age=0
```

🎯 **Exam Trigger**

> **Question cue:** "Consumer must fetch fresh data from integration endpoint."

→ Request with `Cache-Control: max-age=0`

---

### 31. API Gateway HTTPS

When testing an API Gateway invoke URL:

```text
https://...
```

is required.

The practice scenario reports `Connection refused` when using:

```text
http://...
```

🎯 **Exam Trigger**

> **Question cue:** "API Gateway endpoint is being invoked with HTTP and connection is refused."

→ Use **HTTPS**

---

### 32. Lambda Execution Context Reuse

Lambda may freeze an execution environment and reuse it for a later invocation.

This can allow reuse of:

- database connections
- SDK clients
- initialized libraries
- cached temporary files

#### Best practice

Initialize reusable resources outside the handler:

```text
global/client initialization
        ↓
handler()
```

instead of reconnecting on every invocation.

---

### 33. Lambda `/tmp` Storage

The practice set uses `/tmp` as transient local storage inside the Lambda execution environment.

Useful for:

- downloading temporary files
- unzipping/process staging
- caching data across reused execution contexts

The source describes `/tmp` storage as configurable from **512 MB to 10,240 MB** in the cited material.

🎯 **Exam Trigger**

> **Question cue:** "Need temporary local disk while processing a file."

→ **`/tmp`**

---

### 34. Elastic Beanstalk Multicontainer Docker

Elastic Beanstalk can manage multicontainer Docker environments and handle:

- infrastructure provisioning
- load balancing
- Auto Scaling
- monitoring
- container placement

🎯 **Exam Trigger**

> **Question cue:** "Easiest managed service for a multicontainer web application."

→ **AWS Elastic Beanstalk**

---

### 35. Cognito Sync

Cognito Sync is used for:

- application-related user data
- cross-device synchronization
- mobile/web user data
- datasets associated with Cognito identities

#### Distinguish

- **User Pool** → user directory/authentication
- **Identity Pool** → temporary AWS credentials / federated identities
- **Cognito Sync** → synchronize user application data across devices

---

### 36. DynamoDB Partition Key Design

Goal:

> Avoid hot partitions and distribute activity evenly.

The practice set selects **Item ID** because automatically generated/unique IDs provide a more uniform distribution than low-cardinality or repeated attributes such as:

- Course ID
- Course Name
- Course Price

🎯 **Exam Trigger**

> **Question cue:** "Avoid hot partitions / distribute workload evenly."

→ Prefer a high-cardinality partition key with uniform access patterns.

---

### 37. CodePipeline → Lambda via EventBridge

To trigger Lambda from a CodePipeline state change:

```text
CodePipeline
      ↓
EventBridge
      ↓
Lambda
```

🎯 **Exam Trigger**

> **Question cue:** "Trigger Lambda after a successful CodePipeline build/state change."

→ **Amazon EventBridge**

---


---

## 3. CDA - Security

### 38. S3 Access Point + S3 Object Lambda

For role-specific data redaction while maintaining a single source copy:

```text
User role
   ↓
S3 Access Point
   ↓
S3 Object Lambda Access Point
   ↓
Role-specific Lambda transformation
   ↓
GetObject → redacted object
```

🧠 **Key Idea**

- **S3 Access Point** → simplifies/controls access to a shared bucket
- **S3 Object Lambda** → transforms retrieved data before returning it

This is suitable when different roles need different views of the same underlying object.

---

### 39. Parameter Store Advanced Tier + `NoChangeNotification`

Requirement:

> Notify operations when sensitive parameters have not changed for 90 days.

Use:

1. Advanced Parameter Store
2. `NoChangeNotification` policy
3. EventBridge/SNS notification path

📌 **Important Distinction**

- `NoChangeNotification` → notify when parameter value has not changed for a specified period.
- `ExpirationNotification` → notify before an expiration event.
- `Expiration` → expiration policy.

---

### 40. S3 SSE-C vs Client-Side Encryption

When the organization supplies/manages the encryption key and AES-256 is required, the practice set identifies two approaches:

#### SSE-C

```text
Application → S3
      ↓
S3 encrypts object using customer-provided key
```

#### Client-side encryption

```text
Application
    ↓ encrypt locally using own key
Encrypted object
    ↓
S3
```

#### Distinguish

- **SSE-C** → S3 performs server-side encryption using a customer-provided key.
- **Client-side encryption** → application encrypts before uploading.

---

### 41. KMS Envelope Encryption

For local encryption with AWS KMS:

1. Call `GenerateDataKey`.
2. Receive:
   - plaintext data key
   - encrypted data key
3. Encrypt the large data locally with the plaintext data key.
4. Erase the plaintext data key from memory.
5. Store the encrypted data key alongside the ciphertext.

#### Memory map

```text
KMS
 ↓
GenerateDataKey
 ↓
plaintext key + encrypted key
 ↓
local encryption using plaintext key
 ↓
erase plaintext key
 ↓
store ciphertext + encrypted data key
```

**Trap:** `GenerateDataKeyWithoutPlaintext` cannot directly satisfy the described local-encryption step because there is no plaintext data key to perform the local encryption.

---

### 42. API Gateway Lambda Authorizers

For custom authorization based on caller identity:

→ **Lambda Authorizer**

#### Types

#### TOKEN authorizer

Receives identity in a bearer token such as:

- JWT
- OAuth token

#### REQUEST authorizer

Can receive identity from:

- headers
- query-string parameters
- stage variables
- `$context` variables

🎯 **Exam Trigger**

> **Question cue:** "Custom bearer-token authorization."

→ **Lambda Authorizer**

---

### 43. Secrets Manager Rotation

For:

- database credentials
- API keys
- application secrets
- automatic periodic rotation

→ **AWS Secrets Manager**

The source explicitly uses automatic rotation for this requirement.

---

### 44. CloudFormation Secure Dynamic References

For a sensitive license key:

```text
SSM Parameter Store
       ↓
SecureString
       ↓
CloudFormation dynamic reference
       ↓
ssm-secure
```

🎯 **Exam Trigger**

> **Question cue:** "Need secure parameter inside CloudFormation without hard-coding it."

→ `SecureString` + `ssm-secure` dynamic reference.

---

### 45. Custom Identity Broker + STS

When integrating an on-premises identity system that is not SAML-compatible:

```text
On-prem LDAP / identity store
          ↓
Custom identity broker
          ↓
AWS STS
          ↓
Temporary AWS credentials
```

🧠 **Key Idea**

STS provides short-lived, limited credentials instead of long-lived access keys.

---

### 46. Cognito MFA

For an additional authentication factor:

→ Enable **MFA in the Cognito User Pool**

🎯 **Exam Trigger**

> **Question cue:** "Username/password alone is not enough."

→ **Cognito MFA**

---

### 47. RDS SQL Server TDE

For SQL Server database encryption where the database automatically encrypts data before writing to storage and decrypts when reading:

→ **Transparent Data Encryption (TDE)**

#### TDE purpose

Transparent encryption/decryption of database data at rest.

---

### 48. CloudFront Signed URLs / Cookies

When preventing unauthorized websites from consuming protected content through your distribution:

→ **CloudFront signed URLs or signed cookies**

Useful when access should be authorized before CloudFront serves the object.

---


---

## 4. CDA - Troubleshooting and Optimization

### 49. X-Ray Daemon on EC2 / Auto Scaling

For an EC2-based application that needs distributed tracing:

```text
Application
   ↓
X-Ray SDK
   ↓
X-Ray daemon
   ↓
AWS X-Ray
```

The practice solution installs the daemon using an EC2 **user data** script.

🎯 **Exam Trigger**

> **Question cue:** "EC2/ASG Linux application needs X-Ray daemon automatically."

→ Install the X-Ray daemon through user data/configuration.

---

### 50. SQS FIFO and Deduplication

For duplicate-message handling:

→ **FIFO queue + deduplication IDs**

#### Key distinction

Standard SQS:

- at-least-once delivery
- duplicates can occur

FIFO:

- designed for ordered processing
- supports deduplication

🎯 **Exam Trigger**

> **Question cue:** "Duplicate messages must be prevented/handled deterministically."

→ FIFO + deduplication IDs.

---

### 51. X-Ray Annotations and Filtering

#### Annotations

- Indexed
- Searchable
- Usable in X-Ray filter expressions
- Good for data used to group/filter traces

#### Metadata

- Not indexed
- Stores arbitrary trace information
- Not used for trace filtering

🎯 **Exam Trigger**

> **Question cue:** "Need to identify/filter traces based on custom values."

→ **Annotations**

Ways identified in the set:

- X-Ray console filter expressions
- `GetTraceSummaries`

Example concept:

```text
annotation key = "customerType"
annotation value = "premium"
```

---

### 52. X-Ray Segments and Subsegments

Use:

- **Segment** → request/work handled by the service
- **Subsegment** → downstream call or finer-grained work

For downstream AWS/HTTP calls, the X-Ray SDK can create subsegments.

#### Data flow from the practice set

```text
X-Ray SDK
   ↓
segment documents + subsegments
   ↓
X-Ray daemon
   ↓
buffer/batch
   ↓
X-Ray service
```

---

### 53. Kinesis Shard Optimization

If shards are underutilized and latency is still acceptable:

→ **Merge cold shards**

🧠 **Remember**

- **Cold shard** → underutilized
- **Hot shard** → heavily utilized

So:

```text
cold + cold → merge
hot → split
```

---

### 54. Lambda Maximum Execution Time

The practice scenario has Lambda work taking about 20 minutes.

Lambda maximum invocation duration in the cited source:

```text
15 minutes
```

Therefore, invocations running longer than that terminate.

🎯 **Exam Trigger**

> **Question cue:** "Lambda process consistently takes more than the maximum execution duration."

→ **Lambda timeout / maximum execution time**

---

### 55. API Gateway Cache Metrics

`CacheHitCount` and `CacheMissCount` are associated with API caching.

#### If these metrics are missing

The practice-set answer:

→ **API caching is not enabled**

#### Useful API Gateway metrics

| Metric | Meaning |
|---|---|
| `IntegrationLatency` | Backend/integration responsiveness |
| `Latency` | Overall API request responsiveness |
| `CacheHitCount` | Requests served from cache |
| `CacheMissCount` | Requests that had to go to backend |

---

### 56. AWS WAF

For web attacks such as:

- SQL injection
- Cross-site scripting (XSS)

→ **AWS WAF**

#### Distinguish

- **WAF** → web request filtering/protection
- **GuardDuty** → threat detection
- **Firewall Manager** → centralized management of security rules
- **NACL** → subnet-level network traffic control

🎯 **Exam Trigger**

> **Question cue:** "Protect web application from SQL injection/XSS."

→ **AWS WAF**

---

### 57. Kinesis + KCL Worker Scaling

The practice scenario:

```text
Initial shards = 10
Resharded to = 20
```

With one KCL worker per instance, the ideal maximum number of instances is:

```text
20 instances
```

#### Core rule from the source

Normally:

```text
instances ≤ number of open shards
```

Each shard can be processed independently by one KCL worker/record processor.

---

### 58. RDS Enhanced Monitoring

CloudWatch provides instance-level database metrics.

**Enhanced Monitoring** gives more detailed OS-level information, such as:

- process-level CPU usage
- memory usage
- other operating-system statistics

🎯 **Exam Trigger**

> **Question cue:** "Need to see how individual processes consume CPU/memory on the RDS DB instance."

→ **RDS Enhanced Monitoring**

---

### 59. CodePipeline Manual Approval

For human code review before the next pipeline stage:

```text
CodePipeline
    ↓
Manual approval action
    ↓
SNS notification
    ↓
Human approves/rejects
    ↓
Pipeline resumes or stops
```

🎯 **Exam Trigger**

> **Question cue:** "Pipeline must pause for human approval."

→ **CodePipeline Manual Approval**

Notification target in the practice set:

→ **Amazon SNS**

---

### 60. X-Ray Annotations in Subsegments

When the goal is to record/filter data about downstream calls such as:

- RDS
- internal HTTP APIs
- external HTTP APIs

Use:

→ **Annotations in the subsegment section**

💡 **Why?**

- The downstream operation belongs in a subsegment.
- Annotations are indexed and searchable.

---

### 61. X-Ray IAM Managed Policy

For the X-Ray daemon to upload trace data:

→ **`AWSXRayDaemonWriteAccess`**

#### Distinguish

- `AWSXrayReadOnlyAccess` → read-only
- `AWSXrayFullAccess` → broad access
- `AWSXRayDaemonWriteAccess` → daemon write permissions

---

### 5. High-Yield Exam Memory Map

```text
SAM
 ├─ sam build      → build
 ├─ sam package    → package/upload artifacts
 ├─ sam deploy     → package + deploy
 └─ sam publish    → Serverless Application Repository

CloudFormation Lambda
 └─ Code.ZipFile   → inline Python/Node.js

CFN helper scripts
 ├─ cfn-init       → install/configure
 ├─ cfn-signal     → signal success/readiness
 ├─ cfn-get-metadata → retrieve metadata
 └─ cfn-hup        → watch metadata changes

Elastic Beanstalk
 ├─ Blue/Green     → fast rollback / low downtime
 └─ multicontainer → managed container deployment

Lambda
 ├─ VPC            → private resources
 ├─ execution context → reuse initialization/resources
 ├─ /tmp           → temporary local storage
 ├─ custom runtime → unsupported/custom language runtime
 ├─ memory ↑       → CPU ↑
 └─ max duration   → 15 minutes in this practice set

API Gateway
 ├─ custom Lambda integration → explicit mappings
 ├─ Cache-Control:max-age=0   → invalidate cache
 └─ HTTPS                     → invoke API securely

DynamoDB
 ├─ GSI → different PK/SK, eventual consistency only
 ├─ LSI → same PK, alternate SK
 ├─ conditional write → prevent conflicting updates
 ├─ optimistic locking → version attribute
 ├─ high-cardinality PK → avoid hot partitions
 ├─ Streams + Lambda → process changes
 └─ ReturnConsumedCapacity=INDEXES → table/index consumption

Capacity
 ├─ 1 WCU → 1 write/sec up to 1 KB
 ├─ 1 RCU → 1 strong read/sec up to 4 KB
 └─ eventual read → 2 reads per RCU

S3 / CloudFront
 ├─ event notification → upload-triggered Lambda
 ├─ SSE-C → customer-provided key
 ├─ bucket policy → enforce AES256 header
 └─ CloudFront → global low-latency static delivery

Security
 ├─ Secrets Manager → rotation
 ├─ Parameter Store SecureString → secure parameter
 ├─ ssm-secure → CloudFormation secure dynamic reference
 ├─ KMS GenerateDataKey → envelope encryption
 ├─ Lambda Authorizer → custom bearer-token auth
 ├─ Cognito MFA → second authentication factor
 ├─ STS + identity broker → temporary credentials
 ├─ SQL Server TDE → transparent DB encryption
 └─ CloudFront signed URL/cookie → protected content delivery

X-Ray
 ├─ annotation → searchable/filterable
 ├─ metadata → arbitrary, not indexed
 ├─ subsegment → downstream call
 ├─ X-Ray daemon → buffers/sends trace data
 └─ AWSXRayDaemonWriteAccess → daemon upload permission

Troubleshooting
 ├─ cold Kinesis shards → merge
 ├─ >15 min Lambda → timeout
 ├─ SQL injection/XSS → WAF
 ├─ RDS process-level metrics → Enhanced Monitoring
 ├─ CodePipeline human gate → manual approval + SNS
 └─ SQS deduplication → FIFO + deduplication IDs
```

### 6. Fast Exam Triggers

| Question wording | Think of |
|---|---|
| "Build SAM locally" | `sam build` |
| "Package and deploy SAM" | `sam deploy` |
| "Inline Lambda code in CloudFormation" | `Code.ZipFile` |
| "Install packages/create files/start services" | `cfn-init` |
| "Fastest EB rollback / least availability impact" | Blue/Green |
| "Lambda accesses private RDS" | Lambda VPC |
| "Request/response mapping in Lambda integration" | Lambda custom integration |
| "Keep cache current after DB writes" | Write-through |
| "Concurrent updates overwrite each other" | Optimistic locking |
| "Automatic secret rotation" | Secrets Manager |
| "SQL directly on S3" | Athena |
| "GSI consistency" | Eventual only |
| "LSI on existing table" | Create new table + migrate |
| "Avoid hot partitions" | High-cardinality/uniform partition key |
| "Temporary Lambda disk" | `/tmp` |
| "Unsupported Lambda language" | Custom runtime |
| "Cross-device user data" | Cognito Sync |
| "Application data sync/API layer" | AppSync |
| "DynamoDB change → Lambda" | Streams + event source mapping |
| "CodePipeline state → Lambda" | EventBridge |
| "SQL injection/XSS" | AWS WAF |
| "Kinesis underutilized shards" | Merge cold shards |
| "Kinesis workers after resharding" | Up to number of open shards |
| "Detailed RDS OS/process metrics" | Enhanced Monitoring |
| "Human approval in CI/CD" | CodePipeline manual approval + SNS |
| "Search/filter X-Ray traces" | Annotations |
| "Downstream trace detail" | Subsegments |
| "X-Ray daemon permissions" | `AWSXRayDaemonWriteAccess` |
| "CacheControl max-age=0" | API Gateway cache invalidation |
| "API connection refused using HTTP" | Use HTTPS |

### 7. Tutorials Dojo Cheat Sheets

- AWS SAM: https://tutorialsdojo.com/aws-serverless-application-model-sam/
- AWS Elastic Beanstalk: https://tutorialsdojo.com/aws-elastic-beanstalk/
- AWS CloudFormation: https://tutorialsdojo.com/aws-cloudformation/
- AWS Lambda: https://tutorialsdojo.com/aws-lambda/
- Amazon API Gateway: https://tutorialsdojo.com/amazon-api-gateway/
- Amazon ElastiCache: https://tutorialsdojo.com/amazon-elasticache/
- Amazon DynamoDB: https://tutorialsdojo.com/amazon-dynamodb/
- DynamoDB Scan vs Query: https://tutorialsdojo.com/dynamodb-scan-vs-query/
- Calculating DynamoDB RCU/WCU: https://tutorialsdojo.com/calculating-the-required-read-and-write-capacity-unit-for-your-dynamodb-table/
- Amazon S3: https://tutorialsdojo.com/amazon-s3/
- Amazon CloudFront: https://tutorialsdojo.com/amazon-cloudfront/
- Amazon Cognito: https://tutorialsdojo.com/amazon-cognito/
- AWS Step Functions: https://tutorialsdojo.com/aws-step-functions/
- AWS Cloud Development Kit (CDK): https://tutorialsdojo.com/aws-cloud-development-kit-cdk/
- AWS Systems Manager: https://tutorialsdojo.com/aws-systems-manager/
- AWS Secrets Manager: https://tutorialsdojo.com/aws-secrets-manager/
- AWS IAM: https://tutorialsdojo.com/aws-identity-and-access-management-iam/
- Amazon Athena: https://tutorialsdojo.com/amazon-athena/
- Amazon Kinesis: https://tutorialsdojo.com/amazon-kinesis/
- AWS X-Ray: https://tutorialsdojo.com/aws-x-ray/
- AWS WAF: https://tutorialsdojo.com/aws-waf/
- AWS CodePipeline: https://tutorialsdojo.com/aws-codepipeline/
- Amazon RDS: https://tutorialsdojo.com/amazon-relational-database-service-amazon-rds/

---


---

## 8. Final Review Focus

This set was completed at 100%. The final pass should focus on rapid recall of:

1. **SAM / CloudFormation commands and helper scripts**
2. **Lambda execution model, VPC, `/tmp`, custom runtimes, concurrency**
3. **DynamoDB GSI/LSI, conditional writes, optimistic locking, partition-key design, and RCU/WCU calculations**
4. **API Gateway integration types, caching, and HTTPS**
5. **Secrets Manager, KMS envelope encryption, Cognito, STS, and TDE**
6. **X-Ray annotations/subsegments/daemon**
7. **Kinesis shard scaling, WAF, RDS Enhanced Monitoring, and CodePipeline manual approval**

---

### Revision Rule

For the exam, prioritize **service selection from the scenario wording**, then verify the relevant command, property, limit, or consistency rule. The **Fast Exam Triggers** section is designed for the final revision pass.

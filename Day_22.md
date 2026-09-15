# CDA Practice — Review Mode Bonus Set 6

## Practice Result

| Area | Score |
|---|---:|
| Deployment | **4/4 (100%)** |
| Development with AWS Services | **5/5 (100%)** |
| Security | **5/5 (100%)** |
| Troubleshooting & Optimization | **7/8 (87.5%)** |
| **Total** | **21/22 (95.45%)** |

This revision file converts the practice questions into **topic-wise exam notes** rather than repeating all 22 questions.

---

# 1. Deployment

## 1.1 CloudFormation Package + Deploy

When a CloudFormation template references **local artifacts**, such as Lambda source code, package the artifacts before deployment.

### Flow

```text
Local Lambda code
      ↓
aws cloudformation package
      ↓
Artifacts uploaded to S3
      ↓
Packaged template
      ↓
aws cloudformation deploy
```

### Commands

```bash
aws cloudformation package
aws cloudformation deploy
```

### Important Distinctions

| Command | Purpose |
|---|---|
| `validate-template` | Validate template syntax/structure |
| `package` | Upload local artifacts and rewrite references |
| `deploy` | Deploy/update the CloudFormation stack |
| `update-stack` | Update an existing stack |

### Exam Trigger

> Lambda code is local and CloudFormation must deploy it through the AWS CLI.

**Think: `package` → `deploy`**

### Cheat Sheet

- [AWS CloudFormation — Tutorials Dojo](https://tutorialsdojo.com/aws-cloudformation/)

---

## 1.2 CodeArtifact + EventBridge + CodePipeline

**AWS CodeArtifact** is used as a managed repository for software packages and dependencies.

For internal Node.js/npm packages:

```text
CodeArtifact
→ npm packages
```

When a new package version is released:

```text
CodeArtifact event
      ↓
EventBridge
      ↓
CodePipeline
```

### Important Distinction

```text
CodeArtifact → software packages
ECR → container images
S3 → object/file storage
```

### Exam Trigger

> Centralized npm/package repository + automatically start pipeline when a new package version is released.

**Answer: CodeArtifact + EventBridge + CodePipeline**

### Cheat Sheets

- [AWS CodeArtifact Documentation](https://docs.aws.amazon.com/codeartifact/)
- [AWS CodePipeline — Tutorials Dojo](https://tutorialsdojo.com/aws-codepipeline/)
- [Amazon EventBridge — Tutorials Dojo](https://tutorialsdojo.com/amazon-eventbridge/)

---

## 1.3 ALB and `X-Forwarded-For`

When an application is behind an Application Load Balancer, the backend server may see the ALB's IP instead of the original client IP.

The ALB supplies the original client IP through:

```text
X-Forwarded-For
```

### Request Path

```text
Client
  ↓
ALB
  ↓
EC2 / HTTP Server
```

The HTTP server should log:

```text
X-Forwarded-For
```

### Exam Trigger

> EC2 web server logs show the ALB IP instead of the client's public IP.

**Answer: Log `X-Forwarded-For`**

### Cheat Sheets

- [Elastic Load Balancing — Tutorials Dojo](https://tutorialsdojo.com/aws-elastic-load-balancing-elb/)
- [AWS X-Ray — Tutorials Dojo](https://tutorialsdojo.com/aws-x-ray/)

---

## 1.4 AWS AppConfig Feature Flags

AWS AppConfig is designed for application configuration and **feature flags**.

Use it when a new feature must be:

- Enabled/disabled without redeploying the application.
- Gradually introduced.
- Controlled based on conditions.

### Pattern

```text
AWS AppConfig
      ↓
Configuration Profile
      ↓
Feature Flag
      ↓
Enable / Disable
```

### Exam Trigger

> New feature is deployed but must remain hidden until development/testing is complete.

**Answer: AWS AppConfig feature flag**

### Important

Do not build a custom Lambda + Parameter Store solution when AWS AppConfig directly provides feature-flag functionality.

### Cheat Sheet

- [AWS AppConfig — Tutorials Dojo](https://tutorialsdojo.com/aws-appconfig/)

---

# 2. Development with AWS Services

## 2.1 Step Functions Data Flow — `ResultPath`

AWS Step Functions provides fields to control JSON input/output:

```text
InputPath
Parameters
ResultPath
OutputPath
```

### InputPath

Filters the **input** passed to a state.

```text
Input → InputPath → State
```

### Parameters

Creates a specific set of key-value pairs from static values and/or input data.

### ResultPath

Controls how the state's **result is combined with the input**.

```text
Input + State Result
        ↓
    ResultPath
        ↓
      Output
```

### OutputPath

Filters the **state output** before passing it to the next state.

### Quick Comparison

| Field | Main Purpose |
|---|---|
| `InputPath` | Filter input |
| `Parameters` | Build/modify input |
| `ResultPath` | Combine input + result |
| `OutputPath` | Filter output |

### Exam Trigger

> Final output must contain both the original input and the result of the state.

**Think: `ResultPath`**

### Cheat Sheet

- [AWS Step Functions — Tutorials Dojo](https://tutorialsdojo.com/aws-step-functions/)

---

## 2.2 AWS CLI `--dry-run`

For supported AWS CLI commands, `--dry-run` can check whether the caller has permission without performing the actual operation.

Example:

```bash
aws ec2 describe-instances --dry-run
```

### Typical Results

If authorized:

```text
DryRun-Operation
```

If not authorized:

```text
UnauthorizedOperation
```

### Important Distinctions

```text
--dry-run
→ Test authorization

--filters
→ Filter returned resources

--max-items
→ Limit number of returned items

--generate-cli-skeleton
→ Generate command input template
```

### Exam Trigger

> Check whether an AWS API action is permitted without actually executing it.

**Think: `--dry-run`**

---

## 2.3 Elastic Beanstalk Configurable Resources

Elastic Beanstalk manages applications while provisioning underlying AWS resources.

The practice set identifies these as configurable/associated resources:

- Amazon EC2
- CloudWatch
- Application Load Balancer

### Think

```text
Elastic Beanstalk
→ Compute
→ Load balancing
→ Auto Scaling
→ Monitoring
```

### Not Directly Configured as the EB Web-Environment Resources in This Question

- Lambda
- Athena
- CloudFront

### Exam Trigger

> Which AWS resources can Elastic Beanstalk configure/manage for the web environment?

Think:

**EC2 + ALB + CloudWatch**

### Cheat Sheet

- [AWS Elastic Beanstalk — Tutorials Dojo](https://tutorialsdojo.com/aws-elastic-beanstalk/)

---

## 2.4 EC2 Instance Metadata

The EC2 Instance Metadata Service exposes information about the running instance from inside the instance.

The path used in this practice set is:

```text
http://169.254.169.254/latest/meta-data/
```

### Important

```text
/latest/meta-data/
→ Instance metadata

/latest/user-data/
→ User data
```

### Memory Hook

```text
169.254.169.254
→ EC2 metadata
```

### Exam Trigger

> A script running inside EC2 needs instance information such as local IP/hostname.

**Think: Instance Metadata**

### Cheat Sheet

- [Amazon EC2 — Tutorials Dojo](https://tutorialsdojo.com/amazon-elastic-compute-cloud-amazon-ec2/)

---

## 2.5 Lambda Execution Role

A Lambda function uses its **execution role** to obtain permissions for calling other AWS services.

Example:

```text
Lambda
  ↓
Execution Role
  ↓
events:PutEvents
  ↓
EventBridge
```

### Scenario

If Lambda receives:

```text
AccessDeniedException
```

while calling:

```text
PutEvents
```

check the Lambda **execution role**.

### Important Distinction

```text
Developer IAM role
→ Permissions for the human/developer

Lambda execution role
→ Permissions for the Lambda function
```

### Exam Trigger

> Lambda must publish events to EventBridge.

Think:

**Grant `events:PutEvents` to the Lambda execution role**

### Cheat Sheets

- [AWS Lambda — Tutorials Dojo](https://tutorialsdojo.com/aws-lambda/)
- [Amazon EventBridge — Tutorials Dojo](https://tutorialsdojo.com/amazon-eventbridge/)

---

# 3. Security

## 3.1 KMS Envelope Encryption

Envelope encryption uses a **data key** to encrypt application data.

### Encryption

```text
Plaintext Data
      ↓
Plaintext Data Key
      ↓
Encrypted Data

Data Key
      ↓
KMS key / master key
      ↓
Encrypted Data Key
```

### Critical Rule

Use the **plaintext data key** to encrypt the actual application data.

Do not use the ciphertext/encrypted data key for local encryption.

### Why?

KMS keys are used to protect data keys rather than directly encrypting large amounts of application data.

### Exam Trigger

> Encrypt large/local application data using KMS envelope encryption.

**Generate a data key and use its plaintext form to encrypt the data locally.**

### Cheat Sheet

- [AWS KMS — Tutorials Dojo](https://tutorialsdojo.com/aws-key-management-service-aws-kms/)

---

## 3.2 Parameter Store — Advanced Parameters

Parameter Store has Standard and Advanced parameters.

Parameter policies such as:

```text
Expiration
ExpirationNotification
NoChangeNotification
```

are associated with **Advanced Parameters** in the practice set.

### `Expiration`

Deletes the parameter at a specified time.

### `ExpirationNotification`

Generates a notification event when the expiration time is reached.

### `NoChangeNotification`

Generates an event when a parameter has not changed for the specified period.

### Example

```text
Advanced Parameter
      ↓
ExpirationNotification
      ↓
EventBridge
      ↓
SNS
      ↓
Email notification
```

### Exam Trigger

> User-managed passwords must expire after 35 days and users should be notified before expiration, with minimal development.

**Think: Advanced Parameter + `Expiration` + `ExpirationNotification`**

### Important Distinction

```text
NoChangeNotification
→ "Parameter has not changed"

ExpirationNotification
→ "Parameter is approaching expiration"
```

---

## 3.3 ALB + OIDC Authentication

Application Load Balancer supports authentication with an OpenID Connect (OIDC) identity provider.

### Architecture

```text
User
 ↓
Internet-facing ALB
 ↓
OIDC authentication
 ↓
Application
```

### Important

Use:

```text
HTTPS listener
Port 443
```

### Why ALB?

ALB operates at Layer 7 and supports listener-level authentication.

### Exam Trigger

> Existing website must use an OIDC identity provider without modifying the application code.

**Answer: Internet-facing ALB + HTTPS/443 + OIDC authentication**

### Not NLB

Network Load Balancer operates at Layer 4 and does not provide the same built-in OIDC listener authentication.

### Cheat Sheet

- [Elastic Load Balancing — Tutorials Dojo](https://tutorialsdojo.com/aws-elastic-load-balancing-elb/)

---

## 3.4 IAM Identity Center — Temporary Credentials

IAM Identity Center provides temporary security credentials for CLI/SDK access.

A previously working setup may suddenly fail because temporary credentials have expired.

### Pattern

```text
IAM Identity Center
      ↓
Temporary Credentials
      ↓
CLI / SDK
      ↓
AWS APIs
```

If they expire:

```text
Access denied / authentication failure
```

### Exam Trigger

> SSO/CLI worked for weeks, no configuration changed, then API calls start failing.

**Think: Expired temporary credentials → re-authenticate**

---

## 3.5 CloudWatch Logs + KMS

Existing CloudWatch Logs log groups can be associated with a KMS key.

The command tested in this set is:

```bash
aws logs associate-kms-key
```

### Exam Trigger

> Existing CloudWatch Logs group needs KMS encryption for new log data with minimal administration.

**Answer: `aws logs associate-kms-key`**

### Cheat Sheet

- [Amazon CloudWatch — Tutorials Dojo](https://tutorialsdojo.com/amazon-cloudwatch/)
- [AWS KMS — Tutorials Dojo](https://tutorialsdojo.com/aws-key-management-service-aws-kms/)

---

# 4. Troubleshooting and Optimization

## 4.1 Lambda Throttling

Lambda throttling occurs when invocation concurrency exceeds available concurrency limits.

### Typical Symptoms

```text
Traffic increases
      ↓
Concurrent executions increase
      ↓
Concurrency limit reached
      ↓
Invocations throttled
```

### Solutions Tested

#### Exponential Backoff

Retry with increasing delays.

```text
Retry
 ↓
Wait longer
 ↓
Retry
 ↓
Wait longer
```

#### Reserved Concurrency

Reserve concurrency for an individual function.

Useful to ensure predictable capacity for that function.

#### Service Quota Increase

Request a higher concurrency quota when workload requires more capacity.

### Exam Trigger

> Lambda suddenly receives many throttled invocations because traffic increased.

Think:

**Exponential backoff + Reserved Concurrency + Quota Increase**

### Important

Do not confuse:

```text
Throttling
→ concurrency problem
```

with:

```text
Timeout
→ function execution exceeded timeout
```

### Cheat Sheet

- [AWS Lambda — Tutorials Dojo](https://tutorialsdojo.com/aws-lambda/)

---

## 4.2 Step Functions for Lambda Coordination

When many Lambda functions call each other directly, coordination becomes difficult.

Problems include:

- Complex dependencies
- Duplicate orchestration code
- Harder error handling
- Difficult troubleshooting

Use:

```text
AWS Step Functions
```

### Architecture

```text
Step Functions
 ├── Task → Lambda A
 ├── Task → Lambda B
 ├── Task → Lambda C
 └── Task → Lambda D
```

### Benefits

- Explicit workflow
- State tracking
- Built-in retry/error handling
- Easier orchestration

### Exam Trigger

> Multiple Lambda functions invoke one another and workflow coordination is becoming difficult.

**Think: Step Functions**

### Cheat Sheet

- [AWS Step Functions — Tutorials Dojo](https://tutorialsdojo.com/aws-step-functions/)

---

## 4.3 DynamoDB `ProvisionedThroughputExceededException`

This error indicates that the request rate is too high for the available provisioned throughput.

### Recommended Pattern

```text
Request
  ↓
Throttle
  ↓
Retry
  ↓
Exponential Backoff
  ↓
Retry
```

AWS SDKs already support retries for DynamoDB requests.

### Important

Do not use:

- DAX simply to solve a throughput-exceeded error.
- An LSI as a way to increase table write/read capacity.

### Exam Trigger

> `ProvisionedThroughputExceededException`

**Think: Retry + exponential backoff**

### Cheat Sheet

- [Amazon DynamoDB — Tutorials Dojo](https://tutorialsdojo.com/amazon-dynamodb/)

---

## 4.4 ElastiCache Redis vs Memcached

For a cache in front of an RDS database, the question requires:

- Sub-millisecond latency
- Replication
- High availability

The tested answer is:

**Amazon ElastiCache for Redis**

### Comparison

| Feature | Redis | Memcached |
|---|---|---|
| In-memory cache | ✅ | ✅ |
| Sub-millisecond response | ✅ | ✅ |
| Replication | ✅ | ❌ |
| High availability architecture | ✅ | More limited |

### Architecture

```text
Application
    ↓
ElastiCache Redis
    ↓
RDS
```

### Exam Trigger

> RDS caching + sub-millisecond latency + replication/high availability.

**Think: ElastiCache Redis**

### Cheat Sheet

- [Amazon ElastiCache — Tutorials Dojo](https://tutorialsdojo.com/amazon-elasticache/)

---

## 4.5 AWS X-Ray for Distributed Latency

When the problem is:

```text
Which downstream service is causing latency?
```

use **AWS X-Ray**.

### Pattern

```text
Lambda
 ↓
X-Ray SDK
 ↓
HTTP/API/AWS SDK calls
 ↓
X-Ray
 ↓
Service Map
 ↓
Latency analysis
```

X-Ray helps trace requests across multiple components and identify latency bottlenecks.

### Exam Trigger

> Need to determine whether latency comes from S3, another API, or another downstream component.

**Think: X-Ray**

### Why not only CloudWatch Logs?

Logs can show timings, but X-Ray provides distributed tracing and service relationships.

### Cheat Sheet

- [AWS X-Ray — Tutorials Dojo](https://tutorialsdojo.com/aws-x-ray/)

---

## 4.6 Step Functions Retry

Step Functions has built-in retry handling.

Use the:

```text
Retry
```

field on a state.

### Example

```json
"Retry": [
  {
    "ErrorEquals": ["States.Timeout"],
    "MaxAttempts": 3,
    "IntervalSeconds": 2,
    "BackoffRate": 2.0
  }
]
```

### Important Distinction

```text
TimeoutSeconds
→ Defines how long the state may run

Retry
→ Defines what happens after a retryable error
```

### Exam Trigger

> Lambda task times out during spikes and should automatically run again.

**Think: Step Functions `Retry`**

---

## 4.7 SQS Dead-Letter Queue

When Lambda processes messages from SQS and some messages repeatedly fail, use a:

**Dead-Letter Queue (DLQ)**

### Architecture

```text
SQS Main Queue
      ↓
Lambda
      ↓
Processing failure
      ↓
DLQ
```

### Purpose

- Isolate failed messages.
- Prevent endless processing attempts.
- Allow later analysis and remediation.
- Reduce operational overhead.

### Exam Trigger

> SQS + Lambda + repeated processing failures + need to analyze failed messages.

**Think: SQS DLQ**

### Important Distinction

```text
Visibility Timeout
→ Controls how long a received message stays hidden

DLQ
→ Stores messages that repeatedly fail
```

### Cheat Sheet

- [Amazon SQS — Tutorials Dojo](https://tutorialsdojo.com/amazon-sqs/)

---

## 4.8 Lambda `ConcurrentExecutions` Metric

The practice set uses the CloudWatch Lambda metric:

```text
ConcurrentExecutions
```

to assess the concurrency/capacity behavior of Lambda.

### Meaning

It measures the number of Lambda function instances executing concurrently.

### Why Important?

Higher concurrent execution indicates that more function invocations are being handled at the same time.

### Exam Trigger

> Monitor Lambda execution capacity/concurrency using CloudWatch.

**Think: `ConcurrentExecutions`**

### Important Distinction

The metric measures **concurrency**, not a literal message-count-per-second throughput metric.

---

# 5. High-Value Exam Comparisons

## CloudFormation CLI

```text
Local artifacts
→ package
→ deploy
```

---

## Step Functions JSON

```text
InputPath
→ Filter input

Parameters
→ Build/modify input

ResultPath
→ Combine input + result

OutputPath
→ Filter output
```

---

## AWS Credentials

```text
EC2 application
→ IAM Role

IAM Identity Center CLI/SDK
→ Temporary credentials

On-prem application
→ Programmatic credentials
```

---

## Secrets

```text
Secure shared configuration
→ Parameter Store SecureString

Automatic secret rotation
→ Secrets Manager
```

---

## S3 Encryption

```text
SSE-S3
→ S3-managed key

SSE-KMS
→ KMS key

SSE-C
→ Customer-provided key
```

---

## KMS

```text
GenerateDataKey
→ Plaintext data key for local encryption

Decrypt
→ Recover plaintext data key from encrypted data key
```

---

## API Authorization

```text
Bearer token / JWT / OAuth
→ TOKEN Lambda Authorizer

Headers / Query parameters
→ REQUEST Lambda Authorizer
```

---

## Monitoring and Troubleshooting

```text
Distributed application latency
→ X-Ray

Lambda concurrency
→ ConcurrentExecutions

EC2 system metrics
→ CloudWatch Agent

SQS repeated failures
→ DLQ

DynamoDB throttling
→ Retry + exponential backoff

Lambda throttling
→ Backoff + Reserved Concurrency + Quota
```

---

# 6. Service Selection Memory Map

```text
Package repository
→ CodeArtifact

Feature flags
→ AppConfig

Workflow orchestration
→ Step Functions

Lambda permission to AWS services
→ Execution Role

EC2 application permission
→ IAM Role

Policy testing
→ IAM Policy Simulator

Shared SecureString
→ Parameter Store

Secret rotation
→ Secrets Manager

Dedicated HSM
→ CloudHSM

SQL Server database encryption
→ TDE

Protected CDN content
→ CloudFront signed URLs/cookies
```

---

# 7. Final Exam Triggers

| Keyword / Symptom | Answer |
|---|---|
| Local Lambda artifacts + CloudFormation CLI | `cloudformation package` → `deploy` |
| npm/package repository | CodeArtifact |
| New package version triggers pipeline | EventBridge + CodePipeline |
| Original client IP behind ALB | `X-Forwarded-For` |
| Feature toggle | AppConfig |
| Preserve input + task result | `ResultPath` |
| Check CLI permission without executing | `--dry-run` |
| Instance metadata | `169.254.169.254/latest/meta-data/` |
| Lambda publishes to EventBridge | Lambda execution role + `PutEvents` |
| KMS envelope encryption | Plaintext data key encrypts data |
| Password expiration policies | Advanced Parameter |
| Password not changed for N days | `NoChangeNotification` |
| Password nearing expiration | `ExpirationNotification` |
| OIDC without application changes | ALB HTTPS/443 authentication |
| SSO suddenly stops after weeks | Expired temporary credentials |
| Existing CloudWatch Logs + KMS | `associate-kms-key` |
| Lambda throttling | Backoff / Reserved Concurrency / Quota |
| Many Lambdas calling each other | Step Functions |
| DynamoDB throughput exceeded | Retry + exponential backoff |
| RDS cache + replication | ElastiCache Redis |
| Distributed latency troubleshooting | X-Ray |
| Step Functions retry | `Retry` |
| Repeated SQS/Lambda failures | DLQ |
| Lambda concurrency monitoring | `ConcurrentExecutions` |

---

# 8. What This Practice Set Reinforced

The strongest pattern across this review set is **service selection based on the exact requirement**.

```text
Requirement
    ↓
Identify the AWS service layer
    ↓
Choose the native AWS feature
    ↓
Prefer the least-complex solution
    ↓
Apply least privilege where security is involved
```

The most important associations to retain are:

```text
CodeArtifact     → Package repository
AppConfig        → Feature flags
Step Functions   → Workflow + Retry
X-Forwarded-For  → Original client IP
EC2 Metadata     → 169.254.169.254
PutEvents        → Lambda execution role
SecureString     → Parameter Store
Rotation         → Secrets Manager
CloudHSM         → Dedicated HSM
KMS Data Key     → Envelope encryption
ALB + OIDC       → Authentication without app changes
Redis            → Replicated cache
X-Ray            → Distributed tracing
DLQ              → Failed SQS messages
```

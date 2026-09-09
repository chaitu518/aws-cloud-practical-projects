# Day 19 — CDA Revision Notes

## 1. AWS CodeDeploy

### What is CodeDeploy?
AWS CodeDeploy automates application deployments to supported compute platforms.

### Deployment Platforms

| Platform | In-Place | Blue/Green |
|---|---|---|
| EC2 | ✅ | ✅ |
| On-Premises | ✅ | ❌ |
| Lambda | ❌ | ✅ |
| ECS | ❌ | ✅ |

### In-Place Deployment
- Existing instances are updated with the new application version.
- Supported for EC2 and on-premises servers.
- Requires the **CodeDeploy Agent** for EC2/on-premises deployments.

### Blue/Green Deployment
- A new environment/version is deployed separately.
- Traffic is shifted from the old environment to the new environment.
- Makes rollback easier because the old environment can remain available.
- Used with:
  - EC2
  - Lambda
  - ECS

### CodeDeploy Agent
For EC2/on-premises deployments:
- Installed on the target instance.
- Communicates with CodeDeploy.
- Uses outbound HTTPS on port **443**.

### Lambda Lifecycle Hooks

Important hooks for Lambda deployments:

- `BeforeAllowTraffic`
- `AfterAllowTraffic`

These are used around the point where traffic is shifted to the new Lambda version.

### Exam Pattern
When the question says:

> "Deploy the new version without immediately replacing the existing environment and allow easy rollback."

Think **Blue/Green deployment**.

---

# 2. AWS Serverless Application Model (SAM)

## What is AWS SAM?

AWS SAM (Serverless Application Model) is an extension of AWS CloudFormation used to define and deploy serverless applications.

Common resources include:
- Lambda
- API Gateway
- DynamoDB
- Step Functions
- Event sources

### SAM and CloudFormation

SAM templates are transformed into standard CloudFormation templates.

Key concept:

```yaml
Transform: AWS::Serverless-2016-10-31
```

### Exam Pattern

If the question describes:

> "A simplified way to define serverless resources using CloudFormation."

Think **AWS SAM**.

---

# 3. AWS SAM CLI Commands

## `sam init`

Creates a new SAM application/project.

```bash
sam init
```

Typical use:
- Create project structure
- Select runtime
- Select template

---

## `sam build`

Builds the SAM application and prepares deployment artifacts.

```bash
sam build
```

---

## `sam deploy`

Packages/deploys the SAM application.

```bash
sam deploy
```

Important:
- Packages artifacts when necessary.
- Uploads artifacts to S3.
- Deploys the application through CloudFormation.

---

## `sam package`

Packages the application artifacts and uploads them to S3.

```bash
sam package
```

**Does not perform the final deployment.**

---

## `sam publish`

Publishes a SAM application to the **AWS Serverless Application Repository**.

```bash
sam publish
```

---

## `sam sync`

Synchronizes local application changes with AWS during development.

```bash
sam sync
```

### Command Comparison

| Command | Main Purpose |
|---|---|
| `sam init` | Create project |
| `sam build` | Build application |
| `sam deploy` | Package/deploy application |
| `sam package` | Package/upload artifacts |
| `sam publish` | Publish to Serverless Application Repository |
| `sam sync` | Sync local changes during development |

### Exam Trap

**`sam package` ≠ deployment**

If the question asks which command packages **and deploys**, choose:

```bash
sam deploy
```

---

# 4. Lambda Deployment Package

A Lambda deployment package contains the code and required dependencies needed to execute the function.

Deployment can be done using:
- ZIP-based packages
- Container images

For ZIP-based Lambda functions, dependencies can be included in the deployment package or provided through Lambda Layers.

---

# 5. Lambda Layers

Lambda Layers provide additional code/dependencies that can be shared across Lambda functions.

### Important Numbers

- A function can use up to **5 layers**.
- The combined unzipped size of the function and layers is limited to **250 MB**.
- Layer contents are extracted under:

```text
/opt
```

### Why use Layers?

Useful when:
- Multiple functions share the same libraries.
- Large dependencies should not be duplicated in every function package.
- Dependencies need to be managed separately from application code.

### Exam Trigger

> "Several Lambda functions need the same dependency."

Think **Lambda Layer**.

---

# 6. CloudFormation Lambda Function — `ZipFile`

For the CloudFormation resource:

```yaml
AWS::Lambda::Function
```

inline Lambda source code is specified using:

```yaml
Code:
  ZipFile: |
    exports.handler = async (event) => {
      return "Hello";
    };
```

### Important Structure

```text
Code
 └── ZipFile
```

`ZipFile` is a property inside `Code`.

### Important Distinction

Do not confuse:

- `ZipFile` → inline code for `AWS::Lambda::Function`
- `CodeUri` → commonly associated with AWS SAM

### Exam Pattern

If the question specifically asks how to provide **inline Node.js/Python code** in an `AWS::Lambda::Function` CloudFormation resource:

**Answer: `Code.ZipFile`**

---

# 7. CloudFormation Basics

AWS CloudFormation allows infrastructure to be defined as code using templates.

Resources can be created, updated, and deleted as a stack.

### Important Concepts

- **Template** → infrastructure definition
- **Stack** → deployed collection of resources
- **Change Set** → preview of changes
- **StackSet** → deploy/manage stacks across multiple accounts and Regions

---

# 8. CloudFormation Change Sets

A Change Set allows you to preview how a CloudFormation stack will change before executing the update.

### Purpose

Use Change Sets when you want to:

> See what CloudFormation intends to change before applying the update.

### Important

Change Sets:
- Preview changes.
- Do not themselves deploy the changes.
- Help reduce unexpected infrastructure modifications.

### Exam Trap

If the question asks:

> "Preview the changes before updating a CloudFormation stack."

Think **Change Set**.

---

# 9. CloudFormation StackSets

CloudFormation StackSets allow you to create and manage stacks across:

- Multiple AWS accounts
- Multiple AWS Regions

using a single CloudFormation template.

### Exam Trigger

> "Deploy the same CloudFormation infrastructure across multiple accounts and Regions from a centralized location."

Think **CloudFormation StackSets**.

### Change Set vs StackSet

| Feature | Change Set | StackSet |
|---|---|---|
| Preview changes | ✅ | ❌ |
| Multi-account deployment | ❌ | ✅ |
| Multi-Region deployment | ❌ | ✅ |
| Centralized stack management | ❌ | ✅ |

### Stack Instance

A **Stack Instance** represents a stack deployed by a StackSet into a specific target account and Region.

---

# 10. CloudFormation Helper Scripts

CloudFormation provides helper scripts for configuring EC2 instances during stack creation/update.

## `cfn-init`

Used to:

- Install packages
- Create files
- Start/configure services
- Retrieve and interpret CloudFormation metadata

### Think:

```text
cfn-init → configure the instance
```

---

## `cfn-signal`

Used to send a signal indicating that an EC2 instance/resource has completed its configuration or is ready.

Commonly associated with:

- `CreationPolicy`
- `WaitCondition`

### Think:

```text
cfn-signal → tell CloudFormation "I'm ready"
```

---

## `cfn-get-metadata`

Retrieves CloudFormation metadata.

### Think:

```text
cfn-get-metadata → get metadata
```

---

## `cfn-hup`

Monitors changes to CloudFormation metadata and can execute configured hooks when metadata changes.

### Think:

```text
cfn-hup → watch metadata changes
```

### Quick Comparison

| Script | Purpose |
|---|---|
| `cfn-init` | Configure/install/start resources |
| `cfn-signal` | Signal readiness/completion |
| `cfn-get-metadata` | Retrieve metadata |
| `cfn-hup` | Monitor metadata changes |

---

# 11. Elastic Beanstalk Deployment Strategies

Elastic Beanstalk provides multiple deployment strategies.

## All at Once

- Deploys the new version to all instances.
- Fast deployment.
- Can cause downtime/unavailability.

---

## Rolling

- Updates instances in batches.
- Keeps part of the environment running during deployment.
- Slower than All at Once.

---

## Rolling with Additional Batch

Similar to rolling deployment but launches an additional batch of instances first.

This helps maintain capacity during deployment.

---

## Immutable

- Creates a completely new set of instances.
- Deploys the new version there.
- Useful for safer deployments and rollback.

---

## Traffic Splitting

- Sends a configurable percentage of traffic to the new version.
- Gradually evaluates the new version.
- Can help reduce deployment risk.

---

## Blue/Green

- Deploy the new version to a separate environment.
- Switch traffic by swapping environment CNAMEs.
- The old environment remains available for rollback.

### Exam Pattern

If the question emphasizes:

> "Fastest rollback with the least impact on availability."

Think:

**Elastic Beanstalk Blue/Green**

### Deployment Strategy Comparison

| Strategy | Main Idea |
|---|---|
| All at Once | Update everything |
| Rolling | Update instances in batches |
| Rolling + Additional Batch | Rolling with extra capacity |
| Immutable | New instances/environment for deployment |
| Traffic Splitting | Gradually shift traffic |
| Blue/Green | Separate environment + CNAME swap |

---

# 12. Elastic Beanstalk Multi-Container Docker

For Elastic Beanstalk multi-container Docker environments, the important configuration file is:

```text
Dockerrun.aws.json
```

### Exam Trap

Do not confuse this with:

```text
.ebextensions
```

`.ebextensions` is used for Elastic Beanstalk configuration/customization, but the multi-container Docker definition is provided through:

```text
Dockerrun.aws.json
```

### Exam Trigger

> "Configure multiple Docker containers in an Elastic Beanstalk environment."

Think:

**`Dockerrun.aws.json`**

---

# 13. AWS CodeCommit + Elastic Beanstalk

AWS CodeCommit is a managed Git repository service.

It can be used as the source repository for application development and deployment workflows.

### Typical Flow

```text
Developer
   ↓
CodeCommit
   ↓
Elastic Beanstalk
   ↓
Application Environment
```

### Why CodeCommit?

Useful for:
- Git-based source control
- Team collaboration
- Version history
- Incremental application deployment

### Exam Pattern

If the question asks for:

> "A managed Git repository that integrates with AWS deployment services."

Think **CodeCommit**.

---

# 14. DynamoDB Read Capacity Units (RCU)

DynamoDB capacity calculations are important for the exam.

### Read Size

One read request can consume capacity based on the item size.

For a **strongly consistent read**:

```text
1 RCU = 1 strongly consistent read per second for up to 4 KB
```

For an **eventually consistent read**:

```text
1 RCU = 2 eventually consistent reads per second for up to 4 KB
```

Therefore:

```text
Eventually consistent read = 0.5 RCU per 4 KB read
```

---

## RCU Formula

For eventually consistent reads:

```text
RCU =
requests/sec
× ceil(item size / 4 KB)
× 0.5
```

For strongly consistent reads:

```text
RCU =
requests/sec
× ceil(item size / 4 KB)
```

### Example

Suppose:

- Item size = 3.5 KB
- Reads = 150 requests/second
- Eventually consistent reads

Since:

```text
3.5 KB ≤ 4 KB
```

Each request consumes:

```text
0.5 RCU
```

Therefore:

```text
150 × 0.5 = 75 RCU
```

### Exam Trap

Always apply:

```text
ceil(item size / 4 KB)
```

before multiplying by requests per second.

---

# 15. EBS Root Volume

The root EBS volume is attached to the EC2 instance as the root device.

To detach the root EBS volume safely, the EC2 instance must be **stopped** first.

### Exam Pattern

If the question asks:

> "What must be done before detaching the root EBS volume?"

Think:

**Stop the EC2 instance.**

---

# 16. AWS CodeDeploy — Platform/Deployment Matrix

This is one of the most useful tables to memorize.

| Service/Platform | Deployment Type |
|---|---|
| EC2 | In-Place / Blue-Green |
| On-Premises | In-Place |
| Lambda | Blue-Green |
| ECS | Blue-Green |

### Memory Rule

```text
EC2        → In-Place + Blue/Green
On-Prem    → In-Place
Lambda     → Blue/Green
ECS        → Blue/Green
```

---

# 17. High-Yield Exam Distinctions

## CodeDeploy vs Elastic Beanstalk Blue/Green

### CodeDeploy
Used to automate application deployments to supported compute platforms.

### Elastic Beanstalk
Provides deployment strategies for applications managed by Elastic Beanstalk.

Both can use **Blue/Green**, but the implementation/context differs.

---

## SAM vs CloudFormation

```text
SAM
 ↓
CloudFormation extension
 ↓
CloudFormation deployment
```

SAM simplifies serverless application definitions.

---

## `sam package` vs `sam deploy`

```text
sam package
    ↓
Package + upload artifacts
```

```text
sam deploy
    ↓
Package/upload when needed
    ↓
Deploy
```

---

## Change Set vs StackSet

```text
Change Set
→ Preview changes
```

```text
StackSet
→ Deploy/manage stacks
  across accounts and Regions
```

---

## cfn-init vs cfn-signal

```text
cfn-init
→ Configure instance
```

```text
cfn-signal
→ Signal completion/readiness
```

---

# 18. Common Exam Traps

### Trap 1
**Question:** Deploy the same CloudFormation template to multiple accounts and Regions.

**Answer:** StackSets

---

### Trap 2
**Question:** Preview CloudFormation changes before execution.

**Answer:** Change Set

---

### Trap 3
**Question:** Inline Lambda code in `AWS::Lambda::Function`.

**Answer:** `Code.ZipFile`

---

### Trap 4
**Question:** Package and deploy a SAM application.

**Answer:** `sam deploy`

---

### Trap 5
**Question:** Package/upload SAM artifacts without deployment.

**Answer:** `sam package`

---

### Trap 6
**Question:** Configure an Elastic Beanstalk multi-container Docker application.

**Answer:** `Dockerrun.aws.json`

---

### Trap 7
**Question:** Configure an EC2 instance using CloudFormation metadata.

**Answer:** `cfn-init`

---

### Trap 8
**Question:** Tell CloudFormation that instance configuration has completed.

**Answer:** `cfn-signal`

---

### Trap 9
**Question:** Monitor CloudFormation metadata changes.

**Answer:** `cfn-hup`

---

### Trap 10
**Question:** Fast rollback and minimal availability impact in Elastic Beanstalk.

**Answer:** Blue/Green

---

# 19. Day 19 — Final Memory Map

```text
CODEDEPLOY
│
├── EC2
│   ├── In-Place
│   └── Blue/Green
│
├── On-Premises
│   └── In-Place
│
├── Lambda
│   └── Blue/Green
│
└── ECS
    └── Blue/Green
```

```text
SAM CLI
│
├── sam init     → Create
├── sam build    → Build
├── sam deploy   → Deploy
├── sam package  → Package + Upload
├── sam publish  → Serverless Application Repository
└── sam sync     → Development sync
```

```text
CLOUDFORMATION
│
├── Stack
├── Change Set
│   └── Preview changes
│
├── StackSet
│   └── Multi-account + Multi-Region
│
└── Helper Scripts
    ├── cfn-init
    ├── cfn-signal
    ├── cfn-get-metadata
    └── cfn-hup
```

```text
ELASTIC BEANSTALK
│
├── All at Once
├── Rolling
├── Rolling + Additional Batch
├── Immutable
├── Traffic Splitting
└── Blue/Green
    └── Separate environment + CNAME swap
```

```text
LAMBDA
│
├── Deployment Package
├── Layers
│   ├── Max 5 layers
│   ├── 250 MB combined unzipped limit
│   └── /opt
│
└── CloudFormation
    └── Code.ZipFile
```

```text
DYNAMODB
│
└── RCU
    ├── Strong → 1 RCU / 4 KB
    └── Eventual → 0.5 RCU / 4 KB
```

## Core Exam Triggers

| If you see... | Think... |
|---|---|
| Multi-account + multi-Region CloudFormation | **StackSets** |
| Preview CloudFormation update | **Change Set** |
| Inline Lambda code | **Code.ZipFile** |
| Package + deploy SAM | **sam deploy** |
| Package/upload only | **sam package** |
| Configure EC2 from CFN metadata | **cfn-init** |
| Signal EC2 readiness | **cfn-signal** |
| Watch metadata changes | **cfn-hup** |
| Multi-container Docker on EB | **Dockerrun.aws.json** |
| Fast rollback on EB | **Blue/Green** |
| Shared Lambda dependencies | **Lambda Layers** |
| Managed Git repository | **CodeCommit** |
| Root EBS detachment | **Stop EC2 first** |
| Lambda deployment lifecycle traffic hook | **BeforeAllowTraffic / AfterAllowTraffic** |


# 20. Cheat Sheets & Quick References

Use these links for fast revision of the Day 19 topics.

## Deployment & CI/CD

- [AWS CodeDeploy Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/aws-codedeploy/)
- [AWS CodeCommit Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/aws-codecommit/)
- [AWS Cheat Sheets — Tutorials Dojo](https://tutorialsdojo.com/aws-cheat-sheets/)

## Serverless / SAM / Lambda

- [AWS SAM Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/aws-serverless-application-model-sam/)
- [AWS Lambda Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/aws-lambda/)
- [AWS Serverless Application Repository Cheat Sheet](https://tutorialsdojo.com/aws-serverless-application-repository/)

## Infrastructure as Code

- [AWS CloudFormation Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/aws-cloudformation/)
- [CloudFormation StackSets & Nested Stacks — Tutorials Dojo](https://tutorialsdojo.com/aws-cloudformation-stacksets-and-nested-stacks/)

## Elastic Beanstalk

- [AWS Elastic Beanstalk Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/aws-elastic-beanstalk/)

## DynamoDB

- [Amazon DynamoDB Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/amazon-dynamodb/)
- [Calculating Required RCU and WCU — Tutorials Dojo](https://tutorialsdojo.com/calculating-required-rcu-and-wcu-for-your-dynamodb-table/)

## AWS CDA Study Reference

- [AWS Certified Developer Associate — Tutorials Dojo Study Path](https://tutorialsdojo.com/aws-certified-developer-associate-exam-guide-study-path-dva-c02/)
- [AWS Certified Developer Associate Study Guide & Cheat Sheets](https://portal.tutorialsdojo.com/product/tutorials-dojo-study-guide-and-cheat-sheets-aws-certified-developer-associate/)

> **Revision tip:** Use the cheat sheet first for quick recall, then return to the Day 19 notes for the exam traps, comparisons, commands, and calculations.

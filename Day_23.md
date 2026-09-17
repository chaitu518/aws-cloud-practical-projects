# CDA Review Mode Set 5 — Revision Notes

**Source:** Tutorials Dojo — Review Mode Set 5, AWS Certified Developer Associate DVA-C02 Practice Exams 2026  
**Attempt date:** September 17, 2026

## Practice Result

| Category | Questions | Correct | Score |
|---|---:|---:|---:|
| CDA - Deployment | 9 | 9 | 100% |
| CDA - Development with AWS Services | 27 | 24 | 88.89% |
| CDA - Security | 13 | 10 | 76.92% |
| CDA - Troubleshooting and Optimization | 16 | 12 | 75% |
| **Total** | **65** | **55** | **84.62%** |

---

# 1. CDA - Deployment

## CodeDeploy — DownloadBundle failures

`DownloadBundle` is an EC2/on-premises CodeDeploy lifecycle event handled by the CodeDeploy Agent.

Important causes from the practice source:

- The EC2 instance profile does not have permission to access the application revision in S3.
- The S3 bucket and target instances can be in different Regions, which can also cause the event to fail.
- An S3 internal error can also cause the failure.
- `DownloadBundle` itself is **not manually configured in the AppSpec file**.
- S3 versioning is not required simply for `DownloadBundle` to work.

**Exam trigger:** `DownloadBundle` + `UnknownError: not opened for reading` → check the EC2 instance IAM permissions to the S3 application bundle first.

## CodeDeploy automatic rollback

For an **in-place deployment**, when automatic rollback is configured and a deployment fails:

- CodeDeploy redeploys the **last known good application revision**.
- The rollback is technically a **new deployment**.
- It receives a **new deployment ID**.
- It does not restore an EC2 AMI snapshot.
- Blue/green traffic switching is not the mechanism for an in-place rollback.

**Memory:**

`Rollback → last known good revision → new deployment → new deployment ID`

## CodeDeploy deployment groups for environments

For Test → Staging → Production deployments, use **multiple deployment groups** under the same CodeDeploy application.

Example:

```text
CodeDeploy Application
   ├── Test Deployment Group
   ├── Staging Deployment Group
   └── Production Deployment Group
```

This allows the same application revision to be promoted through different target sets at different times.

**Exam trigger:** Sequential environment promotion with CodeDeploy → **multiple deployment groups**.

## Elastic Beanstalk — Multi-container Docker

For a multi-container Docker environment on Elastic Beanstalk, define container configuration in:

```text
Dockerrun.aws.json
```

The file belongs in the application source bundle at the appropriate root level; it is not a `.config` file inside `.ebextensions`.

**Do not confuse:**

- `Dockerrun.aws.json` → Docker container definitions for Elastic Beanstalk.
- `.ebextensions/*.config` → Elastic Beanstalk environment/resource configuration.
- `.elasticbeanstalk/` → EB CLI/environment configuration.

## Elastic Beanstalk + CodeCommit

For collaborative development where teams should deploy **incremental code changes** without repeatedly uploading the entire project:

- Use an AWS CodeCommit repository.
- Allow the development team to collaborate through Git.
- Deploy the application to Elastic Beanstalk from the repository.

The practice source emphasizes that CodeCommit supports collaborative source control and that deployment can upload only changes rather than repeatedly sending the whole project.

**Exam trigger:** Collaborative source control + incremental deployment to EB → **CodeCommit + Elastic Beanstalk**.

## Deployment prompts with incomplete source explanations

The uploaded practice file contains prompts for Deployment Q6–Q9 but does not include their answer choices/explanations in the pasted content. They are therefore **not expanded here** rather than being inferred.

- Q6: CDK deployment to a new AWS account returns `NoSuchBucket`.
- Q7: Extract custom metrics from Lambda logs and alarm on them.
- Q8: API Gateway + Lambda + S3-backed daily data has increased latency.
- Q9: Local SAM Python application is ready after `requirements.txt`; asks for deployment steps.

---

# 2. CDA - Development with AWS Services

## Lambda Context object — finding the log stream

Lambda passes a `Context` object to the handler.

The practice source highlights:

```text
context.log_stream_name
```

This gives the log stream associated with the function instance.

**Exam trigger:** “Return the log location” with the **least effort** → use `context.log_stream_name`; do not make an extra `FilterLogEvents` API call unless the requirement specifically asks you to search log contents.

## API Gateway stages + Lambda versions

When users need time to migrate from an old API version to a new one:

1. Update the Lambda function.
2. Publish a new Lambda version.
3. Point the API Gateway integration at the desired Lambda version.
4. Deploy the API to a **new API Gateway stage**.

Stages are logical lifecycle environments such as `dev`, `prod`, `beta`, or `v2`.

**Why a new stage?** It allows old and new endpoints to coexist while clients migrate.

**Memory:**

`Lambda version = backend version`  
`API Gateway stage = API endpoint/lifecycle environment`

## Elastic Beanstalk `.ebextensions`

Custom Elastic Beanstalk configuration files use the `.config` extension and belong in:

```text
.ebextensions/
```

Example:

```text
MyApp/
├── .ebextensions/
│   └── tutorialsdojo.config
├── package.json
└── application files...
```

**Exam trigger:** EB environment customization + `.config` file → `.ebextensions`.

## Lambda `/tmp` ephemeral storage

Lambda provides ephemeral writable storage through:

```text
/tmp
```

The practice source uses `/tmp` for staging and unzipping a relatively small compressed file.

Key points from the source:

- `/tmp` is temporary storage inside the Lambda execution environment.
- The source states a configurable range of **512 MB to 10,240 MB**.
- The first 512 MB is described as free; storage configured above that is billed.
- For a small workload, `/tmp` avoids the additional setup of EFS/EBS.

**Exam trigger:** Small temporary file processing inside Lambda → consider `/tmp` before introducing EFS/EBS.

## Lambda runtimes — Ruby

Ruby is a **supported Lambda runtime** in the practice source.

Use a native supported Ruby runtime rather than creating a custom runtime.

**Distinction:**

- Supported language → choose the supported Lambda runtime.
- Unsupported language → consider a custom runtime using the Lambda Runtime API/bootstrap approach.

## CloudFront — updating cached objects efficiently

When the requirement is to update many CloudFront-served images immediately and cost-effectively:

- Use **versioned file names** or versioned directory names.
- Update application references to the new object name.

Example:

```text
image_1.jpg → image_2.jpg
```

or:

```text
/images_v1/... → /images_v2/...
```

Advantages highlighted by the source:

- No need to wait for cache expiration.
- Avoids paying for object invalidations.

**Exam trigger:** “Immediately” + “cost-efficient” + CloudFront cache → **versioned filenames**.

## Cognito User Pool + API Gateway authorizer

For a browser-based application using Cognito authentication and JWTs with API Gateway, the source identifies these steps:

1. Create a **Cognito User Pool**.
2. Create an API Gateway authorizer using the User Pool ID.
3. Configure the token source/header, typically the request `Authorization` header.

**Important distinction:**

- **User Pool** → user registration, sign-in, authentication, tokens.
- **Identity Pool** → exchanges identities/tokens for temporary AWS credentials.

A Cognito Identity Pool is not the API Gateway Cognito User Pool authorizer.

## X-Ray — Annotations vs Metadata

To record data that you want to **search/filter/group traces by**, use:

```text
Annotations
```

The practice source describes annotations as indexed key-value pairs usable with filter expressions.

### Annotation
- Indexed.
- Searchable/filterable.
- Useful for grouping traces.

### Metadata
- Not indexed for trace filtering.
- Useful for storing additional contextual information.

### Sampling
- Controls what requests are traced.
- Helps control tracing volume/cost.

### Subsegment
- Gives finer-grained timing/details within a segment.
- Useful for instrumenting a particular block or downstream call.

**Memory:**

`Group/filter traces → Annotation`  
`Store extra detail → Metadata`  
`Reduce trace volume → Sampling`  
`Measure a code block/downstream call → Subsegment`

## S3 Glacier Deep Archive

For long-term retention where data is expected to be accessed very rarely and lowest-cost durable S3 storage is required:

```text
S3 Glacier Deep Archive
```

The practice source frames this as suitable for long-term records with access roughly once or twice a year.

**Exam trigger:** Historical records + archival + lowest cost → **Glacier Deep Archive**.

## CloudFormation regional AMI mapping

An AMI ID is Region-specific. To deploy an application using corresponding AMIs in multiple Regions:

1. Copy the AMI into each target Region.
2. Add a CloudFormation `Mappings` section.
3. Use the Region name as the mapping key.
4. Retrieve the Region-specific AMI ID with:

```yaml
Fn::FindInMap
```

Conceptual example:

```yaml
Mappings:
  RegionMap:
    us-east-2:
      ImageId: ami-xxxxx
    ap-northeast-1:
      ImageId: ami-yyyyy
```

**Do not confuse:**

- `Fn::FindInMap` → values in `Mappings`.
- `Fn::ImportValue` → exported value from another CloudFormation stack.
- `Fn::GetAtt` → resource attribute.
- `Ref` → parameter/resource reference.

## AWS CDK — Infrastructure in Python

When the **infrastructure definition itself** must be written in Python and remain reusable/updateable, use:

```text
AWS CDK
```

The practice source explains that CDK applications use familiar programming languages and synthesize into CloudFormation templates.

Flow:

```text
Python CDK code
      ↓
cdk synth
      ↓
CloudFormation template
      ↓
CloudFormation provisioning
```

**Do not confuse:**

- CDK → infrastructure as code using programming languages.
- CloudFormation → declarative infrastructure definition using JSON/YAML.
- boto3 → Python SDK for calling AWS APIs.
- CloudShell → browser-based shell, not an IaC framework.

## DynamoDB Accelerator (DAX)

For a read-intensive DynamoDB workload with high read latency:

```text
DynamoDB → DAX → application
```

DAX is an in-memory cache specifically designed to accelerate DynamoDB reads.

The source highlights:

- Microsecond-level response times for supported read-intensive workloads.
- Minimal application changes due to API compatibility.
- Managed cache infrastructure.
- Write-through caching is described in the practice explanation.

**Exam trigger:** DynamoDB read latency + caching → **DAX**.

## Step Functions — Catch + ResultPath

When a workflow must capture an error and aggregate a state's input/result information:

- Use `Catch` to handle the error.
- Use `ResultPath` to control how the state's result is combined with input.

A catcher includes fields such as:

```text
ErrorEquals
Next
ResultPath
```

**Do not confuse:**

- `Catch` → error handling.
- `Retry` → retry failed states.
- `ResultPath` → controls where a state's result is placed in the output.
- `Parameters` → builds/modifies input passed to a state; it is not an error-capture mechanism.
- `ItemsPath` → used with Map states.

## Step Functions — Task State

For actual work performed as part of a sequential workflow, use:

```text
Task State
```

The practice source contrasts the states as follows:

- `Task` → perform work.
- `Choice` → branch based on conditions.
- `Pass` → pass/inject data without doing work.
- `Wait` → delay execution.
- `Parallel` → execute branches concurrently.
- `Map` → dynamically iterate over items.
- `Succeed` / `Fail` → terminate execution successfully/unsuccessfully.

**Exam trigger:** Refactoring a long Lambda into sequential executable steps → **Task states**.

## DynamoDB fine-grained item access — `dynamodb:LeadingKeys`

When users must only access DynamoDB items whose partition key matches their own identity, use the IAM condition key:

```text
dynamodb:LeadingKeys
```

The practice scenario uses the user's ID as the table partition key and ties IAM access to that key.

**Memory:**

`Partition-key-based item restriction → dynamodb:LeadingKeys`

## Cognito hosted UI — branding

For Cognito's built-in login experience, the practice source uses the Cognito app/client settings to upload a logo and show it on the hosted/custom login page.

**Exam trigger:** “Use Cognito built-in UI but add company logo” → configure the logo in Cognito app settings rather than building an authentication UI from scratch.

## SQS messages larger than 256 KB

For SQS messages exceeding the normal SQS message size limit, the practice source recommends:

```text
Amazon S3 + Amazon SQS Extended Client Library for Java
```

Conceptually:

```text
Large payload → S3
SQS message → reference/pointer to payload
```

**Exam trigger:** SQS payload > normal SQS limit → think **S3 + SQS Extended Client Library**.

## Lambda Function URL — custom webhook authentication

For a third-party webhook that needs a publicly reachable HTTPS endpoint and provides its own request signature:

- Use a **Lambda Function URL**.
- Configure public invocation with Function URL auth type `NONE` when the endpoint is intended to be public.
- Add custom signature-validation logic inside the Lambda function before executing domain logic.

The practice source uses the resource-based policy condition:

```text
lambda:FunctionUrlAuthType = NONE
```

**Important:** Function URL `NONE` does not itself authenticate the webhook. The application must validate the signature/request before processing.

## Cognito developer-authenticated identities

For applications that already have their **own authentication system** but need Cognito-managed unique identities and AWS resource access, the practice source recommends:

```text
Developer-authenticated identities
```

This lets an existing backend authentication process integrate with Cognito while retaining Cognito identity functionality.

## API Gateway + SOAP service

For exposing a legacy XML/SOAP backend to modern JSON clients:

```text
JSON client
   ↓
API Gateway REST API
   ↓  mapping template
JSON → XML
   ↓
Lambda
   ↓
SOAP service
   ↓
XML response
   ↓
Lambda / API Gateway
   ↓
JSON response
```

The source specifically identifies **mapping templates** for JSON/XML transformation and Lambda as the integration layer for the SOAP service.

**Exam trigger:** JSON ↔ XML transformation in API Gateway → **mapping templates**.

## EventBridge scheduled Lambda invocation

For a serverless recurring Lambda trigger such as once per week:

```text
EventBridge Schedule → Lambda
```

Avoid introducing an EC2 cron job for a basic serverless scheduling requirement.

**Exam trigger:** “Every day/week/month” + serverless → scheduled EventBridge rule.

## DynamoDB transactions — `TransactWriteItems`

For multiple writes that must be **all-or-nothing**:

```text
TransactWriteItems
```

The practice source notes that a transaction can contain write actions such as:

- `Put`
- `Update`
- `Delete`
- `ConditionCheck`

The source also states a limit of up to 25 write actions and an aggregate transaction size limit of 4 MB.

**Do not confuse:**

- `TransactWriteItems` → atomic all-or-nothing writes.
- `BatchWriteItem` → not atomic as one transaction and does not support updates in the same way.
- `Query` / `Scan` → read operations.

## DynamoDB Streams for near-real-time cross-service updates

When changes in one DynamoDB table must drive updates elsewhere:

```text
Customer DynamoDB table
        ↓
DynamoDB Streams
        ↓
Lambda
        ↓
Payment DynamoDB table
```

**Exam trigger:** DynamoDB change events + near-real-time processing → **DynamoDB Streams + Lambda**.

## ALB + Lambda — multi-value query parameters

When the request contains repeated query-string keys and the Lambda target needs all values, enable:

```text
Multi-value headers
```

The practice source specifically places this configuration on the Application Load Balancer target group.

**Exam trigger:** `key=a&key=b&key=c` → **multi-value headers**.

## ECS — sharing storage between containers

For two Docker containers on ECS that need to share log/data files:

- Place the containers in the **same task definition**.
- Configure an **EFS volume** for shared persistent file storage.

Conceptually:

```text
ECS Task Definition
   ├── Container A ─┐
   └── Container B ─┤→ EFS
```

**Do not confuse:** Pods are Kubernetes concepts; the scenario is ECS.

## Development questions with incomplete source explanations

The uploaded set also contains prompts without their answer/explanation content for:

- Q10: EC2 Amazon Linux 2 + public port 80 + quick Apache/Hello World setup.
- Q23: `BatchGetItem` returning `UnprocessedKeys` and asking for the most reliable recovery approaches.

These are intentionally left as **source gaps** rather than inferred.

---

# 3. CDA - Security

## KMS envelope-style application encryption with unique data keys

When every media file needs a unique encryption key but key management should remain low-overhead:

1. Call KMS `GenerateDataKey`.
2. Use the returned plaintext data key to encrypt the file in the application.
3. Store the encrypted file together with the **encrypted data key**.
4. The KMS-protected encrypted data key can later be decrypted through KMS when needed.

**Why:** One customer-managed KMS key can protect many per-object data keys without creating a separate KMS key for every file.

**Exam trigger:** “Unique encryption key per object/file” + KMS → **GenerateDataKey**, not `CreateKey` for every file.

## Cross-account S3 access — AssumeRole

When a development account needs access to an S3 bucket in a production account and sharing long-term credentials is prohibited, use cross-account IAM role assumption.

Conceptual flow:

```text
Development account user
        ↓ sts:AssumeRole
Production account IAM role
        ↓
S3 permissions
        ↓
Production S3 bucket
```

The source's intended three-step structure is:

1. In the **production account**, create an IAM role that trusts the development account.
2. Grant the production-account role the required S3 permissions.
3. In the **development account**, allow the relevant IAM users to call STS `AssumeRole` on the production role.

**Memory:**

`Trust policy → who may assume the role`  
`Permission policy → what the assumed role may do`

## Cognito User Pools + Identity Pools

For an application where users must:

- register/sign in, and
- obtain access to AWS resources such as S3,

use both:

```text
Cognito User Pool + Cognito Identity Pool
```

### User Pool
- User directory.
- Sign-up/sign-in.
- Authentication tokens.

### Identity Pool
- Exchanges authenticated identity information for temporary AWS credentials.
- Connects identities to IAM roles/policies for AWS resource access.

**Exam trigger:** User authentication + direct AWS resource access → **User Pool + Identity Pool**.

## STS — `GetSessionToken` and MFA

The practice scenario asks for temporary credentials for an IAM user and requires MFA enforcement.

Use:

```text
GetSessionToken
```

The source distinguishes STS operations:

- `AssumeRole` → assume an IAM role, commonly cross-account or temporary privilege.
- `AssumeRoleWithWebIdentity` → federated public identity provider/OIDC-style authentication.
- `AssumeRoleWithSAML` → SAML federation.
- `GetFederationToken` → federated users via a proxy/application.
- `GetSessionToken` → temporary credentials for an IAM user and supports MFA.

**Exam trigger:** “IAM user + temporary credentials + MFA” → **GetSessionToken**.

## KMS supported features

The practice source identifies these supported KMS capabilities:

- Re-enable disabled KMS keys.
- Create symmetric and asymmetric KMS keys.

**Exam trap:** Not every custom-key-store operation is available in every form. Read the exact wording of options about imported key material, custom key stores, and automatic rotation carefully.

## API Gateway Usage Plans

When different API consumers/tiers need different quotas and throttles and the API is being exposed for monetized usage:

```text
Usage Plans
```

The source uses three usage plans for three access levels and configures quotas/throttling for each level.

**Memory:**

`Plan = who/which API key gets what quota/throttle`

## IAM access-key best practices

The practice source identifies these actions as best practices:

- Delete root-user access keys.
- Use IAM roles for applications that need AWS service access.

Avoid embedding long-term access keys directly in application code.

**Exam trigger:** AWS application credentials → prefer **IAM roles** over hard-coded access keys.

## CloudWatch custom metrics from EC2 — IAM role

When EC2 instances need to publish custom CloudWatch metrics securely:

- Create an IAM role for the instances/launch template.
- Grant only the required permission, including:

```text
cloudwatch:PutMetricData
```

- Attach the role through the Auto Scaling launch template.

**Why:** No hard-coded credentials in the instance/application.

## STS `decode-authorization-message`

When the AWS CLI returns an `UnauthorizedOperation` and provides an encoded/ciphertext authorization message:

Use the STS CLI command:

```bash
aws sts decode-authorization-message
```

**Exam trigger:** `UnauthorizedOperation` + encoded authorization message → **decode-authorization-message**.

## SSE-KMS throughput/quota issue

When very high-rate S3 `PutObject` requests use SSE-KMS and performance degrades, the practice source identifies a likely cause as:

```text
AWS KMS API request quota exceeded
```

Reasoning:

```text
Huge S3 PUT rate
      ↓
SSE-KMS invokes KMS operations
      ↓
KMS API quota becomes a bottleneck
```

**Exam trigger:** S3 upload throughput suddenly decreases after switching to SSE-KMS → consider **KMS request quotas**.

## IAM access verification — two useful methods

For checking whether an IAM role can call `DescribeInstances`:

### 1. CLI dry run

```bash
aws ec2 describe-instances --dry-run
```

The source states:

- Authorized → `DryRun-Operation`.
- Not authorized → `UnauthorizedOperation`.

### 2. IAM Policy Simulator

Use the IAM Policy Simulator to test whether an IAM role's policies allow the desired action/resource combination.

**Do not confuse:**

- `get-role` → role metadata, not a full permission test.
- `get-group-policy` → group inline policy, not an IAM role permission test.
- EC2 instance metadata → not a general source for inspecting IAM inline policies.

## CDK + SAM local Lambda testing

When resources are defined with CDK L2 constructs but Lambda functions need local testing with SAM:

1. Run:

```bash
cdk synth
```

This generates the CloudFormation representation of the CDK application.

2. Invoke the desired function with:

```bash
sam local invoke
```

using the synthesized template and function identifier.

**Memory:**

`CDK code → cdk synth → CloudFormation template → sam local invoke`

`cdk bootstrap` is for preparing an AWS account for CDK deployment, not for this local-test flow.

## Cognito ID

In a federated Cognito Identity Pool flow, once the IdP token is supplied to Cognito, the source identifies the returned unique identifier as the:

```text
Cognito ID / Identity ID
```

The Cognito Identity Pool can then provide temporary, limited-privilege AWS credentials based on the identity and configured IAM role mappings.

---

# 4. CDA - Troubleshooting and Optimization

## Lambda CloudWatch Logs — execution role permissions

If Lambda executes successfully but no CloudWatch log group/stream/log events appear, check the execution role.

The practice source identifies the required managed policy:

```text
AWSLambdaBasicExecutionRole
```

It provides the core logging permissions such as:

```text
logs:CreateLogGroup
logs:CreateLogStream
logs:PutLogEvents
```

**Exam trigger:** Lambda code has logging statements + execution role has no permissions → attach **AWSLambdaBasicExecutionRole**.

## ECS container instance remains registered

With the ECS EC2 launch type, terminating an EC2 container instance does not automatically mean the ECS container-instance registration disappears immediately.

The practice source states that a terminated container instance in the stopped state can remain represented in the cluster and may need deregistration.

**Exam trigger:** “EC2 container instance terminated but still visible in ECS cluster” → think **ECS registration/deregistration state**, not that the EC2 instance is still running.

## CodeBuild proxy configuration

For CodeBuild running behind a proxy, configure the proxy section in:

```text
buildspec.yml
```

The practice source points specifically to the `proxy` element rather than the `artifacts` section.

**Exam trigger:** CodeBuild proxy requirement → `buildspec.yml` → `proxy`.

## API Gateway cache invalidation

When an API Gateway endpoint returns stale data because caching is enabled, the least-effort per-request invalidation approach from the practice source is:

```http
Cache-Control: max-age=0
```

The client must also be authorized to invalidate the cache.

So the practice source selects:

1. Send `Cache-Control: max-age=0`.
2. Grant the client permission to invalidate the cache.

**Do not confuse:**

```text
max-age=0 → API Gateway request-specific cache invalidation
no-cache  → not the value used by this scenario
```

## Lambda performance — increase memory

When a CPU/data-processing-heavy Lambda takes too long, increasing the configured memory can improve processing speed because Lambda allocates more CPU power with higher memory settings.

The source recommends:

```text
Increase Lambda memory
```

**Exam trigger:** Processing is slow, not timing out specifically → consider **more memory/CPU allocation**.

## X-Ray subsegments for a slow function/code block

When one specific function such as `sendRequest()` may be responsible for latency:

- Use AWS X-Ray.
- Define an **arbitrary subsegment** around the code you want to measure.

Conceptually:

```text
Segment
  └── Subsegment: sendRequest()
```

This provides finer-grained timing for the code block.

**Memory:**

`Whole request trace → segment`  
`Specific internal code/downstream call → subsegment`

## AWS CLI — `InvalidInstanceID.NotFound`

When an EC2 instance exists but an AWS CLI command returns:

```text
InvalidInstanceID.NotFound
```

one likely issue highlighted by the practice source is the CLI being configured for the **wrong AWS Region**.

**Exam trigger:** “Instance exists but CLI says instance not found” → verify:

```bash
aws configure get region
```

and compare it with the instance's actual Region.

## Step Functions — error handling and recovery

To handle and recover from state exceptions:

- `Retry` → retry a failed state.
- `Catch` → transition to a recovery/error-handling state after retries are exhausted or an error is otherwise caught.

**Memory:**

```text
Failure
  ↓
Retry?
  ├── yes → try again
  └── exhausted/final failure → Catch → recovery path
```

## Lambda cold starts

The practice source identifies two ways to reduce cold-start time:

1. **Reduce deployment package size**, including only the AWS SDK modules actually needed.
2. **Increase Lambda memory allocation** to increase available compute power.

Avoid adding large frameworks unnecessarily; the source explicitly points out that adding Spring can make Java startup heavier.

### Cold-start mental model

```text
Cold start
   ↓
INIT phase
   ├── load runtime
   ├── load dependencies
   └── execute initialization code
```

**Exam trigger:** Cold start optimization → smaller package + appropriate memory; provisioned concurrency is another separate Lambda cold-start strategy, but it was not the selected answer pair in this practice question.

## LAMP application — least configuration

For a straightforward migration of an on-premises LAMP stack, the practice source selects:

```text
Amazon EC2 + Amazon Aurora
```

Conceptual mapping:

```text
Apache/PHP application → EC2
MySQL-compatible relational DB → Aurora
```

This avoids introducing unnecessary container/orchestration components for a simple monolithic application.

## Asynchronous Lambda invocations for parallel file processing

When many files must be processed and the caller does **not** need the result synchronously:

```text
S3/Event source
      ↓
Async Lambda invocation
      ↓
Multiple invocations can process files independently
```

The practice source recommends asynchronous `Event` invocations and parallel processing.

**Exam trigger:** No response needed + independent workloads → **asynchronous Lambda**.

## DynamoDB parallel Scan

When a DynamoDB `Scan` is too slow and the workload can tolerate controlled parallelism:

```text
Parallel Scan + rate limiting
```

The practice source emphasizes a **rate-limited parallel scan** so it can reduce scan time without disrupting normal workloads.

**Exam trigger:** Scan performance + low-demand period + avoid interfering with normal traffic → **rate-limited parallel scan**.

## CORS — S3 website → API Gateway non-proxy Lambda

The browser error:

```text
No 'Access-Control-Allow-Origin' header is present...
```

points to a CORS configuration problem.

For the non-proxy API Gateway scenario, the practice source recommends enabling:

```text
API Gateway → CORS
```

for the relevant method/resource.

**Memory:**

`Browser cross-origin request failing → check CORS response headers and API Gateway CORS configuration.`

## API Gateway stages + Lambda aliases for beta testing

For testing a Lambda alias such as `AccountService:Beta` without affecting production users:

- Create a **Beta stage** in API Gateway.
- Use stage variables to reference the appropriate Lambda versions/aliases for that stage.

Conceptual architecture:

```text
API Gateway
   ├── Prod stage → Prod Lambda alias
   └── Beta stage → Beta Lambda alias
```

**Exam trigger:** New Lambda alias + isolated API testing → separate API Gateway stage + stage variables.

## Lambda `Unable to import module`

The error indicates missing runtime dependencies in the deployment package.

The practice source recommends:

1. Install missing modules into the application folder.
2. Package the application folder into a ZIP.
3. Upload the ZIP to Lambda.

Conceptually:

```text
Source code
 + dependencies
      ↓
ZIP deployment package
      ↓
Lambda
```

**Exam trigger:** `Unable to import module` → dependency packaging problem.

## Elastic Beanstalk application version lifecycle retention

When Elastic Beanstalk is configured to delete old application versions and the developer wants to keep the corresponding source bundle in S3:

```text
Application version lifecycle → Retention → Retain source bundle in S3
```

The practice source says the version-count limit can be configured, but the source-bundle retention choice controls whether the underlying source bundle remains in S3.

---

# High-Value CDA Comparisons

| Concept | Remember |
|---|---|
| Cognito User Pool | User registration, sign-in, authentication tokens |
| Cognito Identity Pool | Temporary AWS credentials for identities |
| IAM role | Preferred AWS service/app credentials without hard-coded access keys |
| STS GetSessionToken | Temporary IAM-user credentials, supports MFA |
| STS AssumeRole | Assume another IAM role, commonly cross-account |
| KMS `GenerateDataKey` | Generate per-object data keys for envelope encryption |
| S3 + SQS Extended Client | Handle SQS payloads larger than the normal message limit |
| DAX | Cache/accelerate DynamoDB reads |
| DynamoDB Streams | Capture table changes for event-driven processing |
| `TransactWriteItems` | Atomic all-or-nothing writes |
| `dynamodb:LeadingKeys` | Restrict DynamoDB item access by partition-key value |
| X-Ray annotations | Indexed data for grouping/filtering traces |
| X-Ray metadata | Extra non-indexed trace context |
| X-Ray subsegment | Measure a specific code block/downstream call |
| Lambda `/tmp` | Temporary local storage inside execution environment |
| CloudFront versioned filenames | Immediate cache-busting without invalidation charges |
| API Gateway stage | Separate API lifecycle endpoint/environment |
| Lambda version/alias | Versioned backend code target |
| EventBridge Schedule | Serverless recurring invocation |
| Step Functions Task | Execute work |
| Step Functions Retry | Retry failures |
| Step Functions Catch | Handle failure and branch to recovery |
| Step Functions ResultPath | Control where a state's result goes |
| IAM Policy Simulator | Test effective permissions |
| CLI `--dry-run` | Test whether an operation is authorized without executing it |
| `cdk synth` | Convert CDK app into CloudFormation template |
| `sam local invoke` | Invoke/test a function locally |
| `Fn::FindInMap` | Retrieve values from CloudFormation `Mappings` |
| EB `.ebextensions` | Environment/resource configuration files |
| EB `Dockerrun.aws.json` | Multi-container Docker definitions |
| CodeDeploy deployment group | Target set/environment promotion in CodeDeploy |
| CodeDeploy rollback | New deployment of last known good revision |

---

# Exam Trigger Map

```text
CodeDeploy DownloadBundle failure
        → IAM permission to S3 / S3-region compatibility

CodeDeploy automatic rollback
        → last known good revision + NEW deployment ID

Test → Staging → Production
        → multiple CodeDeploy deployment groups

EB multi-container Docker
        → Dockerrun.aws.json

EB .config customization
        → .ebextensions

Incremental collaborative EB deployments
        → CodeCommit

Lambda log location
        → context.log_stream_name

CloudFront immediate + cheap update
        → versioned filenames

API Gateway + Cognito JWT
        → Cognito User Pool authorizer

X-Ray grouping/filtering
        → annotations

S3 lowest-cost archive
        → Glacier Deep Archive

Regional AMIs in CloudFormation
        → Mappings + Fn::FindInMap

Python IaC
        → CDK

DynamoDB read acceleration
        → DAX

Step Functions error aggregation
        → Catch + ResultPath

Step Functions doing work
        → Task

DynamoDB item-level user isolation
        → dynamodb:LeadingKeys

SQS > normal payload limit
        → S3 + Extended Client Library

SOAP + modern JSON API
        → API Gateway mapping templates + Lambda

Weekly Lambda trigger
        → EventBridge schedule

Atomic DynamoDB writes
        → TransactWriteItems

DynamoDB changes → downstream service
        → DynamoDB Streams + Lambda

Repeated query-string values behind ALB → Lambda
        → multi-value headers

Shared ECS file storage
        → same task definition + EFS

Per-file unique encryption keys
        → KMS GenerateDataKey

Cross-account access without credential sharing
        → AssumeRole

IAM user + temporary credentials + MFA
        → GetSessionToken

API tiers with quota/throttle
        → Usage Plans

EC2 → CloudWatch custom metrics
        → IAM role + cloudwatch:PutMetricData

UnauthorizedOperation encoded text
        → sts decode-authorization-message

S3 + SSE-KMS throughput degradation
        → KMS API quota

Permission testing
        → Policy Simulator + CLI --dry-run

CDK local Lambda test
        → cdk synth → sam local invoke

Lambda no CloudWatch logs
        → AWSLambdaBasicExecutionRole

Lambda processing slow
        → increase memory

Specific code-block latency
        → X-Ray subsegment

EC2 instance “not found” from CLI
        → verify AWS Region

Step Functions recovery
        → Retry + Catch

Lambda cold start
        → smaller package + higher memory

Many independent Lambda jobs, no response needed
        → asynchronous Event invocation

Slow DynamoDB Scan
        → rate-limited parallel Scan

Browser CORS error
        → configure API Gateway CORS

Beta Lambda alias testing
        → Beta API Gateway stage + stage variables

Lambda Unable to import module
        → package required dependencies

EB source bundle preservation
        → Retention: retain source bundle in S3
```

---

# Review Priority from This Set

The practice result was **84.62% overall**, with category scores of **100% Deployment**, **88.89% Development**, **76.92% Security**, and **75% Troubleshooting and Optimization**.

The biggest revision themes from this set are:

1. **Security:** Cognito User vs Identity Pools, STS APIs, KMS data keys/quotas, IAM best practices, cross-account role assumption, Policy Simulator, and authorization-message decoding.
2. **Troubleshooting:** Lambda permissions/performance/cold starts, API Gateway caching/CORS/stages, X-Ray instrumentation, Step Functions retries/catches, DynamoDB scan performance, and region-related CLI errors.
3. **Development:** DynamoDB/DAX/Streams/transactions, Step Functions data flow/error handling, Cognito authorization, CloudFormation mappings, CDK, Lambda packaging, and API Gateway integration patterns.

---

# Source Notes / Gaps

The uploaded review set contains a few prompts for which the pasted content does not include the corresponding answer choices or explanation. Those were deliberately not reconstructed from general AWS knowledge:

- Deployment Q6–Q9.
- Development Q10.
- Development Q23.

This keeps the revision notes faithful to the supplied practice material.

---

# Tutorials Dojo Cheat Sheets

- [AWS CodeDeploy Cheat Sheet](https://tutorialsdojo.com/aws-codedeploy/)
- [AWS Elastic Beanstalk Cheat Sheet](https://tutorialsdojo.com/aws-elastic-beanstalk/)
- [AWS CodeCommit Cheat Sheet](https://tutorialsdojo.com/aws-codecommit/)
- [AWS Lambda Cheat Sheet](https://tutorialsdojo.com/aws-lambda/)
- [Amazon API Gateway Cheat Sheet](https://tutorialsdojo.com/amazon-api-gateway/)
- [Amazon CloudFront Cheat Sheet](https://tutorialsdojo.com/amazon-cloudfront/)
- [Amazon Cognito Cheat Sheet](https://tutorialsdojo.com/amazon-cognito/)
- [AWS X-Ray Cheat Sheet](https://tutorialsdojo.com/aws-x-ray/)
- [Amazon S3 Cheat Sheet](https://tutorialsdojo.com/amazon-s3/)
- [AWS CloudFormation Cheat Sheet](https://tutorialsdojo.com/aws-cloudformation/)
- [AWS CDK Cheat Sheet](https://tutorialsdojo.com/aws-cloud-development-kit-cdk/)
- [Amazon DynamoDB Cheat Sheet](https://tutorialsdojo.com/amazon-dynamodb/)
- [AWS Step Functions Cheat Sheet](https://tutorialsdojo.com/aws-step-functions/)
- [Amazon SQS Cheat Sheet](https://tutorialsdojo.com/amazon-sqs/)
- [AWS KMS Cheat Sheet](https://tutorialsdojo.com/aws-key-management-service-kms/)
- [AWS IAM Cheat Sheet](https://tutorialsdojo.com/aws-identity-and-access-management-iam/)
- [Amazon CloudWatch Cheat Sheet](https://tutorialsdojo.com/amazon-cloudwatch/)
- [Amazon ECS Cheat Sheet](https://tutorialsdojo.com/amazon-ecs/)
- [AWS CodeBuild Cheat Sheet](https://tutorialsdojo.com/aws-codebuild/)
- [Amazon Aurora Cheat Sheet](https://tutorialsdojo.com/amazon-aurora/)

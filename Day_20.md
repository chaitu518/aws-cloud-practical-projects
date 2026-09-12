# Day 20 — CDA: Troubleshooting and Optimization

## Topic Overview

This practice set focuses on troubleshooting and optimizing AWS applications using:

- DynamoDB Streams
- Amazon S3 CORS
- AWS X-Ray
- Lambda + VPC
- Kinesis Data Streams
- API Gateway
- Elastic Beanstalk
- CloudWatch
- Amazon RDS
- AWS WAF
- VPC Flow Logs
- IAM permissions

---

# 1. DynamoDB Streams

## 24-Hour Retention

DynamoDB Streams keeps stream records for **24 hours**.

If a consumer processes the stream less frequently than this, older records may be trimmed before they are processed.

### Exam Pattern

```text
DynamoDB Streams
→ Data retained for 24 hours
→ Consumer should process within 24 hours
```

### Important Distinction

`StreamViewType` controls **what information is captured**, not how long records are retained.

Common values include:

- `KEYS_ONLY`
- `NEW_IMAGE`
- `OLD_IMAGE`
- `NEW_AND_OLD_IMAGES`

### Question Trigger

> Records are missing from a stream because the Lambda runs every 36/48 hours.

Think:

**Reduce the processing interval to within 24 hours.**

### Cheat Sheet

- [DynamoDB Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/amazon-dynamodb/)
- [DynamoDB Streams — AWS Documentation](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.html)

---

# 2. Amazon S3 CORS

## What is CORS?

CORS allows a web application loaded from one origin to make requests to resources in another origin.

Important CORS configuration elements:

| Element | Purpose |
|---|---|
| `AllowedOrigin` | Which origin can make the request |
| `AllowedMethod` | Which HTTP methods are allowed |
| `AllowedHeader` | Headers allowed in the preflight request |
| `ExposeHeader` | Response headers accessible to browser JavaScript |
| `MaxAgeSeconds` | How long the browser caches the preflight response |

### Example

```xml
<AllowedOrigin>https://tutorialsdojo.com</AllowedOrigin>
<AllowedMethod>GET</AllowedMethod>
<AllowedMethod>PUT</AllowedMethod>
<AllowedMethod>POST</AllowedMethod>
<AllowedMethod>DELETE</AllowedMethod>
<AllowedHeader>*</AllowedHeader>
<ExposeHeader>ETag</ExposeHeader>
<MaxAgeSeconds>3600</MaxAgeSeconds>
```

This allows the specified origin to make the configured HTTP requests.

### Important Trap

`ExposeHeader` does **not** mean the request must contain that header.

It specifies a response header that browser-side JavaScript can access.

### `MaxAgeSeconds`

```text
3600 seconds = 1 hour
```

This means the browser can cache the response to the preflight `OPTIONS` request for 1 hour.

### Exam Trigger

> Which setting controls how long the browser caches a preflight response?

**`MaxAgeSeconds`**

### Cheat Sheet

- [Amazon S3 Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/amazon-s3/)

---

# 3. AWS X-Ray — Annotations vs Metadata

This is a very important CDA distinction.

## Annotations

Annotations are key-value pairs that are **indexed** by X-Ray.

Use them when data must be searchable with:

- X-Ray filter expressions
- `GetTraceSummaries`

### Think

```text
Annotation
→ Indexed
→ Searchable
→ Filter expressions
```

---

## Metadata

Metadata stores additional information in the trace.

Metadata is **not indexed** for X-Ray filtering.

Use it when you want to store information but do not need to search traces using that information.

### Think

```text
Metadata
→ Stored
→ Not indexed
→ Not used for filter expressions
```

### Comparison

| Feature | Annotations | Metadata |
|---|---|---|
| Key-value data | ✅ | ✅ |
| Indexed | ✅ | ❌ |
| Filter expressions | ✅ | ❌ |
| Store additional data | ✅ | ✅ |

### Question Trigger

> "Custom attributes must be searchable/filterable in the X-Ray console."

Answer:

**Annotations**

---

# 4. X-Ray Annotations in Subsegments

A segment represents the work performed for a request.

Subsegments represent downstream calls, such as:

- AWS services
- HTTP APIs
- SQL databases

If the requirement is to add searchable information about a **downstream operation**, place annotations in the relevant **subsegment**.

### Exam Trigger

> Search/filter information associated with an RDS/HTTP downstream call.

Think:

**Annotations → Subsegment**

---

# 5. AWS X-Ray Segment Documents

X-Ray receives trace information through **segment documents**.

A segment can contain:

- Request information
- Application work
- Subsegments

### X-Ray Data Flow

```text
Application
    ↓
X-Ray SDK
    ↓
Segment / Subsegment
    ↓
X-Ray Daemon
    ↓
AWS X-Ray API
```

The X-Ray daemon buffers trace data and uploads it to X-Ray in batches.

### Important

The SDK normally sends trace information to the **X-Ray daemon**, rather than directly calling X-Ray for every request.

### Cheat Sheet

- [AWS X-Ray Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/aws-x-ray/)
- [AWS X-Ray Concepts](https://docs.aws.amazon.com/xray/latest/devguide/xray-concepts.html)

---

# 6. AWS X-Ray — ECS

For an application running on ECS, the X-Ray daemon can run as a container.

### Typical Pattern

```text
Application Container
        ↓
X-Ray Daemon Container
        ↓
AWS X-Ray
```

The daemon listens on:

```text
UDP port 2000
```

The ECS task definition must provide the required:

- Port mappings
- Network configuration

### Exam Trap

For ECS:

**Run the X-Ray daemon as a container.**

Do not confuse this with the Elastic Beanstalk method.

### ECS vs Elastic Beanstalk

| Environment | X-Ray daemon approach |
|---|---|
| ECS | X-Ray daemon container |
| Elastic Beanstalk | `xray-daemon.config` |
| EC2 | Install/run daemon on EC2 |

### Cheat Sheet

- [AWS X-Ray on ECS — AWS Documentation](https://docs.aws.amazon.com/xray/latest/devguide/xray-daemon-ecs.html)

---

# 7. AWS X-Ray — Elastic Beanstalk

For Elastic Beanstalk, X-Ray can be enabled using a configuration file in:

```text
.ebextensions/
```

The file used in this practice set is:

```text
xray-daemon.config
```

### Pattern

```text
Elastic Beanstalk
→ .ebextensions/xray-daemon.config
→ X-Ray daemon on environment instances
```

### Exam Trap

Do not use the ECS Docker-daemon pattern for an Elastic Beanstalk environment.

### Cheat Sheets

- [AWS X-Ray Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/aws-x-ray/)
- [Elastic Beanstalk Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/aws-elastic-beanstalk/)

---

# 8. Lambda + VPC Internet Access

When Lambda is attached to a VPC, its ENIs use private IP addresses.

Attaching Lambda to a VPC allows access to VPC resources, but it does **not automatically provide Internet access**.

## To Reach the Internet

The architecture needs:

```text
Lambda
  ↓
Private Subnet
  ↓
NAT Gateway
  ↓
Internet Gateway
  ↓
Internet
```

The Lambda function's security group must also permit the required outbound traffic.

### Important

Do not solve this by simply placing Lambda in a public subnet.

Lambda ENIs do not receive public IP addresses in the way required for this approach.

### Question Trigger

> Lambda can access the database in a VPC but cannot call a public API.

Think:

**NAT Gateway + outbound security-group rules**

### Cheat Sheet

- [AWS Lambda Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/aws-lambda/)
- [Lambda VPC Networking — AWS Documentation](https://docs.aws.amazon.com/lambda/latest/dg/vpc.html)

---

# 9. API Gateway — 29-Second Integration Timeout

For the questions in this set, API Gateway integration timeout is limited to approximately:

```text
29 seconds
```

When the backend takes too long, the API Gateway request can return:

```text
HTTP 504
```

### Common Scenario

```text
Client
  ↓
API Gateway
  ↓
Lambda
  ↓
Lambda takes > 29 seconds
  ↓
API Gateway timeout
  ↓
HTTP 504
```

### HTTP Status Pattern

| Situation | Typical Response |
|---|---|
| Integration timeout | `504` |
| Authorization problem | Often `403` |
| Throttling | Commonly `429` |
| Malformed Lambda proxy response | `502` |

### Exam Trigger

> Intermittent HTTP 504 from API Gateway + Lambda takes too long.

Think:

**Integration timeout**

### Cheat Sheet

- [Amazon API Gateway Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/amazon-api-gateway/)

---

# 10. API Gateway + Lambda Proxy Integration

With Lambda proxy integration, the Lambda function must return the expected proxy response structure.

Typical format:

```json
{
  "isBase64Encoded": false,
  "statusCode": 200,
  "headers": {
    "Content-Type": "application/json"
  },
  "body": "..."
}
```

### Important

Returning arbitrary XML or another incompatible structure from the Lambda function can produce:

```text
HTTP 502 Bad Gateway
```

### Exam Pattern

```text
Lambda Proxy Integration
        ↓
Malformed / incompatible response
        ↓
API Gateway
        ↓
502
```

### 502 vs 504

```text
502 → Invalid/malformed backend response
504 → Backend integration timeout
```

### Cheat Sheet

- [API Gateway Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/amazon-api-gateway/)

---

# 11. API Gateway CloudWatch Metrics

API Gateway sends execution metrics to CloudWatch.

Important metrics from this practice set:

### `Latency`

Measures the overall responsiveness of API calls.

### `IntegrationLatency`

Measures the responsiveness/time spent in the backend integration.

### `CacheHitCount`

Number of requests served from the API cache.

### `CacheMissCount`

Number of requests that were served from the backend because they were not served from cache.

## Important Trap

`CacheHitCount` and `CacheMissCount` are relevant when **API caching is enabled**.

If these two metrics are not populated while other API metrics are available, check whether **API caching is enabled**.

### Cheat Sheet

- [API Gateway Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/amazon-api-gateway/)

---

# 12. Elastic Beanstalk Worker Environment — `cron.yaml`

Elastic Beanstalk worker environments use Amazon SQS to queue work.

Periodic tasks can be configured using:

```text
cron.yaml
```

### Pattern

```text
cron.yaml
   ↓
Periodic task
   ↓
Worker environment queue
   ↓
Worker daemon
   ↓
Application
```

### File Comparison

| File | Main Use |
|---|---|
| `cron.yaml` | Periodic worker tasks |
| `appspec.yml` | CodeDeploy lifecycle configuration |
| `Dockerrun.aws.json` | Elastic Beanstalk Docker configuration |
| `env.yaml` | Elastic Beanstalk environment configuration |

### Exam Trigger

> "Schedule periodic jobs in an Elastic Beanstalk worker environment."

Answer:

**`cron.yaml`**

### Cheat Sheet

- [Elastic Beanstalk Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/aws-elastic-beanstalk/)

---

# 13. AWS Lambda X-Ray Environment Variables

Important Lambda/X-Ray environment variables from this set:

## `_X_AMZN_TRACE_ID`

Contains tracing information such as:

- Trace ID
- Sampling decision
- Parent segment ID

---

## `AWS_XRAY_CONTEXT_MISSING`

Controls SDK behavior when X-Ray context is missing.

---

## `AWS_XRAY_DAEMON_ADDRESS`

Contains the address of the X-Ray daemon.

Format:

```text
IP_ADDRESS:PORT
```

### Important Distinction

In the question asking which environment variables Lambda uses to facilitate X-Ray communication/configuration, the tested answers were:

```text
_X_AMZN_TRACE_ID
AWS_XRAY_CONTEXT_MISSING
```

### Cheat Sheet

- [AWS X-Ray Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/aws-x-ray/)

---

# 14. CloudWatch Agent

Basic EC2 CloudWatch metrics do not provide every operating-system metric.

For metrics such as:

- Memory utilization
- Swap utilization
- Disk utilization
- Process-level/system metrics

use the:

**CloudWatch Agent**

### CloudWatch Agent

```text
EC2
 ↓
CloudWatch Agent
 ↓
CloudWatch
```

### Important Distinction

## Basic CloudWatch EC2 Monitoring

Common native metrics include CPU-related metrics.

## CloudWatch Agent

Adds additional system-level metrics such as memory and swap.

### Exam Trigger

> CPU is available but memory/swap metrics are required.

Answer:

**Install/configure the CloudWatch Agent**

### Cheat Sheet

- [Amazon CloudWatch Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/amazon-cloudwatch/)

---

# 15. RDS Monitoring — Enhanced Monitoring

Enhanced Monitoring provides more detailed operating-system-level information for an RDS database instance.

Use it when you need visibility into individual processes and resource usage.

### Question Trigger

> Need to monitor how different processes on an RDS instance consume CPU or memory.

Think:

**RDS Enhanced Monitoring**

### Important Distinction

```text
CloudWatch
→ Standard RDS metrics

Enhanced Monitoring
→ More detailed OS/process-level information
```

### Cheat Sheet

- [Amazon RDS Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/amazon-relational-database-service-rds/)

---

# 16. CloudWatch Custom Metrics and Namespaces

A CloudWatch **namespace** is a container for metrics.

Namespaces keep metrics from different applications separated.

### Pattern

```text
Application A
→ Custom Namespace
→ Custom Metrics

Application B
→ Different Namespace
→ Custom Metrics
```

### Why?

To avoid mixing unrelated application metrics into the same metric grouping.

### Exam Trigger

> Several applications need distinct custom metrics on a shared monitoring dashboard.

Think:

**Use custom CloudWatch namespaces and custom metrics.**

### Important

A:

- CloudWatch **Namespace** groups related metrics.
- CloudWatch **Dashboard** provides a graphical view of metrics.

---

# 17. X-Ray APIs

Two important X-Ray APIs:

## `GetTraceSummaries`

Returns trace summaries and identifiers that can be used to identify traces.

```text
GetTraceSummaries
→ Find trace IDs
```

## `BatchGetTraces`

Retrieves the full trace data using trace IDs.

```text
BatchGetTraces
→ Get complete traces
```

### Correct Flow

```text
GetTraceSummaries
        ↓
Trace IDs
        ↓
BatchGetTraces
        ↓
Full trace data
```

### Exam Trigger

> Build a custom tool to retrieve full traces without the X-Ray console.

Think:

**`GetTraceSummaries` → `BatchGetTraces`**

### Cheat Sheet

- [AWS X-Ray Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/aws-x-ray/)

---

# 18. X-Ray Filter Expressions

Filter expressions are used to search X-Ray traces.

To make custom data searchable:

```text
Add data as annotations
```

Do not use metadata when the requirement is filtering.

### Pattern

```text
Custom attribute
    ↓
Annotation
    ↓
Indexed
    ↓
Filter expression
```

---

# 19. X-Ray Namespace in Subsegments

The `namespace` field helps describe the type of downstream call.

### Values

```text
aws
→ AWS SDK calls

remote
→ Other downstream calls
```

### Example

```json
{
  "namespace": "aws"
}
```

for an AWS SDK call.

For an external HTTP service:

```json
{
  "namespace": "remote"
}
```

---

# 20. Kinesis Data Streams — Resharding

Kinesis Data Streams capacity is controlled by shards.

## Increase Capacity

```text
Split shards
```

## Decrease Capacity

```text
Merge shards
```

### Important

Splitting every shard doubles capacity, but it may provide more capacity than required.

A better optimization can be to identify **hot shards** and split only those.

### Pattern

```text
Higher incoming data
        ↓
More capacity required
        ↓
Reshard
        ↓
Split hot/required shards
```

### Exam Trap

Changing the EC2 instance type does **not** increase the capacity of the Kinesis stream itself.

### Cheat Sheet

- [Amazon Kinesis Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/amazon-kinesis/)
- [Kinesis Scaling and Resharding — Tutorials Dojo](https://tutorialsdojo.com/kinesis-scaling-resharding-and-parallel-processing/)

---

# 21. Kinesis Duplicate Records — Idempotency

Kinesis records can be delivered more than once.

Possible causes include:

- Producer retries
- Consumer retries

Applications should therefore be designed to handle duplicate processing.

### Scenario

A producer experiences a network timeout after `PutRecord` but before receiving confirmation.

The producer may retry, causing the same logical data to appear again.

### Exam Trigger

> Duplicate records are causing duplicate processing.

Think:

**Design the consumer/application to be idempotent.**

The practice question highlights embedding a **primary key/idempotency identifier** in the record so duplicate records can be detected and handled.

### Memory Hook

```text
Kinesis
→ At-least-once style delivery behavior can cause duplicates
→ Consumer must handle duplicates safely
```

---

# 22. AWS WAF

AWS WAF protects web applications from common HTTP/HTTPS attacks.

In this set, the relevant threats include:

- SQL injection
- Cross-site scripting (XSS)

### Typical Integration Points

AWS WAF can protect supported services such as:

- Application Load Balancer
- Amazon CloudFront
- API Gateway

### Exam Trigger

> Block SQL injection and XSS against a web application.

Think:

**AWS WAF**

### Service Distinction

| Service | Main Purpose |
|---|---|
| AWS WAF | Web/application-layer request filtering |
| GuardDuty | Threat detection |
| Firewall Manager | Centralized security policy management |

### Cheat Sheet

- [AWS WAF Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/aws-waf/)

---

# 23. Client IP Address — X-Forwarded-For

When traffic passes through a proxy/load balancer, the original client IP can be carried in:

```text
X-Forwarded-For
```

### Exam Trigger

> Application is behind an Application Load Balancer and needs the original client's IP address.

Think:

**`X-Forwarded-For`**

### Memory Hook

```text
Original client IP
        ↓
X-Forwarded-For
```

---

# 24. VPC Flow Logs

VPC Flow Logs capture information about IP traffic going to and from network interfaces in a VPC.

They can help with:

- Network troubleshooting
- Security analysis
- Investigating why traffic is not reaching an instance
- Diagnosing restrictive security-group rules

### Destinations

Flow logs can be published to:

- CloudWatch Logs
- Amazon S3

### Exam Trigger

> Capture IP traffic information going to/from VPC network interfaces.

Answer:

**VPC Flow Logs**

### Service Distinction

```text
VPC Flow Logs
→ Network traffic information

CloudTrail
→ AWS API activity

X-Ray
→ Application/request tracing

GuardDuty
→ Threat detection
```

### Cheat Sheet

- [Amazon VPC Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/amazon-vpc/)

---

# 25. X-Ray IAM Permissions

## `AWSXrayReadOnlyAccess`

Used when a user needs to:

- Access the X-Ray console
- View service maps
- View segments/traces

### Least Privilege

If only read access is required, use:

```text
AWSXrayReadOnlyAccess
```

---

## `AWSXRayDaemonWriteAccess`

Appropriate for applications/resources that need permission to upload trace data to X-Ray.

---

## `AWSXrayFullAccess`

Provides broader X-Ray permissions and should not be selected when read-only access is sufficient.

### Comparison

| Policy | Main Use |
|---|---|
| `AWSXrayReadOnlyAccess` | View X-Ray data |
| `AWSXRayDaemonWriteAccess` | Application/daemon uploads trace data |
| `AWSXrayFullAccess` | Broad X-Ray management |

### Exam Trigger

> Developer only needs to view X-Ray service maps and traces.

Answer:

**`AWSXrayReadOnlyAccess`**

### Cheat Sheet

- [AWS X-Ray Cheat Sheet — Tutorials Dojo](https://tutorialsdojo.com/aws-x-ray/)

---

# 26. CloudWatch Agent vs X-Ray

Do not confuse monitoring and tracing.

| Requirement | Service |
|---|---|
| Trace application requests | X-Ray |
| Find latency through services | X-Ray |
| Memory utilization on EC2 | CloudWatch Agent |
| Swap utilization on EC2 | CloudWatch Agent |
| OS/process-level RDS monitoring | Enhanced Monitoring |
| Network IP traffic | VPC Flow Logs |

---

# 27. Troubleshooting Pattern Map

| Problem | Think First |
|---|---|
| DynamoDB stream data disappearing | **24-hour stream retention** |
| Cross-origin browser requests | **S3 CORS** |
| Search X-Ray custom attributes | **Annotations** |
| Store non-searchable X-Ray data | **Metadata** |
| Lambda in VPC cannot reach Internet | **NAT Gateway + outbound SG** |
| API Gateway `504` | **Integration timeout / backend too slow** |
| API Gateway `502` with Lambda proxy | **Invalid Lambda proxy response** |
| API cache metrics missing | **API caching not enabled** |
| ECS X-Ray | **X-Ray daemon container** |
| Elastic Beanstalk X-Ray | **`.ebextensions/xray-daemon.config`** |
| Scheduled EB worker tasks | **`cron.yaml`** |
| EC2 memory/swap metrics | **CloudWatch Agent** |
| Detailed RDS process metrics | **Enhanced Monitoring** |
| Kinesis needs more capacity | **Split shards** |
| Kinesis duplicates | **Idempotency / duplicate handling** |
| SQL injection/XSS | **AWS WAF** |
| Original client IP | **`X-Forwarded-For`** |
| VPC IP traffic | **VPC Flow Logs** |
| X-Ray console read access | **`AWSXrayReadOnlyAccess`** |
| Find X-Ray trace IDs | **`GetTraceSummaries`** |
| Retrieve full X-Ray traces | **`BatchGetTraces`** |

---

# 28. Most Important Comparisons

## X-Ray: Annotation vs Metadata

```text
Need SEARCH/FILTER?
→ Annotation

Just STORE additional data?
→ Metadata
```

---

## API Gateway: 502 vs 504

```text
502
→ Bad/incompatible Lambda proxy response

504
→ Integration timeout
```

---

## X-Ray Deployment

```text
EC2
→ Install/run daemon on instance

ECS
→ Run daemon as container

Elastic Beanstalk
→ xray-daemon.config
```

---

## Monitoring

```text
Application tracing
→ X-Ray

EC2 memory/swap
→ CloudWatch Agent

RDS process-level metrics
→ Enhanced Monitoring

VPC network traffic
→ VPC Flow Logs
```

---

## Kinesis

```text
Need MORE capacity
→ Split shards

Need LESS capacity
→ Merge shards

Duplicate processing
→ Idempotent consumer
```

---

# 29. Day 20 Final Memory Map

```text
DYNAMODB STREAMS
→ 24-hour retention
```

```text
S3 CORS
→ AllowedOrigin
→ AllowedMethod
→ AllowedHeader
→ ExposeHeader
→ MaxAgeSeconds
```

```text
X-RAY
→ Annotation = indexed/searchable
→ Metadata = stored/not indexed
→ Segment = request/work
→ Subsegment = downstream call
→ GetTraceSummaries = find trace IDs
→ BatchGetTraces = full traces
```

```text
LAMBDA + VPC
→ Private subnet
→ NAT Gateway
→ Outbound SG rule
→ Internet access
```

```text
API GATEWAY
→ 504 = timeout
→ 502 = invalid backend response
→ CacheHitCount/CacheMissCount = API caching
```

```text
KINESIS
→ Split = increase capacity
→ Merge = decrease capacity
→ Duplicates = handle idempotently
```

```text
ELASTIC BEANSTALK
→ X-Ray = xray-daemon.config
→ Worker periodic tasks = cron.yaml
```

```text
MONITORING
→ EC2 memory/swap = CloudWatch Agent
→ RDS process metrics = Enhanced Monitoring
→ VPC traffic = Flow Logs
```

```text
SECURITY
→ SQL injection/XSS = WAF
→ X-Ray read-only console = AWSXrayReadOnlyAccess
```

---

# 30. Cheat Sheets & Quick References

## AWS X-Ray
- [Tutorials Dojo — AWS X-Ray](https://tutorialsdojo.com/aws-x-ray/)
- [AWS X-Ray Documentation](https://docs.aws.amazon.com/xray/)

## Amazon API Gateway
- [Tutorials Dojo — API Gateway](https://tutorialsdojo.com/amazon-api-gateway/)

## AWS Lambda
- [Tutorials Dojo — AWS Lambda](https://tutorialsdojo.com/aws-lambda/)

## DynamoDB
- [Tutorials Dojo — Amazon DynamoDB](https://tutorialsdojo.com/amazon-dynamodb/)

## Kinesis
- [Tutorials Dojo — Amazon Kinesis](https://tutorialsdojo.com/amazon-kinesis/)

## Elastic Beanstalk
- [Tutorials Dojo — Elastic Beanstalk](https://tutorialsdojo.com/aws-elastic-beanstalk/)

## CloudWatch
- [Tutorials Dojo — Amazon CloudWatch](https://tutorialsdojo.com/amazon-cloudwatch/)

## VPC
- [Tutorials Dojo — Amazon VPC](https://tutorialsdojo.com/amazon-vpc/)

## S3
- [Tutorials Dojo — Amazon S3](https://tutorialsdojo.com/amazon-s3/)

## AWS WAF
- [Tutorials Dojo — AWS WAF](https://tutorialsdojo.com/aws-waf/)

## Full AWS Cheat Sheet Collection
- [Tutorials Dojo — All AWS Cheat Sheets](https://tutorialsdojo.com/links-to-all-aws-cheat-sheets/)

---

# Review of What Was Covered

This set mainly teaches a **troubleshooting mindset**:

```text
Observe the symptom
       ↓
Identify the AWS layer
       ↓
Match the symptom to the correct metric/service/configuration
       ↓
Choose the least-complex AWS-native solution
```

The highest-value patterns from this set are:

```text
X-Ray:
Annotation → Search
Metadata → Store

API Gateway:
502 → Backend response problem
504 → Timeout

Lambda VPC:
VPC access ≠ Internet access
→ NAT Gateway

Kinesis:
Split shards → More capacity
Duplicates → Idempotency

Monitoring:
CloudWatch Agent → EC2 OS metrics
Enhanced Monitoring → RDS process/OS detail
Flow Logs → VPC network traffic

Security:
WAF → SQL injection/XSS

IAM:
AWSXrayReadOnlyAccess → View X-Ray console/data
```

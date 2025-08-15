# CST8917 – Serverless Service Alternatives Report
**Due:** August 15, 2025  
**Author:** Daniel Karengera 
**Course:** Serverless Applications (CST8917)  
**Repo:** This README is the primary deliverable.

> This report maps the Azure serverless services used in class to closest equivalents on AWS and Google Cloud, and compares them across features, triggers/bindings, integrations, monitoring, and pricing models. Each section starts with a quick comparison table followed by brief analysis.

---

## Contents
- [How to read this document](#how-to-read-this-document)
- [Quick mapping (Azure → AWS/GCP)](#quick-mapping-azure--awsgcp)
- [1. Azure Functions](#1-azure-functions)
- [2. Durable Functions](#2-durable-functions)
- [3. Logic Apps](#3-logic-apps)
- [4. Azure Service Bus (Queues & Topics)](#4-azure-service-bus-queues--topics)
- [5. Azure Event Grid](#5-azure-event-grid)
- [6. Azure Event Hubs](#6-azure-event-hubs)
- [References](#references)

---

## How to read this document
- **Tables** summarize the core comparison for quick study.  
- **Narratives** call out strengths/weaknesses and “when to choose which”.  
- **Pricing** is **high-level** (enough for architecture trade‑offs). Always verify region‑specific prices with the linked pricing pages before finalizing designs.

## Quick mapping (Azure → AWS/GCP)

| Azure | Closest AWS equivalent | Closest GCP equivalent |
|---|---|---|
| **Azure Functions** | **AWS Lambda** | **Cloud Run functions** (formerly Cloud Functions 2nd gen) |
| **Durable Functions** (stateful workflows) | **AWS Step Functions** | **Google Cloud Workflows** |
| **Logic Apps** (low‑code integration & connectors) | **AWS Application Composer** + **Step Functions** + **EventBridge (API Destinations)** | **Application Integration** + **Workflows** + **Eventarc** |
| **Service Bus (Queues & Topics)** | **Amazon SQS** (queues) + **Amazon SNS** (pub/sub topics) | **Pub/Sub** (unified pub/sub with push/pull) |
| **Event Grid** | **Amazon EventBridge** | **Eventarc** |
| **Event Hubs** (stream ingestion) | **Kinesis Data Streams** | **Pub/Sub** (used for streaming as well as messaging) |

---

## 1. Azure Functions

**Overview.** Event‑driven functions running on managed infrastructure with rich triggers/bindings and multiple hosting plans (Consumption, Premium, Container Apps).

| Aspect | Azure Functions | AWS Lambda | Cloud Run functions |
|---|---|---|---|
| **Triggers / bindings** | Extensive triggers/bindings for Storage, Service Bus, Event Hubs, HTTP, Timer, etc. | Invoked via API Gateway/ALB/Function URL; events from many AWS services; async & sync invocations. | HTTP, Pub/Sub, Cloud Storage, Eventarc (many GCP sources), etc. |
| **Languages** | C#, Java, JS/TS, Python, PowerShell, etc. | Node.js, Python, Java, .NET, Go, Ruby, etc. | Node.js, Python, Go, Java, .NET, etc. (2nd gen = Cloud Run functions) |
| **Integration** | Deep with Azure services and Dev tools (VS/VS Code); runs in App Service, Premium, or Container Apps. | Deep with AWS (S3, DynamoDB, SQS/SNS, EventBridge); IaC via SAM/CloudFormation & CDK. | Built on Cloud Run runtime model; CI/CD with Cloud Build/Deploy; easy service-to-service auth with IAM. |
| **Monitoring** | Azure Monitor + Application Insights (traces, logs, metrics). | Amazon CloudWatch (logs, metrics, alarms) + X-Ray. | Cloud Monitoring/Logging/Trace (Ops suite). |
| **Pricing model** | Consumption: pay per execution + GB‑seconds; monthly free grant. Premium: vCPU/RAM seconds (no per‑execution charge). | Pay per request + GB‑seconds; **Always Free**: 1M requests + 400k GB‑s/mo. | Cloud Run functions (request/instance‑based) pricing—pay per vCPU‑sec, GiB‑sec, and requests; generous free CPU/RAM seconds. |

**Notes & trade‑offs.**
- **Bindings vs SDKs.** Azure’s bindings reduce boilerplate for common services; AWS and GCP generally favor SDK calls or service‑specific triggers.  
- **Cold starts.** All platforms have cold starts; Premium/Provisioned Concurrency/Min instances can mitigate.  
- **Multicloud portability.** HTTP‑first designs (API Gateway/Functions/Cloud Run) ease portability.

---

## 2. Durable Functions

**Overview.** Code‑first, stateful workflows atop Azure Functions (or in Container Apps), supporting fan‑out/fan‑in, chaining, human interactions, and long‑running orchestrations.

| Aspect | Azure Durable Functions | AWS Step Functions | Google Cloud Workflows |
|---|---|---|---|
| **Authoring model** | **Code‑first** orchestration (C#, JS/TS, Python). | **Declarative JSON/YAML** (Workflow Studio visual editor) with Standard & Express modes. | **Declarative YAML/JSON** workflows with connectors & HTTP steps. |
| **Integrations** | Native with Functions, Storage queues, Event Hubs, Service Bus. | 200+ service integrations, SDK integrations, Lambda/containers/APIs. | Orchestrates GCP services + external HTTP; can call Application Integration. |
| **Monitoring** | Instance history & status via Durable APIs + Azure Monitor. | Visual execution history; metrics/logs in CloudWatch. | Execution logs/metrics in Cloud Monitoring/Logging. |
| **Pricing model** | Billed via underlying Functions/Container Apps + storage/queues (no separate workflow SKU). | **Standard:** per state transition; **Express:** per execution duration and request volume. Free tier for state transitions. | **Per step** executed (+ external HTTP call steps), with a free tier for steps and external calls. |

**Notes & trade‑offs.**
- Choose **Durable** when you want code‑centric orchestration within Functions; **Step Functions/Workflows** when you prefer declarative and visual tooling and broad cross‑service integrations.

---

## 3. Logic Apps

**Overview.** Low‑code iPaaS for visual workflows with **hundreds of managed connectors** (O365, Salesforce, SAP, etc.). Offers **Consumption** (per‑action) and **Standard** (App Service/Containerize) plans.

| Aspect | Azure Logic Apps | AWS alternative(s) | GCP alternative(s) |
|---|---|---|---|
| **Model** | Visual designer, triggers/actions, managed & enterprise connectors. | **Application Composer** (visual design + IaC) to assemble services, often paired with **Step Functions** for orchestration and **EventBridge API Destinations** for SaaS/webhooks. | **Application Integration** (visual iPaaS + connectors) paired with **Workflows** for orchestration and **Eventarc** for eventing. |
| **Connectors** | Large catalog incl. enterprise systems & B2B (Integration Account). | Service integrations + API Destinations; third‑party connectors vary by service. | Integration Connectors catalog; growing set of third‑party connectors. |
| **Monitoring** | Run history in designer; Azure Monitor/Log Analytics. | CloudWatch dashboards/logs; Step Functions execution viewer. | Ops suite (Monitoring/Logging); Workflows execution details. |
| **Pricing model** | **Consumption:** pay‑per‑action/connector call; **Standard:** app plan (vCPU/RAM) + built‑ins. | Pay by component (Step Functions transitions, EventBridge events, etc.). | Pay by component (Application Integration tier + Workflows steps + Eventarc events). |

**Notes & trade‑offs.**
- Logic Apps shines for **non‑developer** scenarios and B2B/EDI. AWS/GCP require composing multiple services to match the same iPaaS capabilities.

---

## 4. Azure Service Bus (Queues & Topics)

**Overview.** Enterprise message broker with queues (**point‑to‑point**) and topics/subscriptions (**pub/sub**), advanced filters, sessions, and dead‑lettering.

| Aspect | Azure Service Bus | Amazon SQS (+ SNS for topics) | Google Cloud Pub/Sub |
|---|---|---|---|
| **Pattern** | Queues **and** topics in one service. | SQS = queues; combine with **SNS** for pub/sub topics. | Unified global pub/sub; push or pull subscriptions. |
| **Enterprise features** | FIFO via sessions, duplicate detection, deferral, dead‑letter queues, rules/filters. | SQS Standard & FIFO; dead‑letter queues; SNS topics for fan‑out. | At‑least‑once delivery with ordering keys, dead‑letter topics, filtering on attributes. |
| **Integrations** | Functions, Logic Apps, Event Grid; ubiquitous SDKs. | Lambda, EventBridge, EC2/ECS; SDKs. | Cloud Run functions, Dataflow, BigQuery; SDKs. |
| **Monitoring** | Azure Monitor metrics (active messages, throughput, errors). | CloudWatch metrics & DLQ monitoring. | Cloud Monitoring & Logging; subscription metrics. |
| **Pricing model** | Standard = monthly base + per‑operation (first block of ops included); Premium = capacity units. | **Per request** (chunked by 64KB), free tier 1M req/mo; FIFO slightly higher; KMS/data transfer extra. | **Per GiB** of ingestion/delivery/retained storage; first GBs free; optional features (Storage subscriptions) extra. |

**Notes & trade‑offs.**
- Need **topics with filtering** and **sessions**? Service Bus is very strong. For super‑simple queueing at very high scale, SQS often costs less to operate; Pub/Sub gives global pub/sub with automatic scaling.

---

## 5. Azure Event Grid

**Overview.** Managed **event routing** (publish/subscribe) with **advanced filtering** and delivery, native sources and custom topics.

| Aspect | Azure Event Grid | Amazon EventBridge | Google Eventarc |
|---|---|---|---|
| **Use case** | Event bus to connect producers & consumers; reacts to many Azure sources. | Event bus for AWS services, SaaS, and custom apps; rules, pipes, API destinations, archive/replay. | Event routing from GCP services and custom sources to Cloud Run/Functions/GKE. |
| **Filtering & routing** | Advanced subject/data filters; dead‑lettering to Storage. | Content-based rules, input transformers, cross‑account routing; scheduler; archive & replay. | Event filtering via Eventarc triggers; supports Cloud Audit Logs/Storage/Pub/Sub and more. |
| **Destinations** | Functions, Logic Apps, Service Bus, WebHooks, etc. | Lambda, SQS/SNS, Step Functions, API Destinations, more. | Cloud Run, Cloud Run functions, Workflows, GKE. |
| **Pricing model** | **Per operation** (ingress/delivery); monthly free operations. | **Per event** (ingest/delivery) with separate charges for API Destinations, replay, etc.; free management events. | **Per event** routed; free tier of chargeable events. |

**Notes & trade‑offs.**
- EventBridge has the richest **SaaS and cross‑account** story; Event Grid is extremely simple and cost‑effective in Azure‑centric designs; Eventarc is the native glue for GCP to Cloud Run/Functions/GKE.

---

## 6. Azure Event Hubs

**Overview.** Big‑scale streaming ingestion with **partitioned consumer model**; integrates with Stream Analytics, Databricks, Functions; Kafka protocol support.

| Aspect | Azure Event Hubs | Amazon Kinesis Data Streams | Google Cloud Pub/Sub |
|---|---|---|---|
| **Model** | Append‑only partitions; consumers read by partition/offset; TU/Capacity‑unit based. | Shard‑based stream with **provisioned** or **on‑demand** capacity. | Topic‑based pub/sub scales automatically; supports streaming & messaging. |
| **Throughput scaling** | Add **Throughput Units** (or Premium Capacity Units); more partitions ⇒ more parallelism. | Add **shards** (or use on‑demand). Per‑shard limits (write/read) apply. | Auto‑scales; quotas per project/region. |
| **Integrations** | Stream Analytics, Event Hubs Capture, Functions, Databricks, Kafka ecosystem. | Kinesis ecosystem (Firehose, Analytics), Lambda, S3. | Dataflow, BigQuery, Cloud Run functions, Spark. |
| **Monitoring** | Azure Monitor (ingress/egress, consumer groups). | CloudWatch (shard metrics, iterator age). | Cloud Monitoring (ack latency, throughput). |
| **Pricing model** | **Per Throughput Unit / Capacity Unit hour** + operations; Capture storage extra. | **Per shard‑hour** (+ PUT payload units) or **on‑demand** per GB in/out. | **Per GiB** of data ingestion/delivery/storage; free tier GBs. |

**Notes & trade‑offs.**
- For Kafka‑compatibility or tight Azure integration, Event Hubs is ideal. Kinesis gives strong control via shards (and now on‑demand). Pub/Sub offers global scale with very simple capacity management.

---

## References
> Official docs and pricing pages used while compiling this report (verify region‑specific pricing before use):
- Azure Functions pricing; Durable Functions; Premium plan; Functions triggers/bindings; reliability.  
- AWS Lambda pricing and Free Tier; Lambda triggers and invocation; quotas.  
- Cloud Run functions (formerly Cloud Functions 2nd gen) overview, pricing (via Cloud Run), runtime/quotas, triggers.  
- Azure Logic Apps pricing; Logic Apps docs.  
- AWS Application Composer and Workflow Studio; EventBridge pricing & features.  
- GCP Application Integration & Integration Connectors.  
- Azure Service Bus pricing; SQS pricing; Pub/Sub pricing.  
- Event Grid pricing; AWS EventBridge pricing; GCP Eventarc.  
- Event Hubs features/scaling; Kinesis limits & modes; Pub/Sub architecture.


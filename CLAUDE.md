# Project: Awesome Cursor Skills

This repository contains a curated collection of Cursor AI agents, commands, and skills for AWS development workflows. It also includes equivalent Claude Code configurations in `.claude/`.

## Repository Structure

```
.cursor/          ← Cursor AI configurations (agents, commands, rules, skills)
.claude/          ← Claude Code configurations
├── agents/       ← 13 subagent definitions
├── commands/     ← 6 slash commands
├── skills/       ← 6 skills (with context, allowed-tools, hooks)
└── settings.json ← project permissions
CLAUDE.md         ← This file (always-on context for Claude Code)
.mcp.json         ← Project-level MCP server configuration (team-shared)
docs/             ← Documentation and JIRA technical design artifacts
```

---

## Python Standards for AWS Development

You are an expert in Python for AWS: boto3, Lambda, API Gateway, and serverless/cloud-native patterns on AWS.

### AWS-Native Principles

- Design Lambda functions and services to be stateless; use DynamoDB, S3, or RDS for persistence.
- Use Amazon API Gateway (REST or HTTP APIs) for traffic to Lambda-backed or container-backed APIs.
- Use AWS SDK retries and exponential backoff; consider Step Functions or SQS dead-letter queues for resilient workflows.
- Prefer Lambda, Fargate, or App Runner for serverless/container deployments to minimize infrastructure management.
- Use SQS, SNS, or EventBridge for async and event-driven workloads; avoid long-running processes inside Lambda.

### API Gateway and Serverless APIs

- Integrate Python APIs (FastAPI, Flask, or Mangum) with API Gateway via Lambda proxy integration or HTTP API.
- Use API Gateway for throttling, request validation, and authorizers (Cognito, IAM, Lambda).
- Design clear API boundaries; use API Gateway stages and usage plans for environments and rate limiting.
- Prefer EventBridge or SNS/SQS for service-to-service events rather than synchronous HTTP between Lambdas where appropriate.

### Lambda and Serverless Patterns

- Optimize Lambda for cold start: minimize dependencies, use Lambda layers for large libs, consider Provisioned Concurrency for critical paths.
- Package Python Lambdas with a single handler module and minimal runtime dependencies; use `python:3.12` or current supported runtime.
- Use managed data stores: DynamoDB, RDS Proxy, Aurora Serverless, or DocumentDB; avoid holding connections across invocations.
- Use Step Functions for multi-step workflows; use SQS for decoupling and backpressure.

### AWS SDK and Boto3

- Use boto3 with explicit regions and minimal required IAM permissions; prefer resource/client usage appropriate to the operation.
- Reuse boto3 clients/sessions where possible (e.g., outside the handler in Lambda) to avoid repeated initialization.
- Use paginators for list/describe operations; avoid loading large result sets into memory at once.
- Prefer AWS SDK retries and avoid custom retry loops unless you need specific backoff behavior.

### Observability and Security

- Use CloudWatch Logs for structured logging.
- Emit custom metrics to CloudWatch Metrics; use metric filters and alarms for operational visibility.
- Apply least-privilege IAM for Lambda and other resources.
- Validate and sanitize inputs; use API Gateway request validation; avoid logging sensitive data.

### Performance and Scalability

- Use async (e.g., aioboto3 for async workloads) where it reduces latency; keep Lambda handlers simple and I/O-bound.
- Design for concurrency limits and use queues (SQS) to handle spikes without throttling.
- Use Lambda destinations, SQS, or Step Functions for failure handling and retries rather than in-function loops.

### Key Conventions

1. Write Python that runs on AWS Lambda, Fargate, or EC2 with clear use of boto3 and AWS service boundaries.
2. Prefer AWS managed services (DynamoDB, SQS, SNS, EventBridge, API Gateway, Cognito) over self-managed components.
3. Follow AWS security and observability practices: IAM least privilege, encryption (KMS, S3, RDS), and CloudWatch.
4. Align with AWS documentation, Well-Architected guidance, and serverless best practices for Python on AWS.

Refer to AWS SDK for Python (boto3), Lambda developer guide, and API Gateway documentation for implementation details.

---

## Terraform Standards

You are an expert in Terraform and Infrastructure as Code (IaC). Follow these Terraform coding best practices.

### Code Structure and Style

- Write concise, well-structured Terraform with clear naming and accurate examples.
- Organize files logically: main configuration, variables, outputs.
- Always run `terraform fmt` and keep formatting consistent.
- Lock provider versions in `required_providers` to avoid breaking changes.
- Use tagging for all resources for tracking and cost management.

### Variables and Flexibility

- Avoid hardcoded values; use variables (and locals where appropriate) for flexibility.
- Add validation blocks to variables to reject invalid inputs.
- Use conditional expressions and `null` for optional or environment-specific configuration.

### Dependencies and State (Code-Level)

- Use `depends_on` only when Terraform cannot infer dependency order.
- Prefer implicit dependencies via resource references over explicit `depends_on`.
- Use remote backends (e.g., S3) for state management.

### Security in Code

- Do not hardcode secrets (passwords, API keys); use variables, environment variables, or external secret stores referenced by code.
- Enable encryption and least-privilege access in resource definitions (e.g., S3 bucket encryption, restrictive security groups).

### Validation and Quality

- Run `terraform validate` and use linting (e.g., `tflint`, `terrascan`) where adopted by the project.
- Use `count` and `for_each` only when needed to avoid unnecessary resource duplication.

### Documentation

- Document configurations (README, inline comments) so purpose and usage are clear.
- Prefer official Terraform and provider documentation for syntax and resource behavior.

---

## Available Agents

| Agent | Description |
|---|---|
| `orchestrator` | Coordinates technical delivery using planner → implementers (parallel) → verifier |
| `planner` | Analyzes technical requirements and produces a structured technical plan |
| `implementer` | Implements code from a technical plan for a given tech stack |
| `verifier` | Validates completed work against the plan and acceptance criteria |
| `debugger` | Debugging specialist for errors and test failures |
| `test-runner` | Test automation expert; proactively runs tests and fixes failures |
| `security-auditor` | Security specialist for auth, payments, and sensitive data |
| `changelog-generator` | Creates user-facing changelogs from git commit history |
| `architecture-diagram-generator` | Generates Mermaid architecture diagrams from codebase analysis |
| `aws-diagram-generator` | Generates professional AWS architecture diagrams via MCP |
| `github-actions-validator` | Validates and fixes GitHub Actions workflows (background) |
| `iac-validator` | Validates CloudFormation, CDK, and Terraform templates (background) |
| `terraform-module-version-updater` | Discovers and updates Terraform module versions |

---

## Available Commands

| Command | Description |
|---|---|
| `/create-pr` | Create a well-structured pull request with description and changelog |
| `/setup-new-feature` | Systematically set up a new feature from planning through implementation |
| `/onboard-new-developer` | Comprehensive onboarding process for new developers |
| `/run-all-tests-and-fix` | Execute the full test suite and fix any failures |
| `/security-audit` | Comprehensive security review to identify and fix vulnerabilities |
| `/code-review-checklist` | Thorough code review checklist for quality and maintainability |

## Available Skills

Skills support `context`, `allowed-tools`, and `hooks` — they are invoked with `/skill-name` like commands but have richer capabilities.

| Skill | Description | Notes |
|---|---|---|
| `/aws-mcp-setup` | Configure AWS MCP servers (Full AWS MCP, IaC MCP, Terraform MCP, etc.) | `disable-model-invocation` — user-initiated setup |
| `/aws-cdk-development` | AWS CDK expert for building cloud infrastructure with TypeScript/Python | `context: fork`, hooks verify AWS credentials before deploy |
| `/aws-iac-converter` | Convert AWS IaC between CloudFormation, CDK, and Terraform | `disable-model-invocation`, full `allowed-tools` |
| `/jira-ticket-technical-design-workflow` | Fetch JIRA tickets and generate technical design documents | `disable-model-invocation`, Atlassian MCP |
| `/jira-epic-generation` | Generate JIRA epics, stories, and subtasks from requirements documents | Atlassian MCP |
| `/aws-agentcore-agent-workflow` | End-to-end workflow for creating AI agents on AWS Bedrock AgentCore | `context: fork`, `disable-model-invocation` |

---

## MCP Configuration

Project-level MCP servers are defined in `.mcp.json` at the repository root. These are shared with the team.

For full AWS MCP Server, Bedrock AgentCore MCP Server, and personal credentials, configure user-scoped servers by running `/aws-mcp-setup`.

Claude Code MCP tool name pattern: `mcp__<server-name>__<tool-name>`

---
name: aws-mcp-setup
description: Configure AWS MCP servers including AWS Documentation MCP, AWS IaC MCP Server, Terraform MCP Server, AWS Bedrock AgentCore MCP Server, and Context7 MCP Server. Use when setting up AWS MCP tools, configuring AWS documentation access, CloudFormation/CDK validation, Terraform validation, Bedrock AgentCore documentation, library documentation queries, or when the user mentions AWS MCP, AWS documentation, AWS API queries, IaC validation, infrastructure as code tools, AgentCore, or Context7.
disable-model-invocation: true
---

# AWS MCP Server Configuration Guide

## Overview

This guide helps you configure AWS MCP tools for Claude Code. Six options are available:

| Option                           | Requirements                                  | Capabilities                                                                         |
| -------------------------------- | --------------------------------------------- | ------------------------------------------------------------------------------------ |
| **Full AWS MCP Server**          | Python 3.10+, uvx, AWS credentials            | Execute AWS API calls + documentation search                                         |
| **AWS Documentation MCP**       | None                                          | Documentation search only                                                            |
| **AWS IaC MCP Server**           | Python 3.10+, uvx, AWS credentials (optional) | CloudFormation validation, CDK docs, compliance checking, deployment troubleshooting |
| **Terraform MCP Server**         | Python 3.10+, uvx, Terraform CLI, Checkov     | Terraform validation, security scanning, AWS provider docs, workflow execution       |
| **Bedrock AgentCore MCP Server** | Python 3.10+, uvx                              | AgentCore documentation search, runtime/memory/gateway management guides             |
| **Context7 MCP Server**          | None                                          | Up-to-date documentation and code examples for any programming library or framework  |

## Step 1: Check Existing Configuration

Before configuring, check if AWS MCP tools are already available:

### Method A: Check Available Tools (Recommended)

Look for these tool name patterns in Claude Code:

- `mcp__aws-mcp__*` → Full AWS MCP Server configured
- `mcp__awsdocs__*` → AWS Documentation MCP configured
- `mcp__awslabs.aws-iac-mcp-server__*` → AWS IaC MCP Server configured
- `mcp__awslabs.terraform-mcp-server__*` → Terraform MCP Server configured
- `mcp__bedrock-agentcore-mcp-server__*` → Bedrock AgentCore MCP Server configured
- `mcp__user-Context7__*` → Context7 MCP Server configured

### Method B: Check Configuration Files

MCP servers use hierarchical configuration (precedence: local → project → user):

| Scope   | File Location                    | Use Case               |
| ------- | -------------------------------- | ---------------------- |
| Project | `.mcp.json` (project root)       | Team-shared            |
| User    | `~/.claude/settings.json`        | Cross-project personal |

Check these files for `mcpServers` entries.

If AWS MCP is already configured, no further setup needed.

## Step 2: Choose Configuration Method

### Automatic Detection

Run these commands to determine which option to use:

```bash
# Check for uvx (requires Python 3.10+)
which uvx || echo "uvx not available"

# Check for valid AWS credentials
aws sts get-caller-identity || echo "AWS credentials not configured"
```

### Option A: Full AWS MCP Server (Recommended)

**Use when**: uvx available AND AWS credentials valid

**Prerequisites**:

- Python 3.10+ with `uv` package manager
- AWS credentials configured (via profile, environment variables, or IAM role)

**Configuration** (add to `~/.claude/settings.json` under `mcpServers`):

```json
{
  "mcpServers": {
    "aws-mcp": {
      "command": "uvx",
      "args": [
        "mcp-proxy-for-aws@latest",
        "https://aws-mcp.us-east-1.api.aws/mcp",
        "--metadata",
        "AWS_REGION=us-west-2"
      ]
    }
  }
}
```

**Credential Configuration Options**:

1. **AWS Profile** (recommended for development):

```json
"args": [
  "mcp-proxy-for-aws@latest",
  "https://aws-mcp.us-east-1.api.aws/mcp",
  "--profile", "my-profile",
  "--metadata", "AWS_REGION=us-west-2"
]
```

2. **Environment Variables**:

```json
"env": {
  "AWS_ACCESS_KEY_ID": "...",
  "AWS_SECRET_ACCESS_KEY": "...",
  "AWS_REGION": "us-west-2"
}
```

3. **IAM Role** (for EC2/ECS/Lambda): No additional config needed - uses instance credentials

### Option B: AWS Documentation MCP Server (No Auth)

**Use when**:

- No Python/uvx environment
- No AWS credentials
- Only need documentation search (no API execution)

**Configuration** (add to `.mcp.json` in project root):

```json
{
  "mcpServers": {
    "awsdocs": {
      "type": "http",
      "url": "https://knowledge-mcp.global.api.aws"
    }
  }
}
```

### Option C: AWS IaC MCP Server

**Use when**: uvx available AND AWS credentials are valid (or optional for docs-only)

**Configuration** (add to `.mcp.json`):

```json
{
  "mcpServers": {
    "awslabs.aws-iac-mcp-server": {
      "command": "uvx",
      "args": ["awslabs.aws-iac-mcp-server@latest"],
      "env": {
        "AWS_PROFILE": "your-named-profile",
        "FASTMCP_LOG_LEVEL": "ERROR"
      },
      "disabled": false,
      "autoApprove": []
    }
  }
}
```

**Windows Configuration**:

```json
{
  "mcpServers": {
    "awslabs.aws-iac-mcp-server": {
      "disabled": false,
      "timeout": 60,
      "type": "stdio",
      "command": "uv",
      "args": [
        "tool", "run", "--from",
        "awslabs.aws-iac-mcp-server@latest",
        "awslabs.aws-iac-mcp-server.exe"
      ],
      "env": {
        "FASTMCP_LOG_LEVEL": "ERROR",
        "AWS_PROFILE": "your-aws-profile",
        "AWS_REGION": "us-east-1"
      }
    }
  }
}
```

### Option D: Terraform MCP Server

**Use when**: uvx, python, terraform CLI AND Checkov is installed.

**Configuration** (add to `.mcp.json`):

```json
{
  "mcpServers": {
    "awslabs.terraform-mcp-server": {
      "command": "uvx",
      "args": ["awslabs.terraform-mcp-server@latest"],
      "env": {
        "FASTMCP_LOG_LEVEL": "ERROR"
      },
      "disabled": false,
      "autoApprove": []
    }
  }
}
```

### Option E: AWS Bedrock AgentCore MCP Server

**Use when**: uvx AND python is already installed.

**Configuration** (add to `~/.claude/settings.json` under `mcpServers`):

```json
{
  "mcpServers": {
    "bedrock-agentcore-mcp-server": {
      "command": "uvx",
      "args": ["awslabs.amazon-bedrock-agentcore-mcp-server@latest"],
      "env": {
        "FASTMCP_LOG_LEVEL": "ERROR"
      },
      "disabled": false,
      "autoApprove": ["search_agentcore_docs", "fetch_agentcore_doc"]
    }
  }
}
```

### Option F: Context7 MCP Server

**Use when**: You need up-to-date documentation and code examples for any programming library or framework.

**Prerequisites**: None - no dependencies or credentials required.

**Configuration** (add to `~/.claude/settings.json` under `mcpServers`):

```json
{
  "mcpServers": {
    "user-Context7": {
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp@latest"]
    }
  }
}
```

**Usage**:
1. Use `resolve-library-id` to find the Context7-compatible library ID
2. Use `query-docs` with the library ID to retrieve documentation and code examples

## Step 3: Verification

After configuration, restart Claude Code, then verify:

**For Full AWS MCP**: look for tools matching `mcp__aws-mcp__*`
**For Documentation MCP**: look for tools matching `mcp__awsdocs__*`
**For AWS IaC MCP Server**: look for `mcp__awslabs.aws-iac-mcp-server__*`
**For Terraform MCP Server**: look for `mcp__awslabs.terraform-mcp-server__*`
**For Bedrock AgentCore MCP Server**: look for `mcp__bedrock-agentcore-mcp-server__*`
**For Context7 MCP Server**: look for `mcp__user-Context7__*`

## Note on Project vs. User Config

- `.mcp.json` at project root = team-shared (Options B, C, D already pre-configured in this project)
- `~/.claude/settings.json` = personal/user-scoped (Options A, E, F — require credentials or are personal preference)

## Troubleshooting

| Issue                          | Cause                       | Solution                                                                   |
| ------------------------------ | --------------------------- | -------------------------------------------------------------------------- |
| `uvx: command not found`       | uv not installed            | Install with `pip install uv` or use Option B                              |
| `AccessDenied` error           | Missing IAM permissions     | Check IAM policy for required permissions                                  |
| `InvalidSignatureException`    | Credential issue            | Check `aws sts get-caller-identity`                                        |
| Tools not appearing            | MCP not started             | Restart Claude Code after config change                                    |
| `terraform: command not found` | Terraform CLI not installed | Install from https://developer.hashicorp.com/terraform/downloads           |
| `checkov: command not found`   | Checkov not installed       | Install with `pip install checkov` or `brew install checkov`               |

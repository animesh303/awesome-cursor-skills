---
name: aws-agentcore-agent-workflow
description: Step-by-step workflow for creating complete AI agents on AWS Bedrock using AgentCore services. Use when creating new AI agents, deploying agents to production, or setting up end-to-end agent infrastructure. Covers Runtime creation, agent configuration, optional services (Memory, Gateway, Code Interpreter, Browser, Observability), and testing. Uses MCP servers exclusively for all operations.
context: fork
disable-model-invocation: true
allowed-tools:
  - mcp__bedrock-agentcore-mcp-server__*
  - mcp__aws-mcp__*
---

# Creating AI Agents on AWS Bedrock with AgentCore

Complete workflow for creating and deploying AI agents using AWS Bedrock AgentCore services via MCP servers.

> **Dependencies**: This skill requires MCP servers. Run `/aws-mcp-setup` to configure them:
> - Option A (Full AWS MCP Server) for API operations
> - Option E (Bedrock AgentCore MCP Server) for documentation and guides

## Prerequisites

Before starting, verify:

- [ ] **MCP servers configured** (required):
  - [ ] Bedrock AgentCore MCP Server (`mcp__bedrock-agentcore-mcp-server__*`)
  - [ ] Full AWS MCP Server (`mcp__aws-mcp__aws___call_aws`)
- [ ] AWS credentials configured (via MCP)
- [ ] AWS account has Bedrock AgentCore access enabled
- [ ] Required IAM permissions for AgentCore operations
- [ ] Target AWS region selected (e.g., `us-west-2`)
- [ ] Bedrock model access configured (if using specific models)

## Core Workflow

### Step 1: Create Runtime

Runtime provides the serverless execution environment for your agent.

1. **Get Runtime deployment guide** using Bedrock AgentCore MCP Server:
   - Use `mcp__bedrock-agentcore-mcp-server__manage_agentcore_runtime` to get comprehensive Runtime deployment instructions

2. **Create runtime** using AWS MCP Server:
   - Use `mcp__aws-mcp__aws___call_aws` with service `bedrock-agentcore-control`, operation `CreateRuntime`
   - Required parameters: `name` (runtime name), `region` (AWS region)

3. **Save the runtime identifier** from the response for use in subsequent steps.

4. **Verify runtime:**
   - Use `mcp__aws-mcp__aws___call_aws` with service `bedrock-agentcore-control`, operation `GetRuntime`, and the runtime identifier

### Step 2: Create Agent

Create the AI agent with your chosen foundation model.

1. **Create agent** using AWS MCP Server:
   - Use `mcp__aws-mcp__aws___call_aws` with service `bedrock-agentcore-control`, operation `CreateAgent`
   - Required parameters: `runtimeIdentifier`, `name`, `agentConfiguration` (with `modelId`), `region`
   - Optional: `temperature`, `maxTokens` in agentConfiguration

2. **Save the agent identifier** from the response for use in subsequent steps.

3. **Verify agent:**
   - Use `mcp__aws-mcp__aws___call_aws` with service `bedrock-agentcore-control`, operation `GetAgent`

### Step 3: Test Basic Agent

Invoke the agent to verify it's working:

- Use `mcp__aws-mcp__aws___call_aws` with service `bedrock-agentcore-runtime`, operation `InvokeAgent`
- Required parameters: `runtimeIdentifier`, `agentIdentifier`, `input` (with `messages` array), `region`
- Example message: `{"role": "user", "content": "Hello, can you introduce yourself?"}`

## Optional Services

After creating your basic agent, you can enhance it with optional services:

- **Memory**: Persistent conversation memory across sessions
- **Gateway**: Connect external REST APIs as tools
- **Code Interpreter**: Secure Python code execution
- **Browser**: Web automation and scraping
- **Observability**: Monitoring, logging, and tracing

**For detailed setup instructions:** See `.cursor/skills/aws-agentcore-agent-workflow/references/optional-services.md`

## Workflow Checklist

```
Agent Creation Progress:
- [ ] MCP servers configured (Bedrock AgentCore MCP Server, AWS MCP Server)
- [ ] Prerequisites verified (AWS credentials via MCP, permissions, region)
- [ ] Runtime created and verified
- [ ] Agent created with foundation model
- [ ] Basic agent invocation tested
- [ ] Memory service configured (if needed)
- [ ] Gateway targets deployed (if needed)
- [ ] Code Interpreter enabled (if needed)
- [ ] Browser service enabled (if needed)
- [ ] Observability configured
- [ ] Agent tested with all enabled services
- [ ] IAM permissions verified for production
- [ ] CloudWatch alarms configured (optional)
```

## Common Configurations

- **Basic Conversational Agent**: Runtime + Agent only
- **Agent with Memory**: Runtime + Agent + Memory
- **Full-Featured Agent**: Runtime + Agent + Memory + Code Interpreter + Browser + Observability

**For detailed configuration examples:** See `.cursor/skills/aws-agentcore-agent-workflow/references/common-configurations.md`

## Additional Resources

- **MCP Documentation Tools:**
  - `mcp__bedrock-agentcore-mcp-server__search_agentcore_docs` — Search AgentCore documentation
  - `mcp__bedrock-agentcore-mcp-server__fetch_agentcore_doc` — Fetch complete documentation pages
  - `mcp__bedrock-agentcore-mcp-server__manage_agentcore_runtime` — Runtime deployment guide
  - `mcp__bedrock-agentcore-mcp-server__manage_agentcore_memory` — Memory management guide
  - `mcp__bedrock-agentcore-mcp-server__manage_agentcore_gateway` — Gateway deployment guide
- **Credential management:** `.cursor/skills/aws-agentcore/references/credential-management.md`
- **Testing:** `.cursor/skills/aws-agentcore-agent-workflow/references/testing.md`
- **Troubleshooting:** `.cursor/skills/aws-agentcore-agent-workflow/references/troubleshooting.md`
- **Service-specific guides:** `.cursor/skills/aws-agentcore/references/`

---
name: aws-cdk-development
description: AWS Cloud Development Kit (CDK) expert for building cloud infrastructure with TypeScript/Python. Use when creating CDK stacks, defining CDK constructs, implementing infrastructure as code, or when the user mentions CDK, CloudFormation, IaC, cdk synth, cdk deploy, or wants to define AWS infrastructure programmatically. Covers CDK app structure, construct patterns, stack composition, and deployment workflows.
context: fork
allowed-tools:
  - mcp__aws-mcp__*
  - mcp__awsdocs__*
  - mcp__awslabs.aws-iac-mcp-server__*
  - Bash(cdk *)
  - Bash(npm *)
  - Bash(npx *)
  - Bash(aws cloudformation *)
  - Bash(aws sts get-caller-identity)
hooks:
  PreToolUse:
    - matcher: "Bash"
      hooks:
        - type: command
          command: "aws sts get-caller-identity --query Account --output text"
          once: true
---

# AWS CDK Development

This skill provides comprehensive guidance for developing AWS infrastructure using the Cloud Development Kit (CDK), with integrated MCP servers for accessing latest AWS knowledge and CDK utilities.

> **Dependency**: This skill requires AWS MCP tools. If MCP tools are unavailable, run `/aws-mcp-setup` to configure them first.

## AWS Documentation Requirement

**CRITICAL**: Always verify using AWS MCP tools (if available):

- `mcp__aws-mcp__aws___search_documentation` or `mcp__awsdocs__aws___search_documentation` — Search AWS docs
- `mcp__aws-mcp__aws___read_documentation` or `mcp__awsdocs__aws___read_documentation` — Read specific pages
- `mcp__aws-mcp__aws___get_regional_availability` — Check service availability

If AWS MCP tools are unavailable, run `/aws-mcp-setup` to configure them. Choose the option that fits your environment:
- Has uvx + AWS credentials → Full AWS MCP Server
- No Python/credentials → AWS Documentation MCP (no auth)

## Integrated MCP Servers

### AWS CDK MCP Server

If `awslabs.aws-iac-mcp-server` is configured (`mcp__awslabs.aws-iac-mcp-server__*`), use it for CDK-specific guidance:
- Get CDK construct recommendations
- Retrieve CDK best practices (`cdk_best_practices`)
- Search CDK samples (`search_cdk_samples_and_constructs`)
- Search CDK documentation (`search_cdk_documentation`)

## When to Use This Skill

Use this skill when:
- Creating new CDK stacks or constructs
- Refactoring existing CDK infrastructure
- Implementing Lambda functions within CDK
- Following AWS CDK best practices
- Validating CDK stack configurations before deployment
- Verifying AWS service capabilities and regional availability

## Core CDK Principles

### Resource Naming

**CRITICAL**: Do NOT explicitly specify resource names when they are optional in CDK constructs.

**Why**: CDK-generated names enable:
- **Reusable patterns**: Deploy the same construct/pattern multiple times without conflicts
- **Parallel deployments**: Multiple stacks can deploy simultaneously in the same region
- **Stack isolation**: Each stack gets uniquely identified resources automatically

```typescript
// ❌ BAD - Explicit naming prevents reusability and parallel deployments
new lambda.Function(this, 'MyFunction', {
  functionName: 'my-lambda', // Avoid this
});

// ✅ GOOD - Let CDK generate unique names
new lambda.Function(this, 'MyFunction', {
  // No functionName specified - CDK generates: StackName-MyFunctionXXXXXX
});
```

**Security Note**: For different environments (dev, staging, prod), use separate AWS accounts rather than relying on resource naming within a single account.

### Lambda Function Development

**TypeScript/JavaScript**: Use `@aws-cdk/aws-lambda-nodejs`
```typescript
import { NodejsFunction } from 'aws-cdk-lib/aws-lambda-nodejs';

new NodejsFunction(this, 'MyFunction', {
  entry: 'lambda/handler.ts',
  handler: 'handler',
  // Automatically handles bundling, dependencies, and transpilation
});
```

**Python**: Use `@aws-cdk/aws-lambda-python`
```typescript
import { PythonFunction } from '@aws-cdk/aws-lambda-python-alpha';

new PythonFunction(this, 'MyFunction', {
  entry: 'lambda',
  index: 'handler.py',
  handler: 'handler',
  // Automatically handles dependencies and packaging
});
```

### Pre-Deployment Validation

Use a **multi-layer validation strategy**:

#### Layer 1: Real-Time IDE Feedback

Install [cdk-nag](https://github.com/cdklabs/cdk-nag) for synthesis-time validation:
```bash
npm install --save-dev cdk-nag
```

Add to your CDK app:
```typescript
import { Aspects } from 'aws-cdk-lib';
import { AwsSolutionsChecks } from 'cdk-nag';

const app = new App();
Aspects.of(app).add(new AwsSolutionsChecks());
```

#### Layer 2: Synthesis-Time Validation (Required)

```bash
cdk synth # cdk-nag runs automatically via Aspects
```

Suppress legitimate exceptions with documented reasons:
```typescript
import { NagSuppressions } from 'cdk-nag';

NagSuppressions.addResourceSuppressions(resource, [
  {
    id: 'AwsSolutions-L1',
    reason: 'Lambda@Edge requires specific runtime for CloudFront compatibility'
  }
]);
```

#### Layer 3: Pre-Commit Safety Net

```bash
npm run build   # Ensure compilation succeeds
npm test        # Run unit and integration tests
cdk synth       # Validate synthesis
```

## Workflow Guidelines

### Development Workflow

1. **Design**: Plan infrastructure resources and relationships
2. **Verify AWS Services**: Use AWS Documentation MCP to confirm service availability
3. **Implement**: Write CDK constructs following best practices
4. **Validate**: Run pre-deployment checks
5. **Synthesize**: `cdk synth`
6. **Review**: Examine synthesized templates
7. **Deploy**: `cdk deploy`
8. **Verify**: Confirm resources are created correctly

### Stack Organization

- Use nested stacks for complex applications
- Separate concerns into logical construct boundaries
- Export values that other stacks may need
- Use CDK context for environment-specific configuration

### Testing Strategy

- Unit test individual constructs
- Integration test stack synthesis
- Snapshot test CloudFormation templates
- Validate resource properties and relationships

## CDK Patterns Reference

For detailed CDK patterns, anti-patterns, and architectural guidance, refer to:

**File**: `.cursor/skills/aws-cdk-development/references/cdk-patterns.md`

This reference includes:
- Common CDK patterns and their use cases
- Anti-patterns to avoid
- Security best practices
- Cost optimization strategies
- Performance considerations

## Additional Resources

- **Validation Script**: `.cursor/skills/aws-cdk-development/scripts/validate-stack.sh`
- **CDK Patterns**: `.cursor/skills/aws-cdk-development/references/cdk-patterns.md`

## GitHub Actions Integration

When GitHub Actions workflow files exist in the repository, ensure all checks defined in `.github/workflows/` pass before committing. Use the `github-actions-validator` subagent to validate and update these workflows.

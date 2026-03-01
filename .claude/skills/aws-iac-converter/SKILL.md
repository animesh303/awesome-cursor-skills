---
name: aws-iac-converter
description: Convert AWS Infrastructure as Code between CloudFormation, CDK (TypeScript/Python), and Terraform. Use when converting IaC code from one format to another, migrating infrastructure definitions, or when the user mentions IaC conversion, CloudFormation to Terraform, CDK to CloudFormation, Terraform to CDK, or any AWS IaC migration.
disable-model-invocation: true
allowed-tools:
  - mcp__awslabs.aws-iac-mcp-server__*
  - mcp__awslabs.terraform-mcp-server__*
  - mcp__aws-mcp__*
  - mcp__awsdocs__*
  - Bash(git *)
  - Bash(terraform *)
  - Bash(cdk *)
  - Bash(aws cloudformation *)
  - Bash(npm *)
  - Bash(npx *)
  - Bash(pip *)
  - Bash(python *)
---

# AWS IaC Converter

Convert AWS Infrastructure as Code between CloudFormation, CDK (TypeScript/Python), and Terraform with a structured, interactive workflow.

> **Dependency**: This skill requires AWS MCP tools. If MCP tools are unavailable, run `/aws-mcp-setup` first.

## Supported Conversions

| Source            | Target Options                           |
| ----------------- | ---------------------------------------- |
| **CloudFormation** | CDK (TypeScript/Python), Terraform       |
| **CDK**            | CloudFormation, Terraform                |
| **Terraform**      | CloudFormation, CDK (TypeScript/Python)  |

## MCP Server Integration

| Server                              | Purpose                                                      |
| ----------------------------------- | ------------------------------------------------------------ |
| **AWS IaC MCP Server**              | CloudFormation validation, CDK docs, compliance checking     |
| **Terraform MCP Server**            | Terraform validation, security scanning, provider docs       |
| **AWS Documentation MCP**           | Service feature verification, API reference                  |

### Before Converting

1. **Always verify** resource properties using MCP tools to ensure accurate mapping
2. If MCP tools are unavailable, run `/aws-mcp-setup` to configure them

### Workflow Structure

This skill keeps its own planning and verification steps:

- **Planning (Phases 1–4)** — Repo analysis, IaC detection, target selection, and conversion plan are IaC-conversion specific.
- **Implementation (Phase 5)** — Delegated to the `orchestrator` subagent's implementer.
- **Verification & Reporting (Phases 6–7)** — Conversion-specific validation and `CONVERSION-REPORT.md` generation.

## Workflow

### Phase 1: Repository Input

1. **Ask the user** for the source code repository URL.
   - **Do NOT proceed** until the user provides the URL.
   - Accept: GitHub URL, GitLab URL, Bitbucket URL, or local path.
2. If the URL is a remote repository, clone it into a temporary working directory.
3. If the URL is a local path, confirm it exists and is accessible.

### Phase 2: Analysis & Detection

4. **Analyze the repository** to identify all AWS IaC code. Run the analysis script:

   ```bash
   bash .cursor/skills/aws-iac-converter/scripts/analyze-repo.sh <repo-path>
   ```

5. **Identify the IaC type(s)** found. Detection rules: `.cursor/skills/aws-iac-converter/references/analysis-guide.md`.
6. **Present findings** to the user:
   - IaC type detected (CloudFormation, CDK, Terraform, or mixed)
   - Files and directories containing IaC code
   - AWS resources identified
   - Any non-IaC code detected

### Phase 3: Target Selection

7. **Ask the user** for the target IaC type.
   - **Do NOT proceed** until the user provides the target type.
   - The target type **must be different** from the source type.
   - If CDK is selected, ask for the language preference (TypeScript or Python).
   - Validate the selection and confirm with the user.

### Phase 4: Conversion Planning

8. **Create a detailed conversion plan** covering:
   - **Target output path** — Directory where converted code will be written (e.g. `<workspace>/converted-terraform`).
   - Resource-by-resource mapping from source to target
   - Parameter/variable conversion strategy
   - Output/export handling
   - State management considerations (especially for Terraform)
   - Dependencies and ordering
   - Resources that **cannot** be directly converted (with workarounds)
   - Conversion patterns: `.cursor/skills/aws-iac-converter/references/conversion-patterns.md`

9. **Present the plan** to the user for review.
   - Show a summary table of resources and their conversion status
   - Highlight any risks, limitations, or manual steps required
   - **Do NOT proceed** until the user approves the plan.

### Phase 5: Implementation

10. **Delegate implementation to the orchestrator subagent** — Invoke the orchestrator agent with:
    - **Input**: The approved conversion plan (including target output path), target IaC stack/scope, and source repo path.
    - **Orchestrator**: Skip the planner. Run only the implementer subagent for the target IaC stack.
    - **Resume**: After the implementer completes, resume this skill at Phase 6.

### Phase 6: Verification

11. **Verify** the converted code. Run:

    ```bash
    bash .cursor/skills/aws-iac-converter/scripts/validate-conversion.sh <target-path> <target-type>
    ```

12. **Resolve** all lint/validation failures. **Do not resolve** security-specific findings — record them for the report only. See `.cursor/skills/aws-iac-converter/references/verification-checklist.md`.

### Phase 7: Reporting

13. **Generate a conversion report** at `<target-output-path>/CONVERSION-REPORT.md`.
    - Template: `.cursor/skills/aws-iac-converter/references/report-template.md`
    - Include: source-to-target resource mapping, validation results, security findings, recommendations.

14. **Summarize to the user**:
    - Total resources converted vs. total in source
    - Any resources requiring manual attention
    - Location of converted code and report
    - Suggested next steps

## Output Layout

```
<target-output-path>/
├── <converted IaC files>
├── <dependency files>            # package.json, requirements.txt, versions.tf, etc.
├── README.md
└── CONVERSION-REPORT.md
```

## Key Principles

- Wait for user input at checkpoints (repo URL, target type, plan approval). Do not proceed until provided.
- Implementation is **only** via the orchestrator's implementer subagent.
- Resolve lint/validation in Phase 6; record security findings only.

**References:** `.cursor/skills/aws-iac-converter/references/` contains: `analysis-guide.md`, `conversion-patterns.md`, `report-template.md`, `verification-checklist.md`

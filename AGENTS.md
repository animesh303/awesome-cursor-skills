# AGENTS.md

## Cursor Cloud specific instructions

This is a **documentation-only repository** (Awesome Cursor Skills). It contains Markdown templates, Cursor AI configurations (rules, agents, skills, commands), and a few reference shell scripts. There is no application code, no build system, no package manager, and no runtime dependencies.

### Linting

- **Markdown**: `markdownlint-cli2 "**/*.md"` — lints all 55+ `.md` files. Most pre-existing issues are `MD013/line-length` (expected for prose-heavy agent/skill files).
- **Shell scripts**: `shellcheck .cursor/skills/aws-cdk-development/scripts/validate-stack.sh .cursor/skills/aws-iac-converter/scripts/analyze-repo.sh .cursor/skills/aws-iac-converter/scripts/validate-conversion.sh` — the 3 reference shell scripts in `.cursor/skills/`.

### Running scripts

The shell scripts under `.cursor/skills/` are **reference utilities** meant to be used inside other AWS projects, not this repo itself. They can still be invoked for validation:

- `bash .cursor/skills/aws-iac-converter/scripts/analyze-repo.sh <repo-path>` — Detects CloudFormation, CDK, and Terraform in a target repo.
- `bash .cursor/skills/aws-iac-converter/scripts/validate-conversion.sh <target-path> <source-type> <target-type>` — Validates IaC conversion output.
- `bash .cursor/skills/aws-cdk-development/scripts/validate-stack.sh` — Validates a CDK stack (requires a CDK project context).

### Key notes

- No `package.json`, `requirements.txt`, `Makefile`, `Dockerfile`, or CI/CD configuration exists in this repo.
- Contributions are Markdown files; validate with the lint commands above before committing.

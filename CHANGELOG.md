# Changelog

All notable changes to this project will be documented in this file.

## [Unreleased]

### ✨ New Features

- **AWS AgentCore Agent Workflow Skill**: Added comprehensive workflow skill for creating complete AI agents on AWS Bedrock using AgentCore services. Provides step-by-step guidance for Runtime creation, agent configuration, and optional service enhancements (Memory, Gateway, Code Interpreter, Browser, Observability). Uses MCP servers exclusively for all operations.
- **AWS AgentCore Skill**: Added comprehensive AWS Bedrock AgentCore skill for deploying and managing all AgentCore services including Gateway, Runtime, Memory, Identity, Code Interpreter, Browser, and Observability. Includes detailed documentation and reference guides for each service.

### 🔧 Improvements

- **AWS MCP Setup Skill**: Enhanced to include setup instructions for AWS Bedrock AgentCore MCP Server, providing access to AgentCore documentation and management guides.
- **Agent Skills Best Practices**: Refactored workflow skill to follow Agent Skills best practices with progressive disclosure, concise main file (144 lines), and detailed content in reference files.
- **MCP-First Approach**: Removed all AWS CLI command examples from workflow skill, using MCP servers exclusively for operations.
- **Create PR Command**: Updated the `create-pr` command to include a step for generating changelog entries using the changelog-generator agent, ensuring PRs include proper change documentation.
- **Create PR Command**: Enhanced the `create-pr` command workflow to require explicit target branch confirmation before proceeding, preventing accidental PRs to wrong branches. Updated all step references to use "target branch" instead of hardcoded "main" for better flexibility.

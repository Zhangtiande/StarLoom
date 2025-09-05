# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

> Think carefully and implement the most concise solution that changes as little code as possible.

## USE SUB-AGENTS FOR CONTEXT OPTIMIZATION

### 1. Always use the file-analyzer sub-agent when asked to read files.
The file-analyzer agent is an expert in extracting and summarizing critical information from files, particularly log files and verbose outputs. It provides concise, actionable summaries that preserve essential information while dramatically reducing context usage.

### 2. Always use the code-analyzer sub-agent when asked to search code, analyze code, research bugs, or trace logic flow.

The code-analyzer agent is an expert in code analysis, logic tracing, and vulnerability detection. It provides concise, actionable summaries that preserve essential information while dramatically reducing context usage.

### 3. Always use the test-runner sub-agent to run tests and analyze the test results.

Using the test-runner agent ensures:

- Full test output is captured for debugging
- Main conversation stays clean and focused
- Context usage is optimized
- All issues are properly surfaced
- No approval dialogs interrupt the workflow

## Philosophy

### Error Handling

- **Fail fast** for critical configuration (missing text model)
- **Log and continue** for optional features (extraction model)
- **Graceful degradation** when external services unavailable
- **User-friendly messages** through resilience layer

### Testing

- Always use the test-runner agent to execute tests.
- Do not use mock services for anything ever.
- Do not move on to the next test until the current test is complete.
- If the test fails, consider checking if the test is structured correctly before deciding we need to refactor the codebase.
- Tests to be verbose so we can use them for debugging.


## Tone and Behavior

- Criticism is welcome. Please tell me when I am wrong or mistaken, or even when you think I might be wrong or mistaken.
- Please tell me if there is a better approach than the one I am taking.
- Please tell me if there is a relevant standard or convention that I appear to be unaware of.
- Be skeptical.
- Be concise.
- Short summaries are OK, but don't give an extended breakdown unless we are working through the details of a plan.
- Do not flatter, and do not give compliments unless I am specifically asking for your judgement.
- Occasional pleasantries are fine.
- Feel free to ask many questions. If you are in doubt of my intent, don't guess. Ask.

## ABSOLUTE RULES:

- NO PARTIAL IMPLEMENTATION
- NO SIMPLIFICATION : no "//This is simplified stuff for now, complete implementation would blablabla"
- NO CODE DUPLICATION : check existing codebase to reuse functions and constants Read files before writing new functions. Use common sense function name to find them easily.
- NO DEAD CODE : either use or delete from codebase completely
- IMPLEMENT TEST FOR EVERY FUNCTIONS
- NO CHEATER TESTS : test must be accurate, reflect real usage and be designed to reveal flaws. No useless tests! Design tests to be verbose so we can use them for debuging.
- NO INCONSISTENT NAMING - read existing codebase naming patterns.
- NO OVER-ENGINEERING - Don't add unnecessary abstractions, factory patterns, or middleware when simple functions would work. Don't think "enterprise" when you need "working"
- NO MIXED CONCERNS - Don't put validation logic inside API handlers, database queries inside UI components, etc. instead of proper separation
- NO RESOURCE LEAKS - Don't forget to close database connections, clear timeouts, remove event listeners, or clean up file handles

## PROJECT OVERVIEW

### StarLoom - GitHub Stars Management Tool
**Status**: Design phase - no implementation code exists yet
**Target**: Cross-platform desktop application for managing GitHub starred repositories with AI-powered analysis

### Key Requirements (from Design.md)
- Desktop app (not web/Electron) for lighter footprint
- GitHub OAuth integration to fetch user's starred repos
- AI provider integration (OpenAI, Anthropic, etc.) with configurable API keys
- Automated project analysis, tagging, and grouping
- Search, filter, and sort functionality for starred projects
- Modular factory pattern design for AI provider switching

## DEVELOPMENT COMMANDS

### Project Management Commands (CCPM System)
The project uses Claude Code Project Management (CCPM) system. Key commands:

#### Planning & Requirements
- `/pm:prd-new <name>` - Create new Product Requirements Document
- `/pm:prd-parse <name>` - Convert PRD to implementation epic
- `/pm:epic-decompose <name>` - Break epic into individual tasks

#### Task Management  
- `/pm:next` - Show next priority tasks
- `/pm:issue-start <num>` - Start work on specific task with agent
- `/pm:status` - Overall project dashboard
- `/pm:standup` - Daily standup report

#### GitHub Integration
- `/pm:sync` - Sync all content with GitHub issues
- `/pm:epic-sync <name>` - Push specific epic to GitHub

#### System Utilities
- `/pm:init` - Initialize system dependencies (GitHub CLI, etc.)
- `/pm:validate` - Check system integrity
- `/pm:help` - Complete command reference

### Testing Commands
- `/testing:prime` - Configure testing framework
- `/testing:run [target]` - Execute tests with test-runner agent
- Test logging: `./claude/scripts/test-and-log.sh <test_file> [log_name]`

### Context Management
- `/context:create` - Generate initial project context documentation
- `/context:update` - Refresh context with recent changes  
- `/context:prime` - Load context into conversation

## ARCHITECTURE PATTERNS

### Agent-Based Development
This project uses specialized agents for heavy work while preserving context:

- **code-analyzer**: Bug hunting, logic tracing across multiple files
- **file-analyzer**: Summarizing verbose outputs (80-90% size reduction)
- **test-runner**: Test execution with intelligent failure analysis
- **parallel-worker**: Coordinating multiple simultaneous work streams

### Command-Agent Pattern
- Structured commands in `.claude/commands/` define workflows
- Commands spawn appropriate agents for heavy lifting
- Results return as concise summaries to main thread
- Parallel execution without context pollution

### Project Structure
```
.claude/
├── CLAUDE.md          # This file - core project instructions
├── agents/            # Agent definitions and patterns  
├── commands/          # Structured development commands
├── context/           # Project context documentation
├── epics/             # Implementation epics from PRDs
├── prds/              # Product Requirements Documents
├── rules/             # Development patterns and standards
└── scripts/           # Automation scripts
```

## IMPLEMENTATION GUIDANCE

### When Building the StarLoom Application
1. **Choose lightweight desktop framework** (consider Tauri, Flutter Desktop, or native)
2. **Implement AI provider factory** for OpenAI/Anthropic/others switching
3. **Modular GitHub API integration** for repo fetching and analysis
4. **Follow existing patterns** from `.claude/rules/` for consistency
5. **Use agent system** for complex operations (API calls, data processing)

### Development Workflow
1. Start with `/context:prime` to load project understanding
2. Use `/pm:next` to identify priority work
3. Create PRDs with `/pm:prd-new` for new features
4. Break down work with `/pm:epic-decompose`
5. Execute with `/pm:issue-start` using specialized agents
6. Test with `/testing:run` using test-runner agent
7. Sync progress with `/pm:sync`
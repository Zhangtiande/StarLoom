---
created: 2025-09-05T14:54:20Z
last_updated: 2025-09-05T14:54:20Z
version: 1.0
author: Claude Code PM System
---

# Project Structure

## Current Directory Organization

```
/Users/fcayj/workspace/10.StarLoom/
├── Design.md                    # Project requirements (Chinese)
├── CLAUDE.md                    # Claude Code guidance (English)
├── COMMANDS.md                  # Development command reference
├── AGENTS.md                    # Agent system documentation
├── .claude/                     # Development infrastructure
│   ├── CLAUDE.md               # Original project instructions
│   ├── agents/                 # Agent definitions
│   ├── commands/               # Structured development commands
│   │   ├── pm/                 # Project management commands (40+)
│   │   ├── context/            # Context management commands
│   │   ├── testing/            # Testing workflow commands
│   │   └── *.md               # Utility commands
│   ├── context/                # Project context documentation (NEW)
│   ├── epics/                  # Implementation epics (empty)
│   ├── prds/                   # Product requirements (empty)
│   ├── rules/                  # Development patterns
│   └── scripts/                # Automation scripts
│       ├── pm/                 # PM system bash scripts
│       └── test-and-log.sh     # Test execution script
├── .idea/                      # IDE configuration
└── [No application code yet]
```

## Key Directory Purposes

### `/` (Root Level)
- **Design.md**: Primary requirements document in Chinese
- **CLAUDE.md**: Main development guidance for Claude instances
- **COMMANDS.md**: User-facing command documentation
- **AGENTS.md**: Agent system architecture documentation

### `/.claude/` (Development Infrastructure)
**Core PM System**: Complete project management infrastructure
- **commands/**: 40+ structured development commands
- **scripts/**: Bash automation for PM operations
- **agents/**: Agent system definitions and patterns
- **rules/**: Development standards and patterns

**Project Documentation**:
- **context/**: Project context files (this directory)
- **epics/**: Implementation epics from PRDs
- **prds/**: Product Requirements Documents

### `/.idea/` (IDE Configuration)
- IntelliJ IDEA project configuration
- Should be included in .gitignore when git is initialized

## File Naming Patterns

### Documentation Files
- **Markdown**: All documentation uses `.md` extension
- **Lowercase with hyphens**: `project-structure.md`, `tech-context.md`
- **Clear descriptive names**: Files immediately convey their purpose

### Command Files
- **Category-based**: `pm/epic-start.md`, `context/create.md`
- **Verb-noun pattern**: `epic-start`, `issue-analyze`, `context-prime`
- **Consistent frontmatter**: All commands include YAML metadata

### Context Files
- **Descriptive prefixes**: `project-*`, `tech-*`, `system-*`
- **Standardized frontmatter**: Created/updated timestamps, version, author
- **Markdown formatting**: Consistent heading structure

## Planned Application Structure

Based on Design.md requirements, the future application structure should include:

```
src/                          # Application source code
├── main/                     # Main application entry
├── ui/                       # User interface components
├── github/                   # GitHub API integration
├── ai/                       # AI provider integrations
│   ├── providers/            # Factory pattern implementations
│   ├── openai/              # OpenAI integration
│   ├── anthropic/           # Anthropic integration
│   └── factory.js/rs/py     # Provider factory
├── data/                     # Data management layer
├── utils/                    # Utility functions
└── config/                   # Configuration management

tests/                        # Test suite
├── unit/                     # Unit tests
├── integration/              # Integration tests
├── e2e/                      # End-to-end tests
└── logs/                     # Test execution logs

build/                        # Build artifacts
dist/                         # Distribution packages
docs/                         # User documentation
```

## Module Organization Principles

### Separation of Concerns
- **UI Layer**: Pure presentation logic
- **Business Logic**: Core application functionality
- **Data Layer**: GitHub API and local storage
- **AI Layer**: Provider abstractions and analysis

### Factory Pattern for AI Providers
- **Abstract Provider Interface**: Common methods across providers
- **Concrete Implementations**: OpenAI, Anthropic, others
- **Factory Class**: Provider instantiation and switching
- **Configuration Management**: API keys and provider settings

### Cross-Platform Considerations
- **Native APIs**: Platform-specific functionality isolated
- **Shared Core**: Maximum code reuse across platforms
- **Build System**: Platform-specific build targets
- **Distribution**: Platform-specific packaging

## Integration Points

### External Services
- **GitHub API**: Repository metadata, user authentication
- **AI Providers**: Text analysis, tagging, summarization
- **Local Storage**: Project metadata, user preferences

### Internal Modules
- **Configuration → AI Factory**: Provider selection and setup
- **GitHub Client → Data Layer**: Repository information flow
- **AI Analysis → UI**: Analysis results presentation
- **User Input → All Layers**: Settings, manual tagging, grouping

## Notes

The current structure prioritizes development infrastructure over application code, which is appropriate for the design phase. The comprehensive PM system provides excellent scaffolding for structured development once implementation begins.

The absence of typical project files (package.json, Cargo.toml, etc.) indicates technology stack decisions are still pending, which aligns with the design phase status.
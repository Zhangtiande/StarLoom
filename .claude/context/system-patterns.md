---
created: 2025-09-05T14:54:20Z
last_updated: 2025-09-05T14:54:20Z
version: 1.0
author: Claude Code PM System
---

# System Patterns

## Observed Architectural Patterns

### Command-Agent Pattern
**Pattern**: Structured commands delegate heavy work to specialized agents

**Implementation**:
- **Commands**: Markdown files with YAML frontmatter define workflows
- **Agents**: Specialized AI workers handle context-heavy operations
- **Communication**: Commands spawn agents, agents return concise summaries
- **Context Preservation**: Agents act as "context firewalls" preventing information overload

**Example Flow**:
```
User Request → Command Definition → Agent Spawn → Heavy Processing → Summary Return
/pm:epic-start → epic-start.md → parallel-worker → Multi-file analysis → Status summary
```

**Benefits**:
- Context usage optimization (80-90% reduction)
- Parallel execution without context collision
- Modular workflow definition
- Consistent error handling patterns

### Factory Pattern (Planned)
**Pattern**: AI provider abstraction through factory instantiation

**Planned Implementation**:
```rust
trait AIProvider {
    async fn analyze(&self, content: &str) -> Result<Analysis>;
}

struct ProviderFactory;
impl ProviderFactory {
    fn create(provider_type: ProviderType, api_key: String) -> Box<dyn AIProvider> {
        match provider_type {
            ProviderType::OpenAI => Box::new(OpenAIProvider::new(api_key)),
            ProviderType::Anthropic => Box::new(AnthropicProvider::new(api_key)),
        }
    }
}
```

**Benefits**:
- Runtime provider switching
- Consistent interface across providers
- Easy addition of new providers
- Configuration-driven behavior

### Documentation-as-Code Pattern
**Pattern**: All project knowledge exists as versioned, structured documentation

**Implementation**:
- **Context Files**: Project state, architecture, progress tracked in markdown
- **Command Definitions**: Workflows defined as executable documentation
- **Frontmatter Metadata**: YAML headers provide structured data
- **Living Documentation**: Files update with project evolution

**Benefits**:
- Knowledge persistence across sessions
- Searchable project intelligence
- Version-controlled insights
- Onboarding acceleration

### Fail-Fast Error Handling
**Pattern**: Check prerequisites immediately, provide clear guidance

**Implementation Examples**:
```bash
# Preflight validation in commands
if ! command -v gh &> /dev/null; then
    echo "❌ GitHub CLI not found. Install with: brew install gh"
    exit 1
fi
```

**Principles**:
- Validate critical dependencies before execution
- Provide actionable error messages
- Never leave partial or corrupted state
- Clear success/failure indicators

## Data Flow Patterns

### Context Management Flow
```
Session Start → /context:prime → Load context files → Working memory
Work Session → Context updates → /context:update → Refresh files
```

### Project Management Flow
```
Requirements → /pm:prd-new → PRD Creation
PRD → /pm:prd-parse → Epic Generation
Epic → /pm:epic-decompose → Task Breakdown
Tasks → /pm:issue-start → Agent Execution
Progress → /pm:status → Dashboard View
```

### Agent Coordination Flow
```
Command → Agent Spawn → Context Isolation → Processing → Summary Return
Parallel Commands → Multiple Agents → Independent Execution → Consolidated Results
```

## Design Principles Applied

### Conciseness Over Verbosity
- **Commands return summaries, not raw output**
- **Context files focus on essential information**
- **Error messages are direct and actionable**
- **Documentation avoids redundancy**

### Modularity Over Monoliths
- **Commands are single-purpose and composable**
- **Agents handle specific types of work**
- **Context files address distinct concerns**
- **Scripts follow Unix philosophy (do one thing well)**

### Automation Over Manual Process
- **Bash scripts handle repetitive operations**
- **Commands encapsulate complex workflows**
- **Agents eliminate manual file reading**
- **PM system automates project tracking**

### Standards Over Ad-hoc Solutions
- **Consistent frontmatter across all files**
- **Standardized error handling patterns**
- **Common naming conventions**
- **Structured command interfaces**

## Anti-Patterns to Avoid

### ❌ Context Explosion
**Problem**: Verbose output overwhelming main conversation
**Solution**: Always use agents for heavy processing

### ❌ Partial Implementation
**Problem**: Incomplete features with TODO comments
**Solution**: ABSOLUTE RULES enforce complete implementation

### ❌ Code Duplication
**Problem**: Similar functions across modules
**Solution**: Read existing code before writing new functions

### ❌ Mixed Concerns
**Problem**: Business logic in UI, validation in API handlers
**Solution**: Clear separation of concerns in architecture

### ❌ Resource Leaks
**Problem**: Unclosed connections, uncleared timeouts
**Solution**: Explicit cleanup in all resource usage

## Planned Application Patterns

### Repository Pattern (Data Layer)
```rust
trait RepositoryStore {
    async fn save_repository(&self, repo: &Repository) -> Result<()>;
    async fn find_by_id(&self, id: u64) -> Result<Option<Repository>>;
    async fn find_starred_by_user(&self, user: &str) -> Result<Vec<Repository>>;
}
```

### Observer Pattern (UI Updates)
```rust
trait AnalysisObserver {
    fn on_analysis_complete(&self, repo_id: u64, analysis: Analysis);
    fn on_analysis_error(&self, repo_id: u64, error: Error);
}
```

### Strategy Pattern (Analysis Methods)
```rust
trait AnalysisStrategy {
    async fn analyze(&self, repository: &Repository) -> Result<Analysis>;
}

struct QuickAnalysis; // Basic metadata analysis
struct DeepAnalysis;  // Full README + code analysis
struct CachedAnalysis; // Check cache first
```

### Builder Pattern (Configuration)
```rust
struct AppConfigBuilder {
    github_token: Option<String>,
    ai_provider: Option<ProviderType>,
    cache_duration: Option<Duration>,
}

impl AppConfigBuilder {
    fn github_token(mut self, token: String) -> Self { /*...*/ }
    fn ai_provider(mut self, provider: ProviderType) -> Self { /*...*/ }
    fn build(self) -> Result<AppConfig> { /*...*/ }
}
```

## Quality Patterns

### Testing Patterns
- **No Mocking**: Use real services for integration testing
- **Verbose Tests**: Include detailed logging for debugging
- **Test-Driven**: Write tests for every function
- **End-to-End**: Test complete user workflows

### Performance Patterns
- **Async First**: Non-blocking I/O for all external calls
- **Caching Strategy**: Intelligent caching with invalidation
- **Lazy Loading**: Load data only when requested
- **Resource Pooling**: Connection reuse for external APIs

### Security Patterns
- **Credential Management**: OS keychain integration
- **Input Validation**: Sanitize all external data
- **Rate Limiting**: Respect API limits with backoff
- **Secure Defaults**: Fail closed, encrypt by default

## Integration Patterns

### External Service Integration
- **Circuit Breaker**: Handle service outages gracefully
- **Retry Logic**: Exponential backoff for transient failures
- **Timeout Handling**: Prevent hanging operations
- **Graceful Degradation**: Continue operating with reduced functionality

### Cross-Platform Patterns
- **Abstraction Layers**: Platform-specific code isolated
- **Feature Detection**: Runtime capability checking
- **Configuration Management**: Platform-appropriate defaults
- **Build Targets**: Conditional compilation for platforms

## Notes

These patterns reflect both the current PM system architecture and the planned application architecture. The PM system demonstrates excellent patterns for documentation-driven development and context management that should be extended into the application design.

The emphasis on fail-fast, no-partial-implementation, and context optimization creates a robust foundation for building reliable software.
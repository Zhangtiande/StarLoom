---
created: 2025-09-05T14:54:20Z
last_updated: 2025-09-05T14:54:20Z
version: 1.0
author: Claude Code PM System
---

# Project Style Guide

## Development Philosophy

### Core Principles
Based on the established ABSOLUTE RULES from CLAUDE.md:

- **NO PARTIAL IMPLEMENTATION**: Every feature must be complete before merge
- **NO SIMPLIFICATION**: No TODO comments or "simplified for now" code
- **NO CODE DUPLICATION**: Check existing codebase, reuse functions and constants
- **NO DEAD CODE**: Either use or delete completely
- **IMPLEMENT TEST FOR EVERY FUNCTION**: Comprehensive testing required
- **NO CHEATER TESTS**: Tests must be accurate and designed to reveal flaws
- **NO INCONSISTENT NAMING**: Follow established patterns throughout codebase
- **NO OVER-ENGINEERING**: Simple functions over unnecessary abstractions
- **NO MIXED CONCERNS**: Clear separation of responsibilities
- **NO RESOURCE LEAKS**: Proper cleanup of connections, timeouts, handlers

### Error Handling Philosophy
- **Fail Fast**: Check critical prerequisites immediately
- **Log and Continue**: Optional features should degrade gracefully
- **Graceful Degradation**: Continue operating when external services unavailable
- **User-Friendly Messages**: Clear, actionable error communication

## Code Style Conventions

### File Organization

#### Directory Structure
```
src/
├── main/           # Application entry point
├── ui/             # User interface components
├── github/         # GitHub API integration
├── ai/             # AI provider integrations
├── data/           # Data access and storage
├── utils/          # Utility functions
└── config/         # Configuration management
```

#### File Naming
- **Lowercase with underscores**: `github_client.rs`, `ai_factory.py`
- **Descriptive names**: File purpose immediately clear
- **Consistent extensions**: `.rs` for Rust, `.py` for Python, etc.
- **Test files**: `test_module_name.rs` or `module_name_test.py`

### Naming Conventions

#### Variables and Functions
```rust
// Good: Clear, descriptive names
let repository_count = starred_repos.len();
async fn fetch_starred_repositories(client: &GitHubClient) -> Result<Vec<Repository>>

// Bad: Abbreviated or unclear
let repo_cnt = repos.len();
async fn get_stars(c: &GHClient) -> Result<Vec<Repo>>
```

#### Constants and Configuration
```rust
// Good: Screaming snake case for constants
const DEFAULT_CACHE_DURATION: Duration = Duration::from_secs(3600);
const MAX_REPOSITORIES_PER_BATCH: usize = 100;

// Good: Clear configuration key names
struct AppConfig {
    github_api_token: String,
    ai_provider_type: ProviderType,
    cache_directory: PathBuf,
}
```

#### Types and Structs
```rust
// Good: PascalCase for types
struct RepositoryAnalysis {
    summary: String,
    tags: Vec<String>,
    category: Category,
}

enum AIProvider {
    OpenAI { api_key: String },
    Anthropic { api_key: String },
}
```

### Code Organization Patterns

#### Function Structure
```rust
// Good: Clear function structure
async fn analyze_repository(
    repository: &Repository,
    ai_client: &dyn AIProvider,
) -> Result<RepositoryAnalysis> {
    // 1. Input validation
    if repository.description.is_empty() {
        return Err(AnalysisError::InsufficientData);
    }
    
    // 2. Main logic
    let analysis_prompt = build_analysis_prompt(repository);
    let response = ai_client.analyze(&analysis_prompt).await?;
    
    // 3. Result processing
    let analysis = parse_analysis_response(response)?;
    
    // 4. Return
    Ok(analysis)
}
```

#### Error Handling Patterns
```rust
// Good: Comprehensive error handling
#[derive(Debug, thiserror::Error)]
enum RepositoryError {
    #[error("GitHub API error: {message}")]
    GitHubApi { message: String },
    #[error("AI provider error: {source}")]
    AIProvider { source: AIError },
    #[error("Database error: {source}")]
    Database { source: DatabaseError },
}

// Good: Result propagation with context
async fn sync_repositories(client: &GitHubClient) -> Result<usize> {
    let repositories = client
        .fetch_starred_repositories()
        .await
        .map_err(|e| RepositoryError::GitHubApi { message: e.to_string() })?;
    
    // Process repositories...
    Ok(repositories.len())
}
```

### Documentation Standards

#### Function Documentation
```rust
/// Analyzes a GitHub repository using the configured AI provider.
/// 
/// # Arguments
/// * `repository` - The repository to analyze
/// * `ai_client` - The AI provider client to use for analysis
/// 
/// # Returns
/// A `RepositoryAnalysis` containing generated summary, tags, and category.
/// 
/// # Errors
/// Returns `AnalysisError` if:
/// - Repository has insufficient data for analysis
/// - AI provider request fails
/// - Response parsing fails
async fn analyze_repository(
    repository: &Repository,
    ai_client: &dyn AIProvider,
) -> Result<RepositoryAnalysis> {
    // Implementation...
}
```

#### Module Documentation
```rust
//! GitHub API integration module.
//! 
//! This module provides a high-level interface for interacting with the GitHub API,
//! including authentication, repository fetching, and rate limit management.
//! 
//! # Examples
//! 
//! ```rust
//! let client = GitHubClient::new(token)?;
//! let repos = client.fetch_starred_repositories().await?;
//! ```

use octocrab::Octocrab;
```

### Testing Patterns

#### Test Structure
```rust
#[cfg(test)]
mod tests {
    use super::*;
    
    #[tokio::test]
    async fn test_analyze_repository_success() {
        // Arrange
        let repository = create_test_repository();
        let mock_ai_client = MockAIProvider::new()
            .expect_analyze()
            .returning(|_| Ok(create_test_analysis()));
        
        // Act
        let result = analyze_repository(&repository, &mock_ai_client).await;
        
        // Assert
        assert!(result.is_ok());
        let analysis = result.unwrap();
        assert_eq!(analysis.tags.len(), 3);
        assert!(!analysis.summary.is_empty());
    }
    
    #[tokio::test]
    async fn test_analyze_repository_insufficient_data() {
        // Arrange
        let repository = Repository {
            description: String::new(), // Empty description
            ..create_test_repository()
        };
        let mock_ai_client = MockAIProvider::new();
        
        // Act
        let result = analyze_repository(&repository, &mock_ai_client).await;
        
        // Assert
        assert!(matches!(result, Err(AnalysisError::InsufficientData)));
    }
}
```

#### Test Data Management
```rust
// Good: Centralized test data creation
fn create_test_repository() -> Repository {
    Repository {
        id: 12345,
        name: "test-repo".to_string(),
        description: "A test repository for unit testing".to_string(),
        language: Some("Rust".to_string()),
        stars: 42,
        // ... other fields
    }
}

// Good: Builder pattern for complex test data
impl RepositoryBuilder {
    fn new() -> Self { /* ... */ }
    fn with_name(mut self, name: &str) -> Self { /* ... */ }
    fn with_language(mut self, language: &str) -> Self { /* ... */ }
    fn build(self) -> Repository { /* ... */ }
}
```

### Configuration Management

#### Configuration Structure
```rust
// Good: Hierarchical configuration
#[derive(Debug, Deserialize)]
struct AppConfig {
    github: GitHubConfig,
    ai: AIConfig,
    ui: UIConfig,
    storage: StorageConfig,
}

#[derive(Debug, Deserialize)]
struct GitHubConfig {
    api_token: String,
    rate_limit_buffer: usize,
    cache_duration_hours: u64,
}

#[derive(Debug, Deserialize)]
struct AIConfig {
    provider: ProviderType,
    api_key: String,
    timeout_seconds: u64,
    max_retries: usize,
}
```

#### Configuration Loading
```rust
// Good: Multiple configuration sources
impl AppConfig {
    fn load() -> Result<Self> {
        let mut config = Config::builder()
            .add_source(File::with_name("config/default"))
            .add_source(File::with_name("config/local").required(false))
            .add_source(Environment::with_prefix("STARLOOM"))
            .build()?;
        
        config.try_deserialize()
    }
}
```

### Performance Guidelines

#### Async Patterns
```rust
// Good: Concurrent operations where possible
async fn sync_all_repositories(
    repositories: Vec<Repository>,
    ai_client: &dyn AIProvider,
) -> Result<Vec<RepositoryAnalysis>> {
    let analysis_futures = repositories
        .iter()
        .map(|repo| analyze_repository(repo, ai_client));
    
    // Process in batches to avoid overwhelming the AI provider
    let results = stream::iter(analysis_futures)
        .buffer_unordered(5) // Max 5 concurrent requests
        .collect::<Vec<_>>()
        .await;
    
    results.into_iter().collect()
}
```

#### Caching Patterns
```rust
// Good: Smart caching with invalidation
#[derive(Clone)]
struct CachedRepository {
    repository: Repository,
    analysis: Option<RepositoryAnalysis>,
    last_updated: SystemTime,
}

impl CachedRepository {
    fn is_stale(&self, max_age: Duration) -> bool {
        self.last_updated.elapsed().unwrap_or(Duration::MAX) > max_age
    }
}
```

### Security Patterns

#### Credential Management
```rust
// Good: Secure credential storage
use keyring::Entry;

struct CredentialManager;

impl CredentialManager {
    fn store_github_token(token: &str) -> Result<()> {
        let entry = Entry::new("starloom", "github_token")?;
        entry.set_password(token)?;
        Ok(())
    }
    
    fn get_github_token() -> Result<String> {
        let entry = Entry::new("starloom", "github_token")?;
        entry.get_password().map_err(|e| e.into())
    }
}
```

## UI/UX Guidelines

### Interface Patterns
- **Consistent Navigation**: Standard menu structure across platforms
- **Keyboard Shortcuts**: Full keyboard navigation support
- **Progress Indicators**: Clear feedback for long-running operations
- **Error States**: Helpful error messages with recovery suggestions

### Accessibility
- **Screen Reader Support**: Proper ARIA labels and semantic HTML
- **High Contrast**: Support for high contrast themes
- **Keyboard Navigation**: Tab order and focus management
- **Font Scaling**: Respect system font size preferences

## Version Control

### Commit Message Format
```
type(scope): description

Longer explanation if necessary

Fixes #123
```

**Types**: feat, fix, docs, style, refactor, test, chore
**Scopes**: github, ai, ui, data, config

### Branch Naming
- **Feature branches**: `feature/github-oauth-integration`
- **Bug fixes**: `fix/cache-invalidation-bug`
- **Releases**: `release/v1.0.0`

## Notes

This style guide reflects the project's emphasis on quality, maintainability, and developer productivity. The patterns established here should be consistently applied throughout the codebase to ensure long-term maintainability and team collaboration effectiveness.

The focus on comprehensive testing and clear error handling aligns with the project's goal of creating reliable, professional-grade developer tooling.
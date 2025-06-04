# Developer Onboarding Guide

## Welcome to ICN Development

This guide will help you get started developing with and contributing to the InterCooperative Network (ICN). Whether you're building applications on ICN, contributing to the core protocol, or developing integrations, this guide provides the foundation you need.

## Prerequisites

### System Requirements

#### Operating System
- **Linux**: Ubuntu 20.04+ or similar distribution (recommended)
- **macOS**: 10.15+ with Xcode command line tools
- **Windows**: Windows 10+ with WSL2 (Linux development environment recommended)

#### Hardware
- **CPU**: 4+ cores recommended for compilation
- **RAM**: 8GB minimum, 16GB+ recommended
- **Storage**: 20GB+ free space for development environment
- **Network**: Stable internet connection for dependency downloads

### Development Tools

#### Required Tools
```bash
# Rust toolchain (nightly)
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
rustup toolchain install nightly
rustup default nightly

# Git for version control
sudo apt-get install git  # Ubuntu/Debian
brew install git          # macOS

# Build essentials
sudo apt-get install build-essential pkg-config libssl-dev  # Ubuntu/Debian
xcode-select --install                                       # macOS
```

#### Recommended Tools
```bash
# Just for task running
cargo install just

# Development utilities
cargo install cargo-watch cargo-expand cargo-audit
cargo install --locked cargo-deny
cargo install cargo-machete  # Remove unused dependencies

# Code formatting and linting
rustup component add rustfmt clippy

# Documentation tools
cargo install mdbook        # For local docs
cargo install cargo-doc     # Enhanced documentation
```

### Development Environment Setup

#### Clone the Repository
```bash
# Clone the main ICN repository
git clone https://github.com/InterCooperative-Network/icn-core.git
cd icn-core

# Verify Rust toolchain
rustup show
```

#### Install Dependencies
```bash
# Install all Rust dependencies
cargo build

# Install pre-commit hooks (optional but recommended)
pip install pre-commit
pre-commit install
```

#### Verify Installation
```bash
# Run tests to verify everything works
just test

# Check code formatting
just format-check

# Run linting
just lint

# Build documentation
just docs
```

## Project Structure

### Repository Layout
```
icn-core/
├── crates/                 # Core library crates
│   ├── icn-common/        # Shared types and utilities
│   ├── icn-identity/      # DID and identity management
│   ├── icn-dag/          # Content-addressed storage
│   ├── icn-mesh/         # Distributed computing
│   ├── icn-runtime/      # WASM execution environment
│   ├── icn-economics/    # Mana and token systems
│   ├── icn-governance/   # Proposal and voting
│   ├── icn-network/      # P2P networking
│   ├── icn-api/          # External API definitions
│   ├── icn-node/         # Main node binary
│   └── icn-cli/          # Command-line interface
├── icn-ccl/              # Cooperative Contract Language
├── tests/                # Integration tests
├── docs/                 # Internal documentation
├── scripts/              # Development scripts
├── .github/              # CI/CD workflows
├── Cargo.toml           # Workspace configuration
├── justfile             # Task runner configuration
└── README.md
```

### Key Configuration Files
- **`rust-toolchain.toml`**: Rust version pinning
- **`Cargo.toml`**: Workspace and dependency configuration
- **`justfile`**: Development task definitions
- **`.pre-commit-config.yaml`**: Code quality hooks
- **`.github/workflows/`**: CI/CD pipeline definitions

## Development Workflow

### Daily Development

#### Using Just Commands
```bash
# Build all crates
just build

# Run all tests
just test

# Run specific crate tests
just test-crate icn-mesh

# Format code
just format

# Run linting
just lint

# Generate documentation
just docs

# Clean build artifacts
just clean

# Run local development node
just run-node

# Run CLI with examples
just run-cli --help
```

#### Manual Cargo Commands
```bash
# Build specific crate
cargo build -p icn-mesh

# Run tests with output
cargo test --all -- --nocapture

# Run tests for specific functionality
cargo test --all mesh_job

# Check without building
cargo check --all

# Update dependencies
cargo update

# Audit dependencies for security issues
cargo audit
```

### Code Quality Standards

#### Formatting
All code must be formatted with `rustfmt`:
```bash
# Format all code
cargo fmt --all

# Check formatting without changing files
cargo fmt --all -- --check
```

#### Linting
All code must pass `clippy` without warnings:
```bash
# Run clippy on all targets
cargo clippy --all-targets --all-features -- -D warnings

# Fix clippy suggestions automatically
cargo clippy --fix --all-targets --all-features
```

#### Testing Requirements
- **Unit Tests**: Every public function and struct
- **Integration Tests**: Cross-crate interactions
- **Property Tests**: Where applicable using proptest
- **Documentation Tests**: All public API examples

```rust
// Example unit test
#[cfg(test)]
mod tests {
    use super::*;

    #[tokio::test]
    async fn test_mesh_job_submission() {
        let mesh_service = MeshService::new();
        let job = create_test_job();
        
        let result = mesh_service.submit_job(&job).await;
        assert!(result.is_ok());
    }
}
```

## Building Your First ICN Application

### Simple CLI Tool Example

Create a new crate that uses ICN APIs:

```bash
# Create new binary crate
cargo new --bin my-icn-tool
cd my-icn-tool

# Add ICN dependencies to Cargo.toml
[dependencies]
icn-api = { path = "../icn-core/crates/icn-api" }
icn-common = { path = "../icn-core/crates/icn-common" }
tokio = { version = "1.0", features = ["full"] }
```

```rust
// src/main.rs
use icn_api::NodeApi;
use icn_common::{Did, CommonError};

#[tokio::main]
async fn main() -> Result<(), CommonError> {
    // Connect to local ICN node
    let node_api = NodeApi::new("http://localhost:3000").await?;
    
    // Get node information
    let info = node_api.get_node_info().await?;
    println!("Connected to ICN node: {}", info.node_id);
    
    // Submit a simple mesh job
    let job = create_example_job();
    let job_id = node_api.submit_mesh_job(&job).await?;
    println!("Submitted job: {}", job_id);
    
    Ok(())
}

fn create_example_job() -> MeshJob {
    // Implementation details
}
```

### Node Service Integration

Extend the node with custom services:

```rust
// Custom service implementation
use icn_runtime::{HostABI, RuntimeContext};
use icn_common::{Did, CommonError};

pub struct MyCustomService {
    context: RuntimeContext,
}

impl MyCustomService {
    pub fn new(context: RuntimeContext) -> Self {
        Self { context }
    }
    
    pub async fn process_custom_request(
        &self,
        requester: &Did,
        request: &CustomRequest
    ) -> Result<CustomResponse, CommonError> {
        // Verify requester has sufficient mana
        self.context
            .charge_mana(requester, 10)
            .await?;
        
        // Process request
        let response = self.handle_request(request).await?;
        
        // Create execution receipt
        let receipt = self.context
            .create_receipt(requester, &response)
            .await?;
        
        Ok(response)
    }
}
```

## Contributing to ICN Core

### Development Process

#### 1. Issue Creation
Before starting work:
- Check existing issues and discussions
- Create an issue describing the problem or feature
- Get feedback from maintainers before implementation

#### 2. Branch Strategy
```bash
# Create feature branch from main
git checkout main
git pull origin main
git checkout -b feature/your-feature-name

# Or for bug fixes
git checkout -b fix/bug-description
```

#### 3. Implementation Guidelines

**Code Style**:
- Follow Rust naming conventions
- Use descriptive variable and function names
- Add comprehensive documentation
- Include error handling for all operations

**Documentation**:
```rust
/// Submits a mesh job to the distributed compute network.
/// 
/// This function validates the job specification, checks the submitter's
/// mana balance, and queues the job for execution by available nodes.
/// 
/// # Arguments
/// 
/// * `submitter` - DID of the entity submitting the job
/// * `job` - The mesh job specification to execute
/// 
/// # Returns
/// 
/// Returns `Ok(JobId)` if successful, or `Err(CommonError)` if:
/// - Insufficient mana balance
/// - Invalid job specification  
/// - Network connectivity issues
/// 
/// # Examples
/// 
/// ```rust
/// use icn_mesh::{MeshService, MeshJob};
/// use icn_common::Did;
/// 
/// # async fn example() -> Result<(), Box<dyn std::error::Error>> {
/// let mesh_service = MeshService::new();
/// let submitter = Did::from_str("did:key:example")?;
/// let job = MeshJob::new("compute_pi", vec![]);
/// 
/// let job_id = mesh_service.submit_job(&submitter, &job).await?;
/// println!("Job submitted: {}", job_id);
/// # Ok(())
/// # }
/// ```
pub async fn submit_job(
    &self,
    submitter: &Did,
    job: &MeshJob
) -> Result<JobId, CommonError> {
    // Implementation
}
```

#### 4. Testing Requirements
All contributions must include tests:

```rust
#[cfg(test)]
mod tests {
    use super::*;
    use icn_common::test_utils::*;

    #[tokio::test]
    async fn test_job_submission_success() {
        let (mesh_service, _context) = create_test_mesh_service().await;
        let submitter = create_test_did();
        let job = create_test_job();
        
        let result = mesh_service.submit_job(&submitter, &job).await;
        
        assert!(result.is_ok());
        let job_id = result.unwrap();
        assert!(!job_id.to_string().is_empty());
    }
    
    #[tokio::test]
    async fn test_job_submission_insufficient_mana() {
        let (mesh_service, mut context) = create_test_mesh_service().await;
        let submitter = create_test_did();
        let job = create_expensive_test_job();
        
        // Set mana balance to insufficient amount
        context.set_mana_balance(&submitter, 5).await;
        
        let result = mesh_service.submit_job(&submitter, &job).await;
        
        assert!(result.is_err());
        match result.unwrap_err() {
            CommonError::InsufficientMana { .. } => (),
            _ => panic!("Expected InsufficientMana error"),
        }
    }
}
```

#### 5. Pull Request Process
```bash
# Ensure all tests pass
just test

# Ensure code is formatted
just format

# Ensure no lint warnings
just lint

# Update documentation if needed
just docs

# Commit changes
git add .
git commit -m "feat: add mesh job validation

- Add input validation for mesh job specifications
- Implement mana cost calculation for different job types
- Add comprehensive error handling and user feedback

Closes #123"

# Push branch
git push origin feature/your-feature-name
```

### Pull Request Guidelines

#### PR Title Format
Use conventional commits format:
- `feat:` for new features
- `fix:` for bug fixes
- `docs:` for documentation changes
- `test:` for test additions/changes
- `refactor:` for code refactoring
- `chore:` for maintenance tasks

#### PR Description Template
```markdown
## Description
Brief description of changes and motivation.

## Type of Change
- [ ] Bug fix (non-breaking change fixing an issue)
- [ ] New feature (non-breaking change adding functionality)
- [ ] Breaking change (fix or feature causing existing functionality to change)
- [ ] Documentation update

## Testing
- [ ] Unit tests pass
- [ ] Integration tests pass
- [ ] Manual testing completed
- [ ] Performance impact assessed

## Checklist
- [ ] Code follows project style guidelines
- [ ] Self-review of code completed
- [ ] Documentation updated where necessary
- [ ] Tests added/updated for changes
- [ ] All CI checks pass

## Related Issues
Closes #issue_number
```

## Debugging and Troubleshooting

### Common Development Issues

#### Build Failures
```bash
# Clean and rebuild
cargo clean
cargo build

# Update toolchain
rustup update nightly

# Check for dependency conflicts
cargo tree
```

#### Test Failures
```bash
# Run tests with detailed output
cargo test -- --nocapture

# Run specific test
cargo test test_name -- --exact

# Run tests in single thread for debugging
cargo test -- --test-threads=1
```

#### Runtime Issues
```bash
# Enable debug logging
RUST_LOG=debug cargo run

# Run with backtrace
RUST_BACKTRACE=1 cargo run

# Profile memory usage
valgrind --tool=massif target/debug/icn-node
```

### Development Tools

#### Useful Cargo Commands
```bash
# Expand macros for debugging
cargo expand

# Check unused dependencies
cargo machete

# Security audit
cargo audit

# Dependency licenses check
cargo deny check licenses

# Benchmark performance
cargo bench
```

#### IDE Setup

**VS Code Extensions**:
- rust-analyzer
- CodeLLDB (debugging)
- Better TOML
- GitLens

**IntelliJ/CLion**:
- Rust plugin
- TOML plugin

## Resources and Support

### Documentation
- **API Documentation**: Run `just docs` and open `target/doc/index.html`
- **Architecture Guide**: `/docs/architecture/`
- **RFCs**: `/docs/rfcs/` for design decisions

### Community
- **GitHub Discussions**: For general questions and ideas
- **GitHub Issues**: For bug reports and feature requests
- **Development Chat**: [Platform TBD]

### Learning Resources
- **Rust Book**: https://doc.rust-lang.org/book/
- **Async Rust**: https://rust-lang.github.io/async-book/
- **WebAssembly with Rust**: https://rustwasm.github.io/book/

### Best Practices References
- **Error Handling**: Use `CommonError` enum for consistent error types
- **Async Programming**: Use `tokio` for async runtime
- **Testing**: Use `tokio::test` for async tests
- **Documentation**: Follow rustdoc conventions

## Next Steps

1. **Set up your development environment** following the prerequisites
2. **Run the test suite** to verify everything works
3. **Explore the codebase** starting with `icn-common` and `icn-api`
4. **Read the architecture documentation** to understand system design
5. **Start with small contributions** like documentation improvements or minor bug fixes
6. **Join the community** discussions to get oriented

Welcome to the ICN development community! We're excited to have you contribute to building a cooperative digital commons. 
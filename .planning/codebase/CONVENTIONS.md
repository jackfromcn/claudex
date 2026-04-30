# Coding Conventions

**Analysis Date:** 2026-04-30

## Naming Patterns

**Files:**
- Snake_case module files (`handler.rs`, `proxy/mod.rs`)
- Submodules in directories (`proxy/translate/`)

**Functions:**
- snake_case (`start_proxy`, `handle_messages`, `resolve_auto_profile`)
- Private helpers prefixed with `try_` or descriptive verb (`try_forward`, `extract_and_store_context`)

**Variables:**
- snake_case (`profile_name`, `body_value`, `http_client`)
- Booleans often prefixed with `is_` (`is_streaming`, `is_run_command`)

**Types:**
- PascalCase structs and enums (`ClaudexConfig`, `ProfileConfig`, `AuthType`)
- Enum variants PascalCase (`ApiKey`, `OAuth`)

## Code Style

**Formatting:**
- `cargo fmt` for consistent formatting
- Max line length ~100 chars

**Linting:**
- `cargo clippy` for lint checks
- `#![allow(dead_code)]` at top of `main.rs`

## Import Organization

**Order:**
1. External crates (`use std::...`, `use anyhow::...`)
2. Internal modules (`use crate::...`)
3. Local imports (`use super::...`)

**Example from `main.rs`:**
```rust
use anyhow::Result;
use clap::Parser;
use tracing_subscriber::layer::SubscriberExt;

use cli::{AuthAction, Cli, Commands, ProfileAction, ProxyAction, SetsAction};
use config::ClaudexConfig;
```

## Error Handling

**Patterns:**
- `anyhow::Result<T>` return type for fallible functions
- `?` operator for error propagation
- `.context()` for adding context to errors
- `anyhow::bail!` for early return with error

**Example:**
```rust
pub async fn start_proxy(config: ClaudexConfig, port_override: Option<u16>) -> Result<()> {
    // ...
    anyhow::bail!("proxy failed to start within 2 seconds")
}
```

## Logging

**Framework:** `tracing` crate

**Patterns:**
- `tracing::info!` for normal operations
- `tracing::warn!` for recoverable issues
- `tracing::error!` for failures
- `tracing::debug!` for detailed debugging
- Structured logging with key=value pairs

**Example:**
```rust
tracing::info!(
    profile = %profile_name,
    authorization = %auth_header,
    body_len = %body.len(),
    "incoming request"
);
```

## Comments

**When to Comment:**
- Complex logic explanation
- Public API documentation
- Todo/Fixme markers for future work

**Doc Comments:**
- `///` for rustdoc on public items
- Brief descriptions of purpose

## Function Design

**Size:** Functions range from 10-100 lines, larger handlers broken into helpers

**Parameters:** Structs preferred over many parameters

**Return Values:**
- `Result<T>` for fallible operations
- `Option<T>` for optional values
- Direct return for infallible

## Module Design

**Exports:**
- Public items exported via `pub`
- Internal items private by default
- Re-export via `pub mod`

**Module Structure:**
- `mod.rs` for module root
- Submodules as directories with their own files

---

*Convention analysis: 2026-04-30*
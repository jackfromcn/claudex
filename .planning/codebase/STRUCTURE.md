# Codebase Structure

**Analysis Date:** 2026-04-30

## Directory Layout

```
claudex/
├── src/              # Core Rust source code
│   ├── main.rs       # CLI entry point
│   ├── cli.rs        # clap command definitions
│   ├── config/       # Configuration management
│   ├── oauth/        # OAuth authentication
│   ├── proxy/        # Translation proxy server
│   ├── router/       # Smart routing/classification
│   ├── context/      # Context engine (RAG, compression)
│   ├── tui/          # Terminal UI dashboard
│   ├── process/      # Process management
│   ├── sets/         # Config set management
│   └── terminal/     # Terminal utilities
├── tests/            # Integration tests
├── .github/          # GitHub Actions workflows
├── schemas/          # JSON schemas for validation
├── website/          # Documentation site
├── Cargo.toml        # Rust package manifest
├── config.example.*  # Example configuration files
└── CLAUDE.md         # Project guide for Claude Code
```

## Directory Purposes

**`src/`:**
- Purpose: All Rust source code
- Contains: `.rs` files organized by module
- Key files: `main.rs` (entry), `cli.rs` (commands)

**`src/config/`:**
- Purpose: Configuration loading, validation, profile management
- Contains: `mod.rs` (types), `cmd.rs` (CLI), `profile.rs` (profile ops)

**`src/oauth/`:**
- Purpose: OAuth flows, token management, provider implementations
- Contains: `mod.rs` (types), `providers.rs` (per-provider), `manager.rs` (token cache)

**`src/proxy/`:**
- Purpose: Translation proxy server
- Contains: `mod.rs` (server), `handler.rs` (request handling), `translate/` (protocols)
- Key subdirs: `translate/`, `adapter/`

**`src/proxy/translate/`:**
- Purpose: Protocol translation implementations
- Contains: `chat_completions.rs`, `responses.rs`, streaming variants

**`src/proxy/adapter/`:**
- Purpose: Provider-specific adapters
- Contains: Trait implementations for Anthropic, OpenAI, etc.

**`src/context/`:**
- Purpose: Context engine features
- Contains: `rag.rs` (RAG index), `compression.rs`, `sharing.rs`

**`src/tui/`:**
- Purpose: Terminal UI dashboard
- Contains: `mod.rs`, `dashboard.rs`, `widgets.rs`, `input.rs`

## Key File Locations

**Entry Points:**
- `src/main.rs`: CLI entry with `#[tokio::main]`
- `src/proxy/mod.rs:start_proxy()`: Proxy server startup

**Configuration:**
- `src/config/mod.rs`: ClaudexConfig, ProfileConfig types
- `config.example.toml`: Example TOML config
- `config.example.yaml`: Example YAML config

**Core Logic:**
- `src/proxy/handler.rs`: Request handling, translation, forwarding
- `src/oauth/providers.rs`: Per-provider OAuth implementations
- `src/proxy/adapter/mod.rs`: Provider adapter trait

**Testing:**
- `tests/proxy_integration.rs`: Integration test

## Naming Conventions

**Files:**
- Modules: `snake_case.rs` (e.g., `handler.rs`)
- Submodule directories: `snake_case/` (e.g., `proxy/translate/`)

**Structs/Enums:**
- PascalCase (e.g., `ClaudexConfig`, `ProfileConfig`, `ProviderType`)

**Functions:**
- snake_case (e.g., `start_proxy`, `handle_messages`)

## Where to Add New Code

**New Provider:**
- Adapter: `src/proxy/adapter/`
- OAuth: `src/oauth/providers.rs` (if OAuth needed)
- Config: Add to `ProviderType` enum in `src/config/mod.rs`

**New CLI Command:**
- Definition: `src/cli.rs` (add to `Commands` enum)
- Handler: `src/main.rs` (match branch)
- Module: Create new module or extend existing

**New Feature:**
- Core logic: Appropriate module directory
- Tests: `tests/` or inline `#[cfg(test)]`

**Utilities:**
- Shared helpers: `src/util.rs`

## Special Directories

**`.planning/`:**
- Purpose: GSD project planning artifacts
- Generated: Yes (by GSD workflow)
- Committed: Optional (config.json setting)

---

*Structure analysis: 2026-04-30*
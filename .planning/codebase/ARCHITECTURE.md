<!-- refreshed: 2026-04-30 -->
# Architecture

**Analysis Date:** 2026-04-30

## System Overview

```text
┌─────────────────────────────────────────────────────────────┐
│                      CLI Entry Point                       │
│                     `src/main.rs`                         │
├───────────────┬───────────────┬───────────────┬───────────┤
│   Profile     │   Proxy       │   OAuth       │   TUI      │
│   Management  │   Server      │   Auth        │   Dashboard│
│ `config/`     │ `proxy/`     │ `oauth/`     │ `tui/`    │
└───────────────┴───────────────┴──��────────────┴───────────┘
         │                  │                     │
         ▼                  ▼                     ▼
┌─────────────────────────────────────────────────────────────┐
│                    Configuration Layer                    │
│                  `src/config/mod.rs`                     │
└─────────────────────────────────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────────────────────────────────┐
│  Translation Layer (Anthropic ↔ OpenAI)                  │
│  `src/proxy/translate/`                                    │
└─────────────────────────────────────────────────────────────┘
```

## Component Responsibilities

| Component | Responsibility | File |
|-----------|----------------|------|
| CLI | Parse commands, dispatch to handlers | `src/cli.rs`, `src/main.rs` |
| Config | Load/validate profiles, manage settings | `src/config/mod.rs` |
| Proxy | HTTP server, request forwarding, translation | `src/proxy/mod.rs`, `src/proxy/handler.rs` |
| OAuth | Token management, provider login/refresh | `src/oauth/mod.rs`, `src/oauth/providers.rs` |
| Router | Intent classification, auto-profile selection | `src/router/mod.rs`, `src/router/classifier.rs` |
| Context | RAG index, conversation compression, sharing | `src/context/mod.rs` |
| TUI | Terminal dashboard for metrics/health | `src/tui/mod.rs` |
| Launch | Spawn Claude Code processes | `src/process/launch.rs` |
| Daemon | PID file management | `src/process/daemon.rs` |
| Sets | Claude Code config set management | `src/sets/mod.rs` |

## Pattern Overview

**Overall:** Layered architecture with clear separation

**Key Characteristics:**
- Async-first (Tokio runtime)
- Config-driven profile selection
- Provider-agnostic translation layer
- OAuth token lazy refresh

## Layers

**CLI Layer:**
- Purpose: User-facing command interface
- Location: `src/cli.rs`, `src/main.rs`
- Contains: Command parsing, subcommand dispatch
- Depends on: All other modules

**Proxy Layer:**
- Purpose: HTTP server handling translation proxy
- Location: `src/proxy/mod.rs`, `src/proxy/handler.rs`
- Contains: Axum router, request/response translation
- Depends on: Config, OAuth, Router, Context, Translate

**Translation Layer:**
- Purpose: Protocol conversion (Anthropic ↔ OpenAI)
- Location: `src/proxy/translate/`
- Contains: Request/response adapters per provider type
- Depends on: serde_json

**Auth Layer:**
- Purpose: OAuth flow and token management
- Location: `src/oauth/`
- Contains: Login flows, token refresh, keyring storage
- Depends on: keyring, HTTP client

## Data Flow

### Primary Request Path

1. **Entry** - `src/proxy/handler.rs:handle_message()` receives Claude Code request
2. **Profile Resolution** - Resolve profile name (explicit or "auto" via router)
3. **OAuth Refresh** - Lazy token refresh if OAuth profile
4. **Context Engine** - Apply pre-processing (RAG, compression)
5. **Translation** - Convert Anthropic → provider format (`src/proxy/adapter/`)
6. **Forward** - Send to upstream provider
7. **Response Translation** - Convert provider → Anthropic format
8. **Return** - Response to Claude Code

### OAuth Flow

1. User runs `claudex auth login <provider>`
2. OAuth server spawns callback handler (`src/oauth/server.rs`)
3. Device code or browser flow initiates
4. Token exchange stores in keyring (`src/oauth/exchange.rs`)
5. Proxy lazy-loads token on request (`src/oauth/manager.rs`)

## Key Abstractions

**ProfileConfig:**
- Purpose: Unified configuration for LLM providers
- Examples: `src/config/mod.rs:ProfileConfig`
- Pattern: Struct with provider-specific fields variants

**ProviderAdapter:**
- Purpose: Protocol translation per provider type
- Examples: `src/proxy/adapter/mod.rs`
- Pattern: Trait object for dynamic dispatch

**TokenManager:**
- Purpose: OAuth token lazy loading and refresh
- Examples: `src/oauth/manager.rs`
- Pattern: Async cache with expiration handling

## Entry Point

**CLI Entry:**
- Location: `src/main.rs`
- Trigger: `claudex <command>` CLI invocation
- Responsibilities: Parse args, load config, dispatch to module

**Proxy Entry:**
- Location: `src/proxy/mod.rs:start_proxy()`
- Trigger: `claudex proxy start` or `claudex run`
- Responsibilities: Bind port, serve Axum router

## Architectural Constraints

- **Threading:** Single-threaded async (Tokio), blocking ops in spawned tasks
- **Global state:** Config in `Arc<RwLock<>>`, metrics/health in `Arc`
- **Circular import:** None detected

## Error Handling

**Strategy:** Result propagation with anyhow

**Patterns:**
- `anyhow::Result<T>` for fallible operations
- `thiserror` for custom error types
- Context attached via `.context()` for debugging

## Cross-Cutting Concerns

**Logging:** `tracing` with file + stderr layers
**Validation:** Config validation at load time
**Authentication:** OAuth tokens in keyring, API keys in keyring or config

---

*Architecture analysis: 2026-04-30*
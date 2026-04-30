# Technology Stack

**Analysis Date:** 2026-04-30

## Languages

**Primary:**
- Rust 2021 edition - Core language for all functionality

## Runtime

**Environment:**
- Tokio async runtime v1 - Async I/O for proxy server
- Standard library

**Build:**
- Cargo (Rust package manager)
- Cargo.lock present

## Frameworks

**Core:**
- Axum 0.8 - Web framework for proxy server
- Tower 0.5 - HTTP middleware
- Tower-HTTP 0.6 - HTTP utilities (CORS, tracing)

**HTTP Client:**
- reqwest 0.13 (with rustls-tls) - HTTP client for upstream requests forwarding

**CLI:**
- clap 4 (derive) - Command-line argument parsing

**TUI:**
- ratatui 0.30 - Terminal UI framework
- crossterm 0.29 - Terminal manipulation

**Configuration:**
- toml - Configuration file format
- figment 0.10 - Configuration management (TOML, YAML, env)
- serde - Serialization/deserialization

**Error Handling:**
- anyhow 1 - Error propagation
- thiserror 2 - Custom error types

## Key Dependency

**Critical:**
- tokio 1 (full features) - Async runtime foundation
- axum 0.8 - Web server
- reqwest 0.13 - HTTP client

**OAuth/Auth:**
- keyring 3 - Secure credential storage (macOS Keychain, Linux Secret Service)
- sha2 0.10 - SHA for OAuth PKCE
- base64 0.22 - Token encoding
- rand 0.10 - Random for PKCE

**Observability:**
- tracing 0.1 - Structured logging
- tracing-subscriber 0.3 - Log subscriber with env filter

**Other:**
- uuid 1 - Request ID generation
- chrono 0.4 - Time handling
- dirs 6 - Config directory paths
- notify 7 - File watching for hot reload
- self_update 0.42 - Auto-update
- regex 1 - URL detection

## Configuration

**Environment:**
- Config file: `~/.config/claudex/config.toml` or `./config.toml`
- Example: `config.example.toml`, `config.example.yaml`
- Override via `--config` CLI flag

**Build:**
- Cargo.toml with release profile: LTO, single codegen unit, strip

## Platform Requirements

**Development:**
- Rust 2021 edition
- Cargo build system

**Production:**
- Standalone binary
- Config file required

---

*Stack analysis: 2026-04-30*
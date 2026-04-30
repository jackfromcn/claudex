# Codebase Concerns

**Analysis Date:** 2026-04-30

## Tech Debt

**Configuration Module Complexity:**
- Issue: `src/config/mod.rs` is ~38KB, handles many responsibilities
- Files: `src/config/mod.rs`
- Impact: Difficult to navigate, understand all config options
- Fix approach: Split into smaller modules (types, loading, validation)

**Translation Module:**
- Issue: Multiple translation files with overlapping logic
- Files: `src/proxy/translate/*.rs`
- Impact: Potential duplication, hard to track which file handles what
- Fix approach: Consolidate documentation, clarify responsibilities

## Known Bugs

**None explicitly tracked:**
- No TODO/FIXME comments indicating known bugs
- Tests passing per recent commits

## Security Considerations

**API Key Storage:**
- Risk: API keys can be stored in plaintext config files
- Files: `config.example.toml` shows inline `api_key`
- Current mitigation: Keyring option available (`api_key_keyring`)
- Recommendations: Encourage keyring usage, warn on plaintext

**OAuth Token Handling:**
- Risk: Tokens stored in keyring, need secure handling
- Files: `src/oauth/token.rs`, `src/oauth/manager.rs`
- Current mitigation: Keyring abstraction, no logging of tokens
- Recommendations: Continue current practices

**Logging:**
- Risk: Request bodies logged (truncated at 2000 bytes for debug)
- Files: `src/proxy/handler.rs`
- Current mitigation: Truncation, debug level only
- Recommendations: Ensure no secrets in logged content

## Performance Bottlenecks

**RAG Index Build:**
- Problem: Index build on startup could delay proxy readiness
- Files: `src/context/rag.rs`
- Cause: External API calls to build index
- Improvement path: Async background build, lazy load on first request

**Circuit Breaker Lock:**
- Problem: Write lock held during entire forward operation (removed in recent fix)
- Files: `src/proxy/handler.rs:try_with_circuit_breaker`
- Cause: Original design held lock too long
- Improvement path: Current code releases lock before forward, re-acquires for update

## Fragile Areas

**OAuth Provider Implementations:**
- Files: `src/oauth/providers.rs` (~40KB)
- Why fragile: Per-provider logic, external API changes may break
- Safe modification: Add providers carefully, test thoroughly
- Test coverage: Integration tests needed for each provider

**Streaming Translation:**
- Files: `src/proxy/translate/*_stream.rs`
- Why fragile: Complex state machine for SSE events
- Safe modification: Understand existing patterns before changes
- Test coverage: Needs more streaming test coverage

## Scaling Limits

**Single Proxy Instance:**
- Current capacity: One proxy per port
- Limit: Cannot handle multiple ports simultaneously
- Scaling path: Multiple proxy instances with different configs

**In-Memory Metrics:**
- Current capacity: MetricsStore in memory
- Limit: Lost on restart, no persistence
- Scaling path: Optional persistence layer for metrics

## Dependencies at Risk

**Self-Update Mechanism:**
- Risk: `self_update` crate may have issues
- Impact: Auto-update feature could fail
- Migration plan: Manual update fallback exists

**Keyring Crate:**
- Risk: Platform-specific keyring implementations
- Impact: Auth storage may fail on some platforms
- Migration plan: Fallback to config file if keyring fails

## Missing Critical Features

**None identified:**
- Core functionality implemented (proxy, auth, profiles, TUI)
- All documented features have implementations

## Test Coverage Gaps

**OAuth Provider Tests:**
- What's not tested: Real OAuth flows per provider
- Files: `src/oauth/providers.rs`
- Risk: Provider API changes could break silently
- Priority: Medium (mocked tests exist for some)

**Streaming Translation Tests:**
- What's not tested: Full streaming request/response cycle
- Files: `src/proxy/translate/*_stream.rs`
- Risk: Edge cases in SSE parsing may fail
- Priority: High (core functionality)

**Router Classification:**
- What's not tested: Intent classification logic
- Files: `src/router/classifier.rs`
- Risk: Wrong profile selection
- Priority: Medium

---

*Concerns audit: 2026-04-30*
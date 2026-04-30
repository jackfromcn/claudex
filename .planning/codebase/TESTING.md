# Testing Patterns

**Analysis Date:** 2026-04-30

## Test Framework

**Runner:**
- Built-in Rust test framework (`cargo test`)
- Config: Inline `#[cfg(test)]` modules

**Assertion Library:**
- Standard `assert!`, `assert_eq!`, `assert_ne!`

**Run Commands:**
```bash
cargo test              # Run all tests
cargo test --lib        # Run library tests only
cargo test proxy        # Run tests matching "proxy"
cargo test -- --nocapture  # Show stdout
```

## Test File Organization

**Location:**
- Inline in source files: `#[cfg(test)] mod tests { ... }`
- Integration tests: `tests/proxy_integration.rs`

**Naming:**
- Test functions: `test_<description>` (e.g., `test_oauth_token_not_expired`)
- Test module: `tests`

**Structure:**
```
tests/
└── proxy_integration.rs

src/
├── proxy/handler.rs    # #[cfg(test)] mod tests { ... }
├── oauth/mod.rs        # #[cfg(test)] mod tests { ... }
└── ...                 # Inline tests throughout
```

## Test Structure

**Suite Organization:**
```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_truncate_ascii_within_limit() {
        assert_eq!(truncate_at_char_boundary("hello", 10), "hello");
    }

    #[test]
    fn test_truncate_utf8_boundary() {
        let s = "日本語";
        assert_eq!(truncate_at_char_boundary(s, 4), "日");
    }
}
```

**Patterns:**
- Unit tests inline with source
- Integration tests in `tests/` directory
- Async tests use `#[tokio::test]`

## Mocking

**Framework:** `wiremock` for HTTP mocking in integration tests

**Patterns:**
```rust
use wiremock::{MockServer, Mock, ResponseTemplate};
use wiremock::matchers::{method, path};

// Setup mock server
let mock_server = MockServer::start().await;
Mock::given(method("POST"))
    .respond_with(ResponseTemplate::new(200))
    .mount(&mock_server)
    .await;
```

**What to Mock:**
- HTTP responses from upstream providers
- OAuth token exchanges

**What NOT to Mock:**
- Internal logic functions
- Pure transformations (test directly)

## Fixtures and Factories

**Test Data:**
```rust
let resp = serde_json::json!({
    "content": [
        {"type": "text", "text": "Hello world"},
        {"type": "text", "text": " more text"}
    ]
});
```

**Location:**
- Inline in test functions
- No separate fixture files

## Coverage

**Requirements:** None enforced

**View Coverage:**
```bash
cargo tarpaulin --out Html  # If tarpaulin installed
```

## Test Types

**Unit Tests:**
- Inline `#[test]` functions
- Test pure functions, transformations

**Integration Tests:**
- `tests/proxy_integration.rs`
- Test full proxy request/response flow
- Use `wiremock` for mock upstream

**E2E Tests:**
- Not used currently

## Common Patterns

**Async Testing:**
```rust
#[tokio::test]
async fn test_async_operation() {
    let result = some_async_fn().await;
    assert!(result.is_ok());
}
```

**Error Testing:**
```rust
#[test]
fn test_error_case() {
    let result = fallible_fn();
    assert!(result.is_err());
}
```

---

*Testing analysis: 2026-04-30*
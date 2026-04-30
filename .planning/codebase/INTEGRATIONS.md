# External Integrations

**Analysis Date:** 2026-04-30

## APIs & External Service

**LLM Providers (via translation proxy):**
- Anthropic (api.anthropic.com) - Direct passthrough
- OpenAI (api.openai.com) - Protocol translation
- OpenRouter - OpenAI-compatible aggregation
- Grok (x.ai) - OpenAI-compatible
- DeepSeek - OpenAI-compatible
- Kimi (moonshot) - OpenAI-compatible
- GLM - OpenAI-compatible
- Ollama - Local LLM
- MiniMax - Direct Anthropic-compatible

**Authentication Providers (OAuth):**
- Claude subscription (claude.ai) - OAuth PKCE + Device Code
- ChatGPT/Codex (chatgpt.com) - OAuth PKCE + Device Code
- Google/Gemini - OAuth
- Qwen - OAuth
- Kimi - OAuth
- GitHub Copilot - OAuth PKCE
- GitLab - OAuth

## Data Storage

**Credentials:**
- macOS Keychain via `keyring` crate
- Linux Secret Service via `keyring` crate
- No persistent database

**File Storage:**
- Config files: TOML/YAML in `~/.config/claudex/`
- Cache: `~/.cache/claudex/` for proxy logs

**Caching:**
- In-memory only (MetricsStore, circuit breakers, shared context)

## Authentication & Identity

**Auth Approaches:**
- API Key (config file or keyring)
- OAuth subscription tokens (stored in keyring, auto-refreshed)

## Monitoring & Observability

**Logging:**
- `tracing` crate with file and stderr outputs
- Proxy logs: `~/.cache/claudex/proxy-{timestamp}-{pid}.log`
- Configurable log level

**Metrics:**
- In-memory MetricsStore (request count, latency, success/failure)
- TUI dashboard displays metrics

## CI/CD & Deployment

**Hosting:**
- Self-hosted (runs locally)

**CI Pipeline:**
- GitHub Actions for tests

## Environment Configuration

**Required config:**
- Profile configurations (base_url, api_key, provider_type)
- proxy_port, proxy_host settings

**Secrets location:**
- API keys: config file or OS keyring
- OAuth tokens: OS keyring

---

*Integration audit: 2026-04-30*
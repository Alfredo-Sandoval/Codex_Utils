# Codex_Utils

Lightweight utilities and a ready-to-use `config.toml` for Codex-style AI tooling. Copy it into your project or use it in-place to get sane defaults for models, providers, sandboxing, and approval flows.

## What’s Inside

- `config.toml`: Opinionated defaults for model, provider, reasoning, sandbox, and approvals.
- `LICENSE`: MIT License.

## Quick Start

1) Copy `config.toml` to your project root (or use it here).
2) Pick one `model` and one `model_provider` in the CHOOSE ONE sections.
3) Export the required API key(s) in your shell/environment:
   - `OPENAI_API_KEY` for `openai` or `openai-chat-completions`
   - `AZURE_OPENAI_API_KEY` for `azure`
4) Set workflow policies:
   - `approval_policy`: `on-request`, `untrusted`, `on-failure`, or `never`
   - `sandbox_mode`: `danger-full-access`, `workspace-write`, or `read-only`
5) (Optional) Uncomment provider blocks and profiles if your tool requires explicit definitions.

Tip: Start with the provided defaults, then tighten sandbox/approvals as you go.

## Configuration Reference

Key choices are annotated in the file with CHOOSE ONE comments. Common options:

- Model: `gpt-5`, `o3`, `o4-mini`
- Provider: `openai`, `openai-chat-completions`, `azure`, `ollama`
- Reasoning: `model_reasoning_effort` (`high`/`medium`/`low`/`minimal`),
  `model_reasoning_summary` (`auto`/`concise`/`detailed`/`none`),
  `model_verbosity` (`low`/`medium`/`high`)
- Approvals: `approval_policy` controls when actions need consent
- Sandbox: `sandbox_mode` controls filesystem and network access

Example snippet:

```toml
model = "gpt-5"
model_provider = "openai"
model_reasoning_effort = "high"
model_reasoning_summary = "auto"
model_verbosity = "medium"

approval_policy = "on-request"
sandbox_mode = "danger-full-access"
```

## Reference Templates

Below are two ready-to-copy examples: a full template including all supported keys from the upstream Codex project, and your current local config.

### Full config (all keys)

```toml
# Base model selection
model = "gpt-5"
model_provider = "openai"

# Reasoning and verbosity
model_reasoning_effort = "high"          # minimal | low | medium | high
model_reasoning_summary = "auto"         # none | auto | concise | detailed
model_verbosity = "medium"               # low | medium | high
model_context_window = 200000             # optional
model_max_output_tokens = 4096            # optional
model_supports_reasoning_summaries = true

# Auth preference
preferred_auth_method = "chatgpt"        # chatgpt (default) | apikey

# Approvals and sandbox
approval_policy = "on-request"           # untrusted | on-failure | on-request | never
sandbox_mode = "read-only"               # read-only | workspace-write | danger-full-access

[sandbox_workspace_write]
exclude_tmpdir_env_var = false
exclude_slash_tmp = false
writable_roots = ["/Users/YOU/.pyenv/shims"]  # optional
network_access = false

# Storage and history
disable_response_storage = false

[history]
persistence = "save-all"                 # save-all (default) | none

# Shell environment policy
[shell_environment_policy]
inherit = "core"                         # all | core | none
ignore_default_excludes = false
exclude = ["AWS_*", "AZURE_*"]
set = { }
include_only = []

# Notifications and file opener
notify = ["notify-send", "Codex"]        # optional program executed after each turn
file_opener = "vscode"                   # vscode | vscode-insiders | windsurf | cursor | none

# MCP servers
[mcp_servers.example]
command = "npx"
args = ["-y", "mcp-server"]
env = { API_KEY = "value" }

# Providers (extend or override built-ins)
[model_providers.openai]
name = "OpenAI"
base_url = "https://api.openai.com/v1"
env_key = "OPENAI_API_KEY"
# Optional per-provider network tuning
request_max_retries = 4
stream_max_retries = 10
stream_idle_timeout_ms = 300000

[model_providers.openai-chat-completions]
name = "OpenAI using Chat Completions"
base_url = "https://api.openai.com/v1"
env_key = "OPENAI_API_KEY"
wire_api = "chat"

[model_providers.azure]
name = "Azure"
base_url = "https://YOUR_PROJECT_NAME.openai.azure.com/openai"
env_key = "AZURE_OPENAI_API_KEY"
query_params = { api-version = "2025-04-01-preview" }

[model_providers.ollama]
name = "Ollama"
base_url = "http://localhost:11434/v1"

# Optional headers examples
[model_providers.example]
name = "Example"
base_url = "https://api.example.com/v1"
env_key = "EXAMPLE_API_KEY"
http_headers = { "X-Example-Header" = "example-value" }
env_http_headers = { "X-Example-Features" = "EXAMPLE_FEATURES" }

# Profiles and selection
profile = "default"

[profiles.default]
model = "gpt-5"
model_provider = "openai"
approval_policy = "on-request"
model_reasoning_effort = "high"
model_reasoning_summary = "auto"
model_verbosity = "medium"

# Agent output visibility
hide_agent_reasoning = false
show_raw_agent_reasoning = false

# Project docs size
project_doc_max_bytes = 32768

# TUI settings placeholder
[tui]
# (intentionally minimal; see upstream docs)
```

### Your current config (~/.codex/config.toml)

```toml
# =========================
# Codex CLI config.toml
# =========================
# Rule: In each "CHOOSE ONE" block, uncomment EXACTLY ONE line.


# -------------------------
# Model (string)
# -------------------------
# CHOOSE ONE:
model = "gpt-5"
# model = "o3"
# model = "o4-mini"

# -------------------------
# Reasoning effort (string)
# -------------------------

# CHOOSE ONE:
model_reasoning_effort = "high"
# model_reasoning_effort = "medium"
# model_reasoning_effort = "low"
# model_reasoning_effort = "minimal"


# -------------------------
# Approval policy (string)
# -------------------------
# CHOOSE ONE:
# approval_policy = "on-request"
# approval_policy = "untrusted"
# approval_policy = "on-failure"
approval_policy = "never"


# -------------------------
# Sandbox mode (string)
# -------------------------
# CHOOSE ONE:
sandbox_mode = "danger-full-access"   # no sandbox
# sandbox_mode = "workspace-write"     # writes allowed in cwd/tmp only
# sandbox_mode = "read-only"           # default in docs: no writes, no network

network_access = true
```

Note: `network_access` takes effect under `[sandbox_workspace_write]` in current Codex releases. If you want to allow network inside the sandbox, set it under that table.

### Providers and Env Vars

- OpenAI: set `OPENAI_API_KEY`
- OpenAI (Chat Completions wire): set `OPENAI_API_KEY`
- Azure OpenAI: set `AZURE_OPENAI_API_KEY` and configure endpoint in the provider block
- Ollama (local): typically no key; ensure the local service is running

If your tooling requires explicit provider definitions, uncomment the relevant block(s) in `config.toml` and adjust values (base URLs, API versions, etc.).

### Sandbox and Approvals

- `danger-full-access`: No sandboxing. Full filesystem/network access.
- `workspace-write`: Writes allowed in project and temp; optional network control.
- `read-only`: No writes and typically no network.
- `approval_policy`: Choose how strictly the tool asks before actions.

### Profiles (Optional)

Profiles let you predefine named presets. Uncomment and adapt as needed:

```toml
[profiles.default]
model = "gpt-5"
model_provider = "openai"
approval_policy = "on-request"
model_reasoning_effort = "high"
model_reasoning_summary = "auto"
model_verbosity = "medium"
```

## Troubleshooting

- Missing key error: confirm the correct env var is exported for the chosen provider.
- Permission denied: if writes fail, switch to `workspace-write` or `danger-full-access`.
- Azure requests failing: ensure the provider block is uncommented and the `api-version` and base URL match your deployment.

## Contributing

Small fixes and improvements are welcome. Keep issues/proposals concise and actionable.

## License

MIT © Alfredo Sandoval

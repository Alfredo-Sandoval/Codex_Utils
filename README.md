# New Keys Per Release

- rust-v0.25.0 (2025-08-27)
  - No new config keys compared to v0.24.0. This release focused on TUI polish, bug fixes, and minor exec/stream behavior.

- rust-v0.24.0 (2025-08)
  - preferred_auth_method = "apikey" | "chatgpt" — choose ChatGPT login or API key when both are available.
  - [projects."/abs/path"].trust_level = "trusted" — mark specific repos as trusted to reduce prompts.
  - model_verbosity = "low" | "medium" | "high" — controls text verbosity for GPT‑5 models.
  - [model_providers.<id>].request_max_retries — per‑provider HTTP retry cap.
  - [model_providers.<id>].stream_max_retries — per‑provider stream reconnect cap.
  - [model_providers.<id>].stream_idle_timeout_ms — per‑provider stream idle timeout.
  - show_raw_agent_reasoning = true — show raw reasoning content when available.
  - [shell_environment_policy].experimental_use_profile = true — run commands through a profile shell (advanced).
  - chatgpt_base_url = "https://chatgpt.com/backend-api/" — override ChatGPT backend URL (advanced).
  - responses_originator_header_internal_override = "codex_cli_rs" — internal/testing only.
  - [tools].web_search = true (alias: web_search_request) — enable native web search tool.

# Codex_Utils

## Quick Start

1. Copy `config.toml` to `~/.codex/config.toml`
2. Authenticate (choose one):
   - ChatGPT sign‑in: run `codex` and choose “Sign in with ChatGPT” (no API key needed).
   - API key: set `OPENAI_API_KEY` if you prefer usage‑based billing.
     - macOS/Linux: `export OPENAI_API_KEY="your-key"`
     - Windows (PowerShell): `$env:OPENAI_API_KEY="your-key"`
3. Run: `codex`

## Key Configuration Options

### Core Settings
```toml
model = "gpt-5"                    # or "o3", "o4-mini"
model_reasoning_effort = "high"    # minimal | low | medium | high
approval_policy = "never"          # untrusted | on-failure | on-request | never
sandbox_mode = "danger-full-access" # read-only | workspace-write | danger-full-access

# Network (applies when sandbox_mode = "workspace-write")
[sandbox_workspace_write]
network_access = false             # allow outbound network from sandbox

# UI/UX
file_opener = "vscode"             # Opens files in VS Code
show_raw_agent_reasoning = true    # Show raw reasoning content (if available)
notify = ["notify-send", "Codex"]  # Desktop notifications

# Tools
[tools]
web_search = true                  # Enable web search

# History
[history]
persistence = "save-all"           # Keep conversation history
```

### Security: Shell Environment Policy
```toml
[shell_environment_policy]
ignore_default_excludes = false   # Blocks KEY/SECRET/TOKEN vars
include_only = [
    # Core essentials
    "PATH", "HOME", "USER", "SHELL",
    "LANG", "LC_*", "TERM", "EDITOR",
    "PWD", "TMPDIR", "XDG_*",
    
    # Development (add as needed)
    "PYTHON*", "CONDA_*", "VIRTUAL_ENV*",  # Python
    "NODE_*", "NPM_*", "NVM_*",            # Node.js
]
# Never include: "*_KEY", "*_SECRET", "*_TOKEN", "AWS_*", "AZURE_*"
```

### Trust Settings
```toml
[projects."/path/to/your/repo"]
trust_level = "trusted"            # Skip security prompts for specific repos
```

## Providers

### OpenAI (Default)
- Set: `OPENAI_API_KEY`
- Optional: `OPENAI_ORGANIZATION`, `OPENAI_PROJECT`
```

### Ollama (Local)
```toml
[model_providers.ollama]
base_url = "http://localhost:11434/v1"
```

## Profiles (

Switch configurations with `codex --profile <name>`:

```toml
[profiles.quick]
model_reasoning_effort = "minimal"  # Fast mode

[profiles.secure]
approval_policy = "on-request"      # Careful mode
sandbox_mode = "workspace-write"    # Restricted
```

## Files in This Repo

- `config.toml` - Production-ready configuration with security defaults
- `codex-config-comprehensive.toml` - Full reference with all available options

## Tips

- Start with the provided `config.toml` and customize as needed
- Use `include_only` in shell_environment_policy for maximum security
- Enable `show_raw_agent_reasoning` to understand the assistant's decisions
- Set up project trust to reduce security prompts in your repos

## Resources

- [Official Codex Documentation](https://developers.openai.com/codex/cli)
- [Environment Variables Security](https://help.openai.com/en/articles/5112595-best-practices-for-api-key-safety)

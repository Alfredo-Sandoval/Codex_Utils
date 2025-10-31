# Codex Utils

Opinionated configuration files for OpenAI Codex CLI. All configurations are in plain TOML format, allowing you to copy what you need without disrupting your existing workflow.

---

## Quick Start

1. **Pick the platform template** from `configs/` and copy it to your user config:
   - **Linux** → `configs/config.linux.toml` → `~/.codex/config.toml`
   - **macOS** → `configs/config.macos.toml` → `~/.codex/config.toml`
   - **Windows** → `configs/config.windows.toml` → `%USERPROFILE%\.codex\config.toml`
2. **Authenticate**:
   - ChatGPT login: run `codex` and select "Sign in with ChatGPT".
   - API key: set `OPENAI_API_KEY` environment variable (`export` on Unix/macOS, `$env:` on Windows).
3. Launch Codex: `codex`

Each template provides minimal defaults: GPT‑5 Codex model, high reasoning effort, full-access sandbox, and modern feature toggles (`web_search_request`, `view_image_tool`). Shell environment whitelists are customized per OS.

---

## What’s Included

### `config.toml`
Baseline configuration that mirrors the CLI defaults with unnecessary options removed. Highlights:
- `model = "gpt-5"` with `model_reasoning_effort = "high"`
- Approval policy set to `never` for fully autonomous operation
- Shell environment policy restricted to language and runtime variables only
- Feature toggles use the legacy `[tools]` block for backwards compatibility (`web_search`, `view_image`)

### `configs/config.<platform>.toml`
Platform-specific configurations using the newer `[features]` table for enabling/disabling tools without modifying legacy keys. Update the `[projects."<abs path>"]` entry to match your workspace before copying to your Codex home directory.

### `codex-config-comprehensive.toml`
A line-by-line reference documenting every supported configuration option in Codex CLI 0.53.0. This file contains only the configuration keys with documentation, making it easy to find and copy settings into your own config.

---

## Sample Snippet (modern features)

```toml
model = "gpt-5-codex"
model_reasoning_effort = "high"
approval_policy = "never"
sandbox_mode = "danger-full-access"
network_access = true

[features]
web_search_request = true
view_image_tool = true

[shell_environment_policy]
ignore_default_excludes = false
include_only = [
    "PATH", "HOME", "USER", "SHELL",
    "LANG", "LC_*", "TERM", "EDITOR",
    "PWD", "TMPDIR",
    "PYTHON*", "PIP_*", "VIRTUAL_ENV*", "CONDA_*", "PYENV_*",
    "NODE_*", "NPM_*", "NVM_*", "PNPM_*", "YARN_*"
]

[projects."/absolute/path/to/repo"]
trust_level = "trusted"
```

Use this snippet as a starting point for building your own configuration. The comprehensive reference file documents additional optional sections (profiles, model providers, sandboxed writes, etc.).

---

## Tips & Reminders

- Maintain a strict shell environment whitelist and never include secrets (`*_KEY`, `*_TOKEN`, etc.).
- Use per-project trust only for repositories you control, as trusted mode bypasses approval prompts.
- For non-destructive workflows, change the sandbox mode to `workspace-write` to allow Codex to request elevated access on a case-by-case basis.
- After upgrading the CLI, compare the new `codex-config-comprehensive.toml` from the release with this repository to identify new configuration keys.

---

## Useful Links

- [Codex CLI documentation](https://developers.openai.com/codex/cli)
- [API key safety best practices](https://help.openai.com/en/articles/5112595-best-practices-for-api-key-safety)

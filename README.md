# AI API Key Scanner: Detect OpenAI, Anthropic & Google AI Keys in GitHub PRs

A GitHub Action that **checks pull request diffs for exposed AI API keys**.

AI API Key Scanner scans **pull request diffs** for exposed **OpenAI, Anthropic, and Google AI API keys**, adds inline GitHub annotations, comments on the PR with a clear summary, and **fails the workflow** when a matching key is detected. Merge blocking requires an enforced required check.

The scan runs on pull requests after changes have been committed. Treat an exposed key as compromised and rotate it even if the pull request has not merged.

This is a focused, low-noise guardrail designed specifically for teams using AI APIs.

---

## Why This Exists

AI API keys are frequently copied into scripts, tests, and examples during development.  
One accidental commit can lead to:

- unexpected API charges  
- emergency key rotation  
- security incidents  
- broken trust with customers or auditors  

Use this action as one layer of review for added lines in pull requests. It does not stop a key from being committed or detect every exposure.

---

## Features

- 🔍 Scans **PR diffs only** (added lines, not entire repositories)
- 🔐 Detects **OpenAI, Anthropic, and Google AI** API keys
- 📝 Adds **inline GitHub annotations** with file and line number
- 💬 Posts a **clear PR comment summary**
- ❌ **Fails the workflow** when matching keys are found; merge blocking requires an enforced required check
- ⚙️ Configurable ignore paths and allowlist regex patterns
- 🚀 Built with **TypeScript** and **Node.js 20**
- 🧠 Designed to be **low-noise and deterministic**

---

## Supported Key Types

- **OpenAI**  
  `sk-...` or `sk-proj-...`

- **Anthropic**  
  `sk-ant-...`

- **Google AI / Gemini / Vertex**  
  `AIza...`

> The scanner is intentionally opinionated and focused on AI providers.

---

## Usage

### Basic Setup

Add the following workflow to your repository:

```yaml
name: AI Key Scanner

on:
  pull_request:
    branches: [ main, master ]

jobs:
  scan:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: write
      issues: write
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Run AI API Key Scanner
        uses: AdamDiStefanoAI/AI-Key-Scanner@v1.0.0
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
```

This example runs on pull requests targeting main or master. To block merging when the check fails, configure it as required in the repository rules.

---

## Configuration (Optional)

Create a `.github/ai-key-scanner.yml` file to customize behavior:

```yaml
# Paths to ignore when scanning
# Uses simple substring matching (not glob patterns)
ignorePaths:
  - node_modules/
  - dist/
  - build/

# Regular expressions to allowlist certain key patterns
# Keys matching ANY of these regexes will be ignored
allowlistRegex:
  - "^test-.*"
  - "^example-.*"
```

### Configuration Options

| Option           | Description                                                                                       |
| ---------------- | ------------------------------------------------------------------------------------------------- |
| `ignorePaths`    | Array of **path substrings** to exclude from scanning (simple substring match, not glob patterns) |
| `allowlistRegex` | Array of regex patterns; matching keys are ignored                                                |

---

## How It Works

1. Action triggers on `pull_request`
2. Fetches PR file diffs via GitHub API
3. Parses diffs and extracts **added lines only**
4. Scans each line for AI API key patterns
5. Applies ignore and allowlist rules
6. Adds inline annotations for each finding
7. Posts a PR summary comment
8. Fails the workflow if matching keys are found; repository rules determine whether that blocks merging

Large diffs without patch data are skipped with a warning to avoid false confidence.

---

## Security Notes

* **Never commit real API keys**, even in tests or examples
* Treat detected keys as compromised and rotate immediately
* This action scans **PR diffs only**, not the full codebase
* Require the check in repository rules if failures must block merging

---

## Why Not Use a Generic Secret Scanner?

Generic secret scanners are powerful, but often:

* noisy
* over-configured
* focused on all credentials, not AI usage

This action is intentionally narrow:

> **One focused check: flag known AI API key patterns in added pull request lines.**

---

## License

MIT

---

## Keywords

GitHub Action, GitHub Marketplace, OpenAI API key scanner, Anthropic API key, Google AI API key,
AI security, secret scanning, pull request security, prevent API key leaks

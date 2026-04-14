# CLAUDE.md

This file provides guidance for AI assistants (Claude Code and similar tools) working in this repository.

## Repository Overview

**Owner:** 412251504-max (John-Shengxi-Wang)
**Repository:** 412251504-max/John-Shengxi-Wang
**Status:** Freshly initialized — no application code yet.

The repository currently contains only:
- `README.md` — project title placeholder

## Current State

This is a new, empty repository created on 2026-04-13. No language, framework, build system, or tooling has been established yet. When the project direction is decided, this file should be updated to reflect the actual stack and conventions.

## Git Workflow

### Branch Strategy

- **`main`** — stable, production-ready code. Never push directly.
- **Feature branches** — all work is done on named branches before merging to `main`.

Observed branch naming convention:
```
claude/<short-description>-<random-suffix>
feature/<short-description>
fix/<short-description>
```

### Commit Messages

Write concise, imperative-mood commit messages:
```
Add user authentication module
Fix null pointer in payment handler
Refactor API client to use async/await
```

- First line: 50 characters or fewer, imperative mood
- Body (optional): explain *why*, not *what*, wrapped at 72 characters
- Reference issues/PRs when relevant: `Closes #42`

### Push Procedure

```bash
git push -u origin <branch-name>
```

If the push fails due to a network error, retry up to 4 times with exponential backoff (2 s, 4 s, 8 s, 16 s).

### Pull Requests

- Do **not** create a PR unless the user explicitly requests one.
- Target branch: `main`
- PR titles should be short (under 70 characters).

## Development Guidelines (to be updated as the stack is chosen)

### Adding New Tooling

When a language or framework is added to this repo, update this file with:

1. **Language & runtime version** (e.g., Node 22, Python 3.12, Go 1.23)
2. **Package manager** and install command (e.g., `npm install`, `pip install -r requirements.txt`)
3. **How to run the project** locally
4. **How to run tests** and what the test framework is
5. **Lint/format commands** and configuration file locations
6. **Build command** if applicable
7. **Environment variables** — list required vars and point to any `.env.example`

### General Coding Conventions

Regardless of language/framework, follow these principles:

- **Minimal changes** — do not refactor code, add comments, or clean up surrounding code unless that is the explicit task.
- **No speculative abstractions** — implement only what the task requires; do not build for hypothetical future requirements.
- **No unused code** — do not leave dead code, unused imports, or placeholder stubs.
- **Security** — never introduce command injection, XSS, SQL injection, or other OWASP Top 10 vulnerabilities. Validate at system boundaries (user input, external APIs) only.
- **No secrets in code** — environment variables for credentials, API keys, and secrets. Never commit `.env` files.

## File Structure (expected once project is populated)

```
John-Shengxi-Wang/
├── CLAUDE.md          # This file
├── README.md          # Project overview for humans
├── .gitignore         # Ignore build artifacts, .env, node_modules, etc.
└── <src/>/            # Application source (to be added)
```

Update this section once the actual directory layout is established.

## Working with AI Assistants

- Always read a file before editing it.
- Prefer the smallest correct change — do not rewrite working code.
- Use the branch listed in session instructions (e.g., `claude/add-claude-documentation-ngNoB`) for all commits during that session.
- Confirm with the user before destructive operations (force push, branch deletion, dropping data).
- Do not push to `main` directly.

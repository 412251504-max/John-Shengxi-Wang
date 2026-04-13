# CLAUDE.md

This file provides guidance for AI assistants (Claude Code and similar tools) working in this repository.

## Repository Overview

**Repository:** John-Shengxi-Wang  
**Remote:** 412251504-max/John-Shengxi-Wang  
**Status:** Newly initialized — no source code, framework, or build system has been added yet.

The repository currently contains only a placeholder `README.md`. All development conventions and tooling will be established as the project grows.

## Current Repository Structure

```
John-Shengxi-Wang/
├── README.md       # Minimal placeholder header
└── CLAUDE.md       # This file
```

## Git Workflow

### Branches

- `main` — the stable primary branch; treat this as the source of truth
- Feature branches follow the pattern `<actor>/<short-description>` (e.g., `claude/add-claude-documentation-oftud`)

### Commit Signing

All commits are signed by default using an SSH key configured in the local git config. Do not pass `--no-gpg-sign` or bypass signing.

### Push Conventions

Always push with the upstream flag:

```bash
git push -u origin <branch-name>
```

If a push fails due to a network error, retry with exponential backoff: wait 2 s, 4 s, 8 s, 16 s between retries.

### Pull Request Policy

Do **not** open a pull request unless the user explicitly requests one.

## Development Guidelines for AI Assistants

### Before Making Changes

1. Read every relevant file before editing it — never modify code you haven't seen.
2. Prefer editing existing files over creating new ones.
3. Scope changes to exactly what was requested; do not add extra features, refactors, or comments.

### Code Quality

- Write safe, secure code; avoid OWASP top-10 issues (SQL injection, XSS, command injection, etc.).
- Do not add error handling for scenarios that cannot occur.
- Do not create helpers or abstractions for one-off operations.
- Do not add docstrings, type annotations, or comments to code you did not change.

### File Hygiene

- Do not create documentation files (e.g., `*.md`) unless explicitly asked.
- Do not introduce backwards-compatibility shims for code that is simply being removed.
- Remove unused code entirely rather than commenting it out or renaming with a leading underscore.

## Updating This File

When the project acquires a technology stack, build system, test framework, or additional conventions, update the relevant sections below and remove the placeholder notes.

### Planned Sections (populate as the project evolves)

- **Technology Stack** — languages, frameworks, runtime versions
- **Build & Run** — how to install dependencies, build, and start the application
- **Testing** — test framework, how to run tests (`npm test`, `pytest`, `make test`, etc.)
- **Linting & Formatting** — tools and commands (`eslint`, `ruff`, `prettier`, etc.)
- **Environment Variables** — required variables and how to configure them
- **CI/CD** — pipeline overview and branch protection rules
- **Architecture** — high-level design, key modules, data flow

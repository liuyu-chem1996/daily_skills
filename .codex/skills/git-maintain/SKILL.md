---
name: git-maintain
description: perform git maintenance and version control
---

# Agent Skill: Git Maintenance & Version Control

## Core Directives
1. **Always Check Status First:** Before making any commits, you must run `git status` and `git diff` to understand exactly what changes have been made.
2. **Atomic Commits:** If there are changes across multiple, unrelated files, group them into separate, logical commits. Do not lump everything into a single "updated files" commit.
3. **Semantic Commit Formatting:** You must strictly use the Conventional Commits format:
   - `feat:` for new features
   - `fix:` for bug fixes
   - `refactor:` for code changes that neither fix a bug nor add a feature
   - `chore:` for routine tasks (e.g., updating dependencies, modifying `.gitignore`)
4. **Safety Protocols:** - NEVER commit directly to the `main` or `master` branch.
   - NEVER run `git push --force`.
   - If conflicts arise, stop immediately and ask the user for manual intervention.

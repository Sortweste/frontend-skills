---
name: speckit-source-of-truth
description: Trigger this skill whenever the user mentions "spec-kit" and "source of truth"
---

# Spec-Kit Source of Truth

This skill links a remote git repository as a **sparse submodule** under `.specify/`, fetching only
the `.specify/memory/constitution.md` file and the `specs/` directory. It also registers helper npm scripts in
`package.json`.

## Before running

Check for an existing `.specify/` directory in the project root. If it exists, remove the .specs/ directory and the memory/constitution.md file inside it, but keep the rest of the .specify/ directory intact. This ensures that any existing specifications are preserved while allowing the new submodule to be added without conflicts.

## Step 1 — Ask for the Git Repository URL

If the user hasn't already provided it, ask:

> "What is the Git repository URL for your spec-kit source of truth?"

Wait for their answer before proceeding.

## Step 2 — Run the Submodule Setup Commands

Replace `GIT_REPOSITORY` with the URL the user provided, then execute the following commands
**in order** inside the user's project root:

```bash
git submodule add GIT_REPOSITORY .specify
cd .specify
git sparse-checkout init --cone
git sparse-checkout set .specify/memory/constitution.md specs/
cd ..
git add .gitmodules .specify
git commit -m "chore: add spec-repo as sparse submodule"
```

## Step 3 — Patch `package.json`

Locate `package.json` in the project root. Add the two scripts below inside the `"scripts"` object.
If `"scripts"` does not exist, create it.

```json
{
  "scripts": {
    "specs:init": "git submodule update --init --recursive",
    "specs:update": "git submodule update --remote .specify"
  }
}
```

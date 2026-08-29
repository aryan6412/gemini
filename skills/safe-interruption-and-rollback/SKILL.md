---
name: safe-interruption-and-rollback
description: Resilience and recovery protocol for interrupted, cancelled, or aborted agent executions. Ensures project integrity after mid-turn stops, detects broken/partial file edits, and provides safe rollback and recovery mechanisms.
---

# Safe Interruption, Recovery & Rollback Protocol

You are a resilient software engineer. Users may cancel, stop, or abort agent execution at any moment (e.g. if they entered a wrong prompt, want to redirect the task, or changed requirements). When this happens, files might be left in a partial, broken, or half-edited state.

Follow this strict protocol to ensure zero code corruption and instant recovery:

---

## 🛑 1. Post-Interruption Health Check (First Action on New Turn)
Whenever starting a new prompt after a previous run was stopped or failed:
1. **Check Modified Files**: Inspect `git status` or recently modified files to see if any file was left half-written.
2. **Verify Syntax Integrity**: If TypeScript, JavaScript, Python, or PHP files were touched, ensure there are no unclosed brackets, missing imports, or truncated code blocks.
3. **Auto-Restore if Broken**: If a file was left in an un-compilable or corrupted state from the stopped run, either restore it cleanly (`git checkout -- <file>`) or surgically repair the syntax before executing the new instruction.

---

## 🛡️ 2. Preventative Safeguards During Execution

### A. Surgical Edits over Destructive Overwrites
* **Prefer Targeted Edits**: Always use `replace_file_content` targeting specific functions/blocks instead of completely overwriting large 500+ line files with `write_to_file`.
* If execution stops mid-turn, targeted edits only affect a small block, leaving the rest of the file 100% intact.

### B. Git Safety Checkpoints Before Multi-File Refactors
* Before touching $>3$ files or doing major structural refactors, verify the working directory is clean or create a temporary git checkpoint:
  ```bash
  git stash create # or lightweight commit
  ```
* If the user stops execution or rejects the outcome, rolling back to a pristine state is instantaneous:
  ```bash
  git checkout .
  # or git restore .
  ```

### C. Non-Destructive Temporary Files
* When generating complex new modules or massive migrations, write to temporary/scratch paths first, test them, and only move them to production paths once validated.

---

## ⚡ 3. Quick User Rollback Commands
If a user ever says *"revert that"*, *"undo last edit"*, or *"go back to how it was"*:
1. Immediately run `git checkout -- <file>` or `git restore <file>` for the files touched in the last turn.
2. Report exactly what was restored so the user knows their codebase is clean.

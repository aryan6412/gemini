---
name: systematic-debugging
description: Evidence-based root-cause diagnosis and bug resolution workflow. Use when investigating errors, test failures, performance bottlenecks, or unexpected behavior to reproduce, isolate, and verify fixes without guesswork.
---

# Systematic Debugging & Root Cause Analysis

You are a principal debugging specialist. When resolving bugs, exceptions, test failures, or crashes, you MUST follow a disciplined evidence-based loop rather than making speculative trial-and-error changes.

---

## 🔬 The 4-Phase Debugging Protocol

### Phase 1: Reproduce & Capture Evidence
1. **Never guess the cause without logs**: Examine stack traces, error messages, and system logs.
2. **Create a minimal reproduction**:
   * Write a failing unit/integration test, or identify the exact input payload that reproduces the bug.
   * Pinpoint the exact line number, function, and state where execution deviates from expected behavior.

### Phase 2: Isolate the Root Cause
1. **Trace the Data Flow**:
   * Verify assumptions at each boundary: input validation $\rightarrow$ service logic $\rightarrow$ database/external APIs $\rightarrow$ response serialization.
   * Add focused temporary logging (`console.log`, `logger.debug`) if runtime state is ambiguous.
2. **Distinguish Symptom vs Root Cause**:
   * *Symptom*: `TypeError: Cannot read properties of undefined (reading 'id')`
   * *Root Cause*: Upstream database query returned `null` because user tenant scoping filtered out the row.

### Phase 3: Implement Minimal Surgical Fix
1. **Change only what is necessary**:
   * Fix the root cause directly at the source.
   * Avoid defensive "band-aids" (e.g. adding `if (!x) return null` everywhere instead of fixing why `x` is missing).
   * Clean up any temporary debug logs before finishing.

### Phase 4: Verify & Prevent Regressions
1. **Re-run the reproduction test**: Confirm the failing test now passes.
2. **Run the full test suite**: Verify no adjacent features or edge cases broke.
3. **Add regression guard**: Ensure the newly written test remains permanently in the test suite.

# AI Agent Instructions (AGENTS.md)

This document defines how AI agents (Cursor, Codex, Claude Code, etc.)
should behave when working in this repository.

The goal is:
- Safe default behavior
- Explicit permission for large changes
- Clear workflow for proposals and refactors
- No limitation on AI capability when authorized

---

## 1. Core Principle (Most Important)

- AI capability is NOT restricted
- Default behavior must be SAFE and CONSERVATIVE
- Explicit user instructions ALWAYS override all rules and specs

If the user explicitly requests:
- a complete feature
- a refactor
- cross-file or cross-module changes

AI is fully authorized to proceed.

---

## 2. General Behavior Rules

- Prefer minimal, incremental changes by default
- Never assume refactoring is allowed unless stated
- Do not make architectural decisions silently
- When scope or impact is unclear, STOP and ASK
- Code clarity > clever abstractions

AI should behave like a senior engineer who knows when to ask for approval.

---

## 3. Workflow Rules (Key Section)

### 3.1 Small / Localized Changes
Examples:
- Bug fixes
- Single-file changes
- Minor logic adjustments

Rules:
- Direct implementation is allowed
- Inline edits are preferred
- No plan or proposal is required

---

### 3.2 Multi-file Changes / New Features
Examples:
- New functionality
- Changes affecting multiple files or modules
- Behavior changes

Rules:
- AI MUST propose a plan before implementation
- The plan should include:
  - Scope of change
  - Affected files or modules
  - Potential risks
- Implementation starts only after user confirmation

---

### 3.3 Refactoring Tasks
Examples:
- Code cleanup
- Structural changes
- Abstraction improvements

Rules:
- Refactoring is NOT allowed by default
- AI MUST propose a refactoring plan
- AI MUST wait for explicit user approval before changing code

---

### 3.4 Unclear or Risky Tasks

If any of the following is true:
- Requirements are ambiguous
- Impact cannot be determined confidently
- There is risk of breaking existing behavior

AI MUST stop and ask for clarification before proceeding.

---

## 4. OpenSpec Integration (Authoritative Specs)

When the request:
- Mentions planning, proposals, specs, or change plans
- Introduces new capabilities, breaking changes, or architecture shifts
- Involves large refactors, performance, or security work
- Is ambiguous and requires authoritative guidance

AI MUST open and follow:

@/openspec/AGENTS.md

That document defines:
- Proposal workflow
- Spec format and conventions
- Rules for large-scale or structural changes
- Project-specific governance

Explicit user instructions override OpenSpec rules.

---

## 4.1 MCP Documentation Lookup (Context7)

When planning changes that involve major architecture, framework-level behavior,
or tech-stack decisions, the AI MUST query official documentation via the Context7 MCP
**if available**. If the Context7 MCP is not available, the AI MUST explicitly state that
and proceed using local project context and existing specifications only.

Examples of when to use Context7:
- Selecting, introducing, or changing core frameworks or libraries
- Introducing new infrastructure components (e.g., messaging systems, service discovery)
- Altering architectural patterns or layering rules
- Security-sensitive, lifecycle-related, or configuration-dependent framework behavior
  (e.g., Spring Security, framework annotations, initialization order, version differences)

If a user explicitly opts out, the MCP lookup may be skipped.

---

## 5. Allowed Actions

AI MAY:
- Add new files, classes, or components when authorized
- Modify existing code within approved scope
- Implement complete features when explicitly requested
- Perform large refactors when explicitly authorized
- Add tests or documentation
- Improve readability without changing behavior

---

## 6. Forbidden Actions (Unless Explicitly Authorized)

AI MUST NOT:
- Delete files or directories
- Rename public APIs, endpoints, or exported functions
- Change database schemas or persistent data structures
- Upgrade frameworks or dependencies
- Change project build or tooling structure
- Introduce new architectural patterns silently

User authorization overrides these rules.

---

## 7. Backend-Specific Rules (If Applicable)

- Follow layered architecture (Controller / Service / Repository)
- Controllers handle request/response only
- Do not change API contracts without explicit approval
- Maintain backward compatibility by default
- Keep changes localized and testable

---

## 8. Frontend-Specific Rules (If Applicable)

- Use Vue 3 Composition API
- Keep components small and focused
- Do not change UI behavior or UX flows unless requested
- Do not change API request paths or contracts
- Prefer explicit state management (e.g. Pinia)

---

## 9. Communication Rules

AI MUST:
- Explain non-trivial changes
- Clearly state assumptions
- Highlight risky or irreversible operations
- Never hide side effects or implicit changes

---

## 10. Conflict Resolution Priority

1. Explicit user instructions
2. This AGENTS.md
3. @/openspec/AGENTS.md
4. Existing project conventions
5. AI default behavior

If conflicts remain, ask before proceeding.

---

## 11. Language Policy

All responses, plans, explanations, and code comments MUST be written in Simplified Chinese.

1. Do not switch languages unless the user explicitly requests otherwise.
2. Do not include English explanations, except for code identifiers, APIs, or proper nouns.

---

End of instructions.

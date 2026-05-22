<!--
SYNC IMPACT REPORT
==================
Version change: 0.0.0 (uninitialized template) → 1.0.0

Modified principles:
  All 9 principles are new — replacing blank template placeholders.

Added sections:
  - I.   Architecture & Stack
  - II.  Containerization
  - III. Database & Persistence
  - IV.  Testing Strategy
  - V.   UI & Styling Framework
  - VI.  Component Design
  - VII. State Management
  - VIII.Code Quality
  - IX.  Execution Boundaries
  - Infrastructure & Deployment (Section 2)
  - Development Workflow (Section 3)
  - Governance

Removed sections:
  - All template placeholder stubs

Templates reviewed:
  ✅ .specify/templates/spec-template.md — technology-agnostic; compatible as-is.
  ✅ .specify/templates/plan-template.md — Constitution Check section is generic; will be filled per-feature.
  ⚠  .specify/templates/tasks-template.md — Contains TDD-first language ("Write these tests FIRST, ensure
      they FAIL before implementation") which conflicts with Principle IV (no TDD). The template marks
      test tasks as OPTIONAL, which mitigates the conflict, but the ordering note should be read as
      "write tests alongside or after implementation" per Principle IV.

Follow-up TODOs:
  - Consider patching tasks-template.md to remove the TDD-ordering note to prevent confusion
    with Principle IV.
  - No command files found under .specify/templates/commands/ — no updates required there.
-->

# Distance Tracker Constitution

## Core Principles

### I. Architecture & Stack

All application code MUST be written in TypeScript — no JavaScript files in src directories.
Vite MUST be used as the build tool and development bundler. The UI framework MUST be React 18 or
later. Single Page Application routing MUST be implemented with React Router; no server-side
rendering or multi-page navigation patterns.

**Rationale**: Consistency across the full stack reduces context-switching, TypeScript eliminates
an entire class of runtime errors, and Vite's fast HMR directly improves developer velocity.

### II. Containerization

The application MUST run inside a Docker container in all environments (development and production).
The production container MUST serve the pre-built static assets via an efficient static file server
(e.g., nginx); it MUST NOT run a development server or Node process in production.

**Rationale**: Containers guarantee environment parity between developer machines and production.
Serving pre-built assets from nginx is orders-of-magnitude more efficient than running a Node
process at runtime.

### III. Database & Persistence

All persistent state MUST be stored in a PostgreSQL database. PostgreSQL MUST run in its own
dedicated Docker container, isolated from the application container. The two containers MUST
communicate exclusively over a shared Docker network; the database port MUST NOT be exposed to
the host in production.

**Rationale**: Container isolation limits the blast radius of a compromised service. A shared
Docker network provides connectivity without exposing the database to the outside world.

### IV. Testing Strategy

Test-Driven Development (TDD) is explicitly NOT used on this project. Tests MUST be written as
end-to-end or integration tests using Playwright, either alongside or after component completion.
Unit tests for pure utility functions are permitted but not required.

**Rationale**: E2E and integration tests validate the full user-facing behavior and catch
regressions at the boundaries that matter most. TDD discipline is traded for iteration speed
during initial component development.

### V. UI & Styling Framework

All UI widgets, form controls, layout structures, and interactive components MUST use Google
Material UI (MUI). Custom CSS or inline styles MUST only be used where MUI's `sx` prop or
theme system cannot satisfy the requirement. All styling MUST follow official MUI theme design
guidelines and MUST use MUI system properties (`sx`, `styled`, `ThemeProvider`) — not external
CSS-in-JS libraries.

**Rationale**: MUI provides an accessible, well-tested, and design-consistent component library.
Limiting styling to MUI's own system prevents style fragmentation across the codebase.

### VI. Component Design

React components MUST be:
- **Modular**: each component lives in its own file, importable independently.
- **Reusable**: no business-logic hard-wiring inside presentational components.
- **Strictly functional**: no class components; use function components exclusively.
- **Single-purpose**: one clear responsibility per component; split if a component has two
  distinct concerns.

**Rationale**: Single-purpose functional components are easier to test in isolation, easier to
refactor, and align with how React's hooks model is designed to be used.

### VII. State Management

Global and local state MUST be managed using React's built-in primitives: `useState`,
`useReducer`, and the Context API. External state management libraries (Redux, Zustand, Jotai,
MobX, etc.) MUST NOT be introduced unless a feature specification explicitly requires one and
documents why built-in primitives are insufficient.

**Rationale**: React's built-in state tools cover the vast majority of SPA use cases. Adding a
state library before it is needed is premature complexity that adds bundle size, indirection,
and a new mental model for every contributor.

### VIII. Code Quality

All TypeScript interfaces and component props MUST be explicitly typed. The `any` type is
prohibited — use `unknown` with type guards, or model the type correctly. The codebase MUST
pass a strict TypeScript configuration (`strict: true`). Code MUST follow clean-code principles:
meaningful names, small focused functions, no dead code, no commented-out code blocks.

**Rationale**: Strict typing turns a class of runtime bugs into compile-time errors. Clean code
ensures every future contributor can read, understand, and safely modify the codebase.

### IX. Execution Boundaries

In all feature specifications and implementation plans, the "what" (requirements, user scenarios,
acceptance criteria) and the "why" (rationale, goals, success metrics) MUST be separated from
the "how" (implementation steps, file paths, technology choices). Specs describe intent;
plans describe approach; tasks describe actions.

**Rationale**: Keeping intent separate from implementation allows a spec to remain stable even
as the implementation strategy evolves, and prevents premature lock-in to a specific solution.

## Infrastructure & Deployment

Docker Compose MUST be used to orchestrate the application and database containers together.
The Compose file MUST define at minimum two services: `app` (the React SPA container) and
`db` (the PostgreSQL container), connected via a named network. Environment-specific
configuration (database credentials, API keys) MUST be supplied via environment variables —
never hard-coded in source files or committed to the repository.

Production builds MUST be triggered via a multi-stage Dockerfile: a Node build stage compiles
the Vite application, and a final nginx stage copies only the built artifacts.

## Development Workflow

All Playwright tests MUST pass before a feature branch is considered complete. TypeScript
compilation MUST succeed with zero errors (`tsc --noEmit`) before merging. No `any` types
may be introduced without an immediate accompanying comment explaining why and a linked ticket
to resolve it.

Code review MUST verify:
1. Constitution compliance (all 9 principles).
2. No exposed secrets or hard-coded credentials.
3. Playwright test coverage for new user-facing behavior.

## Governance

This constitution supersedes any prior conventions, READMEs, or informal agreements on this
project. All pull requests and code reviews MUST verify compliance with these principles.

**Amendment procedure**: Any principle change requires (a) a written rationale, (b) a version
bump per the semantic versioning policy below, and (c) an update to all affected templates.

**Versioning policy**:
- MAJOR: Backward-incompatible governance change — principle removed or fundamentally redefined.
- MINOR: New principle or section added, or material expansion of existing guidance.
- PATCH: Clarification, wording, or non-semantic refinement.

**Compliance review**: Conduct a constitution compliance check at each feature plan phase gate
(see `plan-template.md` Constitution Check section).

**Version**: 1.0.0 | **Ratified**: 2026-05-21 | **Last Amended**: 2026-05-21

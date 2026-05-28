<!--
Sync Impact Report
- Version: placeholder → 1.0.0
- Modified principles: Added 5 core principles
- Added sections: Training Constraints, Development Workflow
- Removed sections: none
- Templates reviewed:
  - .specify/templates/spec-template.md ✅ reviewed, no constitution-driven changes required
  - .specify/templates/plan-template.md ✅ reviewed, no constitution-driven changes required
  - .specify/templates/tasks-template.md ✅ reviewed, no constitution-driven changes required
- Follow-up TODOs: none
-->

# ContosoDashboard Constitution

## Core Principles

### 1. Training-First Clarity
All project documentation, code comments, and user-facing guidance MUST clearly identify ContosoDashboard as a training artifact and not a production-ready system. This ensures learners and reviewers do not mistake the repository for a deployable enterprise application.

### 2. Replaceable Infrastructure
Infrastructure dependencies MUST be abstracted behind interface boundaries so the application can run locally with built-in services and later migrate to cloud providers. Any code path that depends on external services MUST include a documented local fallback or a training-specific mock implementation.

### 3. Realistic Security with Safe Limits
Security behavior MUST model real-world authorization and access control while preserving a safe training boundary. Mock authentication is permitted only when the limitation is documented, real credentials are not used, and authorization checks prevent IDOR and unauthorized data access.

### 4. Specification-Driven Incremental Work
All feature work MUST begin with a written specification, acceptance criteria, and independent test scenarios. Changes MUST be delivered in small, independently testable increments so learners can validate each behavior before moving to the next.

### 5. Explicit Simplicity and Transparency
The codebase MUST remain simple, explicit, and easy to follow for training audiences. Hidden framework behavior, excessive abstraction, or implementation shortcuts that obscure intent MUST be avoided. Every migration path, security assumption, and training boundary MUST be documented.

## Training Constraints

- ContosoDashboard MUST remain offline-first and local-only for training use.
- External cloud services, external APIs, and production-grade secrets MUST NOT be introduced in training branches.
- The repository MUST continue using SQLite and built-in framework capabilities for the default training experience.
- Any production-grade implementation path (Azure SQL, cloud storage, enterprise identity) MUST be documented as a migration option, not as the default behavior.
- README and architecture documentation MUST preserve the training-only disclaimer, limitations, and security warnings.

## Development Workflow

- New work MUST start with a feature specification using the available spec template and include independent acceptance criteria.
- Pull requests MUST reference the constitution and explain how the proposed change preserves training safety, offline operation, and security patterns.
- Code review MUST verify that mock authentication remains clearly bounded, authorization is enforced at page and service layers, and offline operation is maintained.
- Any change affecting authentication, authorization, storage, or infrastructure MUST update the README and training architecture guidance.
- Testing MUST include a training scenario and at least one authorization or data isolation check for every protected flow.

## Governance

This constitution is the authoritative guidance for ContosoDashboard development. All plans, specifications, and tasks MUST be evaluated against it before implementation.

- Amendments require a documented rationale, review, and an explicit version update in this file.
- Every pull request that changes behavior, constraints, or training guidance MUST cite this constitution.
- The constitution is the source of truth for training constraints and review expectations.

Versioning policy:
- MAJOR version bump for governance or principle redefinition or removal.
- MINOR version bump for adding a new principle, section, or materially expanded guidance.
- PATCH version bump for wording, clarification, or typo fixes.

**Version**: 1.0.0 | **Ratified**: 2026-05-28 | **Last Amended**: 2026-05-28

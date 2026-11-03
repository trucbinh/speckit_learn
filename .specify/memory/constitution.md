<!--
Sync Impact Report
Version change: placeholder template -> 1.0.0
Modified principles: initial constitution adoption
Added sections: Core Principles, Additional Constraints, Development Workflow
Removed sections: none
Templates requiring updates: ✅ .specify/templates/plan-template.md, ✅ .specify/templates/spec-template.md, ✅ .specify/templates/tasks-template.md
Follow-up TODOs: TODO(RATIFICATION_DATE): original project ratification date not recorded in repo
-->

# Speckit Learn Constitution

## Core Principles

### I. Requirement-First Delivery
All work MUST begin from explicit user value, a clear scope boundary, and testable acceptance criteria. If the requirement is ambiguous, the team MUST capture the missing fact before implementation begins. This keeps the project aligned to learning outcomes, user needs, and the generated specification workflow instead of drifting into speculative implementation.

### II. Testable Progress Over Assumption
No feature, bug fix, or workflow change is complete unless it can be verified through a concrete test, review, or observable outcome. The default expectation is to write or update the smallest relevant validation before implementation, then confirm successful execution before claiming readiness. This prevents unverified behavior from being treated as complete.

### III. Simple, Explicit Architecture
The codebase MUST favor simple, explainable structures over clever abstractions. Components, workflows, and data flows must have clear responsibility boundaries, readable naming, and minimal hidden coupling. When complexity is unavoidable, it MUST be justified with a documented tradeoff and a clear path to simplification.

### IV. Security and Safety by Default
Changes MUST respect secure defaults, validate inputs, avoid unsafe assumptions, and preserve the least-privilege principle for credentials, permissions, and external dependencies. Security and reliability concerns are not optional follow-up work; they are design constraints to be addressed alongside feature delivery.

### V. Traceable Collaboration and Documentation
Every significant change MUST remain traceable to the relevant requirement, design artifact, or review decision. Specifications, plans, tasks, and implementation notes MUST stay consistent with one another, and the repository must retain enough context for future contributors to understand why a decision was made. This is essential for a learning-oriented project that depends on repeatable process.

## Additional Constraints

The project operates as a specification-driven workflow and MUST preserve that discipline in all repositories, branches, and task execution. Any feature work must be anchored in a user story, a measurable outcome, and a clear validation step. The project MUST prioritize maintainability, readability, and reproducibility over ad hoc optimization.

The repository MUST keep its templates, workflow definitions, and governance documentation synchronized. When the constitution changes, the affected templates and generated artifacts must be reviewed for alignment before the change is considered complete. The project MUST not silently drift away from the spec-first model that makes it usable for iterative learning and implementation.

## Development Workflow

All work proceeds through the following sequence unless a narrower task is explicitly justified:

1. Define or refine the requirement and scope.
2. Capture the expected behavior in a testable form or acceptance criteria.
3. Implement the minimal change that satisfies the requirement.
4. Validate behavior with the relevant checks and document any remaining caveats.
5. Review for drift against the governing templates and the constitution.

Pull requests and code review must verify compliance with the project principles above, not just correctness in isolation. Reviewers MUST look for unclear requirements, missing validation, over-engineering, security regressions, and documentation drift. If a workaround is introduced, the tradeoff MUST be explicitly called out and justified.

## Governance

This Constitution governs all design and implementation decisions in the Speckit Learn repository. It supersedes inconsistent local practices and ad hoc shortcuts. When a new principle or constraint is added, the existing templates and workflow artifacts MUST be checked for alignment and updated as needed.

Amendments require a written change to this document, a version bump following semantic versioning, and documentation of the rationale for the change. Breaking changes to the governance model or principle structure require a major version bump; additions or material expansions require a minor version bump; clarifications and wording improvements require a patch bump. Any review that identifies noncompliance must either correct the issue or document the exception with a clear reason and an owner.

The project team MUST review the constitution during major planning and milestone transitions to confirm that the rules remain useful, testable, and aligned with the team’s actual practice. This is a living governance document, not a static policy statement.

**Version**: 1.0.0 | **Ratified**: TODO(RATIFICATION_DATE): original project ratification date not recorded in repo | **Last Amended**: 2026-11-03

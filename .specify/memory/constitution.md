<!--
Sync Impact Report
Version change: 2.0.0 -> 2.1.0
Bump rationale: Minor; adds a mandatory audit-column and soft-deletion requirement for
  PostgreSQL tables.
Modified principles: III. PostgreSQL Integrity and Migrations (expanded with mandatory
  audit columns and soft-delete tracking).
Added principles: none
Added sections: none
Removed sections: none
Templates requiring updates: ✅ .specify/templates/plan-template.md,
  ✅ .specify/templates/spec-template.md, ✅ .specify/templates/tasks-template.md
Workflow guidance reviewed: ✅ .specify/workflows/speckit/workflow.yml;
  no separate command templates or runtime README found.
Follow-up TODOs: TODO(RATIFICATION_DATE): original ratification date is not recorded.
-->

# Speckit Learn Constitution

## Core Principles

### I. REST Contract and Compatibility
Every REST feature MUST define its resource paths, HTTP methods, request and response
representations, success and error status codes, and observable behavior in its specification
or an associated API contract before implementation. Responses MUST follow the documented
contract. Changes MUST preserve existing clients unless an explicitly versioned breaking
change is approved and documented; the plan MUST state the API versioning and compatibility
approach without assuming a particular contract format. This makes client-facing behavior
reviewable and testable.

### II. Idiomatic Go and Maintainability
The service MUST be implemented in Go using idiomatic, formatted code, explicit error
handling, and clear package and responsibility boundaries. Abstractions MUST address an
identified need and remain proportionate to it. Go version, web framework, database driver,
and hosting platform are intentionally unspecified; a plan MUST record a choice only when
the feature requires one. These rules keep implementation understandable without prescribing
unconfirmed dependencies.

### III. PostgreSQL Integrity and Migrations
Persistent application data MUST use PostgreSQL. Every schema change MUST be represented
by a reviewed, ordered migration that can be applied reproducibly; application code and
migrations MUST remain compatible during the documented rollout. Database changes and
related writes that must succeed or fail together MUST execute in a transaction. The schema
MUST enforce applicable integrity constraints, and queries MUST pass values as parameters
rather than concatenate untrusted input. Every PostgreSQL table created or managed by the
application, including join and association tables, MUST include the audit columns
`created_at`, `created_by`, `updated_at`, `updated_by`, `deleted` (boolean), and `deleted_at`.
Soft deletion MUST set `deleted` to true and record the deletion time in `deleted_at`; normal
reads MUST exclude soft-deleted rows unless the operation explicitly needs them. Migrations,
models, and persistence tests MUST preserve these columns and their semantics. This protects
data consistency and supports accountability and safe change over time.

### IV. Boundary Validation, Errors, and Security
The API MUST validate untrusted path, query, header, and body inputs at the request boundary
before using them. Invalid requests MUST receive an appropriate 4xx response; successful
and failed operations MUST use documented HTTP status codes and a consistent, machine-readable
error shape. Error responses MUST NOT expose secrets, SQL details, stack traces, or other
internal implementation data. Protected operations MUST enforce authorization, and secrets
and database access MUST follow least-privilege practices. These controls make failures
predictable while limiting avoidable security exposure.

### V. Verification and Testing
Behavior-changing work MUST include automated tests appropriate to the change, and the
relevant test suite MUST pass before the work is considered complete. Go logic MUST have
focused unit tests; HTTP behavior MUST have contract or handler-level tests; persistence
or migration changes MUST have PostgreSQL-backed integration coverage. Tests MUST check
success, invalid-input, and relevant failure paths, including transaction behavior when
applicable. Tests must not depend on unspecified frameworks or drivers.

### VI. Traceable Requirements and Delivery
Each change MUST trace to a stated requirement, API behavior, data rule, or documented
exception. Specifications, plans, tasks, migrations, tests, and implementation MUST agree
on the behavior delivered. Any deferred requirement or exception MUST identify its rationale
and impact. This keeps the specification-driven workflow auditable and repeatable.

## Additional Constraints

The project is a Go REST API backed by PostgreSQL. These technology choices are confirmed
requirements. Plans and specifications MUST NOT silently substitute another language, API
style, or primary database. The project MUST NOT infer a specific Go release, framework,
database driver, or hosting platform; those decisions remain open until supported by a
feature requirement or an explicit project decision.

API contracts MUST state compatibility expectations, and database plans MUST state how
migrations and transaction boundaries preserve data integrity. Tests MUST exercise behavior
at the API and persistence boundaries when those boundaries change. Every PostgreSQL table
created or managed by the application, including join and association tables, MUST have
`created_at`, `created_by`, `updated_at`, `updated_by`, `deleted` (boolean), and `deleted_at`
columns; plans and specifications MUST account for them when defining or changing schema.
No particular column types beyond `deleted` being boolean, defaults, nullability, API
description format, migration tool, test library, or deployment environment is mandated
unless specified by a feature or an explicit project decision.

## Development Workflow

All work proceeds through these steps unless a narrower task is explicitly justified:

1. Define the user need, scope, acceptance criteria, API behavior, and relevant data rules.
2. Plan the Go service changes, compatibility impact, migration strategy, and validation.
3. Implement the smallest idiomatic Go change that satisfies the accepted requirement.
4. Add and run focused unit, API contract or handler, and PostgreSQL integration tests as
   applicable; verify migration and transaction behavior for data changes, including the
   required audit columns and soft-delete semantics for every application table.
5. Review the result for security, compatibility, maintainability, and consistency across
   specification, plan, tasks, code, tests, and migrations.

Reviewers MUST verify contract compatibility, boundary validation, safe error responses,
least privilege, parameterized database access, migration correctness, and test coverage
relevant to the change. Any exception MUST document its reason, scope, and follow-up owner.

## Governance

This Constitution governs all design and implementation decisions in the Speckit Learn
repository and supersedes inconsistent local practices. Amendments MUST be made in writing,
include their rationale, and update affected templates and workflow guidance in the same
change. Reviews MUST check compliance before a change is accepted; identified violations
MUST be corrected or recorded as explicit, justified exceptions with an owner.

Every amendment MUST update the version according to semantic versioning: MAJOR for
backward-incompatible governance or principle removals/redefinitions, MINOR for added
principles or materially expanded requirements, and PATCH for clarifications and
non-semantic wording changes. The team MUST review this Constitution during major planning
and milestone transitions and confirm that it remains testable and aligned with project
practice.

**Version**: 2.1.0 | **Ratified**: TODO(RATIFICATION_DATE): original project ratification date not recorded in repo | **Last Amended**: 2026-11-03

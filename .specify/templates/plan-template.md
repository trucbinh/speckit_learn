# Implementation Plan: [FEATURE]

**Branch**: `[###-feature-name]` | **Date**: [DATE] | **Spec**: [link]

**Input**: Feature specification from `/specs/[###-feature-name]/spec.md`

**Note**: This template is filled in by the `/speckit.plan` command; its definition describes the execution workflow.

## Summary

[Extract from feature spec: primary requirement + technical approach from research]

## Technical Context

<!--
  ACTION REQUIRED: Replace the content in this section with the technical details
  for the project. The structure here is presented in advisory capacity to guide
  the iteration process.
-->

**Language/Version**: Go ([select version when required; do not assume one])

**Primary Dependencies**: [Select only dependencies required by this feature; framework and database driver are unspecified]

**Storage**: PostgreSQL

**Testing**: Go unit tests, API contract/handler tests, and PostgreSQL-backed integration tests as applicable

**Target Platform**: [Determine from deployment requirements; hosting platform is unspecified]

**Project Type**: REST API

**Performance Goals**: [Feature-specific measurable goals or NEEDS CLARIFICATION]

**Constraints**: Preserve documented API compatibility; validate request inputs; use reviewed PostgreSQL migrations and transactions for atomic changes; every application table, including join and association tables, includes the required audit and soft-delete columns

**Scale/Scope**: [Feature-specific scale or NEEDS CLARIFICATION]

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

Before Phase 0 research and again after Phase 1 design, verify:

- The feature's REST paths, methods, request/response behavior, status codes, and
  compatibility/versioning approach are specified.
- Go, REST, and PostgreSQL remain the selected implementation requirements; no
  unspecified Go version, framework, driver, or hosting platform is treated as decided.
- PostgreSQL schema changes have an ordered migration and a compatibility/rollout plan;
  writes requiring atomicity have explicit transaction boundaries.
- Every PostgreSQL table created or managed by the application, including join and
  association tables, includes `created_at`, `created_by`, `updated_at`, `updated_by`,
  boolean `deleted`, and `deleted_at`; soft-delete behavior and migration coverage are
  defined.
- Request validation, consistent client-safe errors, authorization where applicable,
  and least-privilege database access are addressed.
- Tests cover changed Go behavior, API behavior, and PostgreSQL persistence/migrations
  as applicable.

## Project Structure

### Documentation (this feature)

```text
specs/[###-feature]/
├── plan.md              # This file (/speckit.plan command output)
├── research.md          # Phase 0 output (/speckit.plan command)
├── data-model.md        # Phase 1 output (/speckit.plan command)
├── quickstart.md        # Phase 1 output (/speckit.plan command)
├── contracts/           # Phase 1 output (/speckit.plan command)
└── tasks.md             # Phase 2 output (/speckit.tasks command - NOT created by /speckit.plan)
```

### Source Code (repository root)
<!--
  ACTION REQUIRED: Replace the placeholder tree below with the concrete layout
  for this feature. Delete unused options and expand the chosen structure with
  real paths (e.g., apps/admin, packages/something). The delivered plan must
  not include Option labels.
-->

```text
# [REMOVE IF UNUSED] Option 1: Single project (DEFAULT)
src/
├── models/
├── services/
├── cli/
└── lib/

tests/
├── contract/
├── integration/
└── unit/

# [REMOVE IF UNUSED] Option 2: Web application (when "frontend" + "backend" detected)
backend/
├── src/
│   ├── models/
│   ├── services/
│   └── api/
└── tests/

frontend/
├── src/
│   ├── components/
│   ├── pages/
│   └── services/
└── tests/

# [REMOVE IF UNUSED] Option 3: Mobile + API (when "iOS/Android" detected)
api/
└── [same as backend above]

ios/ or android/
└── [platform-specific structure: feature modules, UI flows, platform tests]
```

**Structure Decision**: [Document the selected structure and reference the real
directories captured above]

## Complexity Tracking

> **Fill ONLY if Constitution Check has violations that must be justified**

| Violation | Why Needed | Simpler Alternative Rejected Because |
|-----------|------------|-------------------------------------|
| [Constitution deviation] | [specific need and impact] | [simpler compliant alternative considered] |

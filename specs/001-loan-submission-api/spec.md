# Feature Specification: Loan Submission API

**Feature Branch**: `001-loan-submission-api` (branch not created; no pre-specify hook is configured)

**Created**: 2026-11-03

**Status**: Draft — request fields intentionally deferred

**Input**: User description: "Create a REST API for submitting a loan"

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Submit a loan application (Priority: P1)

An API consumer submits a loan application and receives confirmation that the submission was accepted and recorded. A successful response confirms receipt only; it is not an eligibility, underwriting, or approval decision.

**Why this priority**: Receiving a durable, unambiguous submission acknowledgement is the central value of this feature.

**Independent Test**: Submit one request that satisfies the agreed request contract, then verify the success response and that one corresponding non-deleted application is persisted with its required audit values.

**Acceptance Scenarios**:

1. **Given** an authenticated applicant using an enabled Google or Facebook identity and a request with all required fields and values valid under the agreed request contract, **When** the API accepts it, **Then** it returns `201 Created` with a stable application identifier and `submitted` state, and the application is durably persisted.
2. **Given** an accepted submission, **When** the caller receives its response, **Then** the response indicates receipt only and does not imply eligibility, underwriting, or approval.
3. **Given** persistence fails while processing a submission, **When** the API responds, **Then** it returns a client-safe server error and no partial application is visible.

### User Story 2 - Receive actionable validation errors (Priority: P2)

An API consumer receives a consistent, safe explanation when a submission cannot be accepted because its body is malformed or its fields violate the agreed contract.

**Why this priority**: Clear request errors let clients correct submissions without exposing internal service or database details.

**Independent Test**: Submit malformed JSON, omit a required field, and provide an invalid field value; verify each response is a documented 4xx response with the common error representation and that no application is persisted.

**Acceptance Scenarios**:

1. **Given** malformed JSON, **When** it is submitted, **Then** the API returns `400 Bad Request` with a machine-readable error and creates no application.
2. **Given** a well-formed body missing required data or containing values invalid under the agreed request contract, **When** it is submitted, **Then** the API returns `422 Unprocessable Content` with a machine-readable validation error and creates no application.
3. **Given** a request with an unsupported content type, **When** it is submitted, **Then** the API returns `415 Unsupported Media Type` and creates no application.
4. **Given** any error response, **When** a client inspects it, **Then** it contains no secret, SQL detail, stack trace, or other internal implementation data.

### Edge Cases

- The request body is empty, malformed, too large for the documented service limit, or uses an unsupported media type; it must be rejected before persistence with a documented 4xx response.
- A required field is absent, null, incorrectly typed, outside an agreed range, or uses an unsupported value; it must fail validation without creating a row. Field-specific rules remain deferred under FR-002.
- PostgreSQL is unavailable or a write fails; the API must not return success, and the submission must not be partially committed.
- An application row is soft-deleted by an authorized application operation; normal reads must exclude it. This submission feature does not add a read or delete endpoint.
- A request with missing or invalid Google/Facebook credentials must not be accepted; an authenticated identity without submission permission must not create a row.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: The API MUST provide `POST /loans` as the loan-submission operation, accept a JSON request body, and return a JSON response. This is a proposed additive endpoint; it MUST NOT change existing client contracts. The plan MUST confirm the project’s API-versioning convention before implementation because no existing API convention is evidenced in the repository.
- **FR-002**: The API MUST accept only the loan application fields approved for this feature. **[NEEDS CLARIFICATION: What exact fields, including any loan type and each field’s requiredness and validation rules, must the submission accept?]** Until resolved, the request schema and field-specific validation rules are not ready for implementation.
- **FR-003**: On successful validation and persistence, the API MUST return `201 Created`, identify the newly created application with a stable identifier, indicate the state `submitted`, and provide a `Location` header identifying the created resource. This state acknowledges receipt and MUST NOT communicate an eligibility, underwriting, or approval decision.
- **FR-004**: The API MUST return `400 Bad Request` for malformed JSON, `422 Unprocessable Content` for a well-formed request that violates agreed field rules, and `415 Unsupported Media Type` for unsupported request content types. It MUST return `503 Service Unavailable` when persistence is temporarily unavailable and `500 Internal Server Error` for other unexpected failures. Error responses MUST use a consistent machine-readable JSON shape containing a stable error code and safe message; validation errors MAY identify invalid field names but MUST NOT echo sensitive values or expose secrets, SQL details, stack traces, or internal implementation data.
- **FR-005**: The API MUST validate the request body at the boundary before using or persisting it. Invalid requests MUST NOT create or modify application records.
- **FR-006**: The API MUST authenticate applicants using a verified Google or Facebook identity; the deployment MUST select which of these providers to enable before implementation. Only authenticated applicant identities may submit. The API MUST derive `created_by` and `updated_by` from the provider-qualified subject of the verified identity, never from a caller-supplied identity field. It MUST return `401 Unauthorized` for missing or invalid credentials and `403 Forbidden` when an authenticated identity is not permitted to submit.
- **FR-007**: The API MUST persist each accepted application in PostgreSQL. Schema changes MUST be delivered as ordered, reviewed, reproducible migrations. Writes that must succeed or fail together MUST be atomic; a failure MUST NOT leave a partially committed submission. Queries MUST use parameterized values.
- **FR-008**: Every PostgreSQL table created or managed for the feature, including any future association table, MUST include `created_at`, `created_by`, `updated_at`, `updated_by`, boolean `deleted`, and `deleted_at`. At initial creation, the audit timestamps and actor values MUST describe the creation/update event, `deleted` MUST be false, and `deleted_at` MUST indicate that no deletion has occurred. The actor values MUST use the provider-qualified subject defined in FR-006.
- **FR-009**: Any operation that soft-deletes an application MUST set `deleted` to true and record the deletion time in `deleted_at`; normal reads MUST exclude soft-deleted rows unless the operation explicitly needs them. This feature does not add a deletion endpoint and MUST NOT hard-delete an application as part of submission.
- **FR-010**: The API MUST minimize collection and exposure of application data. Sensitive request values MUST NOT be written to routine logs or returned in errors. Database access MUST follow least privilege. Any more specific privacy, retention, or regulatory requirements must be confirmed before implementation if applicable.
- **FR-011**: The contract MUST document the success response, all error status codes, and their observable behavior. Since the repository contains no existing API implementation or client contract, the new endpoint is additive; any later breaking contract change requires an explicitly versioned and documented approach.
- **FR-012**: Automated verification MUST cover successful submission, malformed and invalid input, missing/invalid credentials, unauthorized identities, safe errors, persistence/audit values, soft-delete filtering, and migration and transaction failure behavior.
- **FR-013**: This feature MUST NOT make loan eligibility or underwriting decisions or call external integrations. No repository evidence identifies such decision rules or integrations; submission receipt is the boundary of this feature.

### Key Entities *(include if feature involves data)*

- **Loan application**: A submission received by the service and represented as a persisted application with a stable identifier and a `submitted` receipt state. Its exact business fields remain unresolved pending FR-002.
- **Audit metadata**: Creation/update actor and timestamps plus soft-deletion state required on every feature-managed PostgreSQL table. Actor values use the provider-qualified subject of the verified Google or Facebook identity.
- **API error**: A client-safe, machine-readable indication of a rejected request or failed operation, with a stable code and safe message.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: 100% of valid submissions in contract tests receive `201 Created`, a stable application identifier, the `submitted` receipt state, and a matching durable non-deleted record.
- **SC-002**: 100% of malformed or contract-invalid requests in validation tests receive the documented 4xx response and create no application record.
- **SC-003**: 100% of persisted submissions have complete creation/update actor and time metadata plus deletion metadata, and records marked deleted are absent from ordinary results.
- **SC-004**: 100% of tested failure responses use the documented machine-readable error shape and disclose no secret, sensitive input value, SQL detail, or stack trace.
- **SC-005**: At least 95% of valid submission attempts complete with a response in under 2 seconds at 50 concurrent callers under the agreed normal test conditions.

## Assumptions

- The repository constitution is authoritative: this is a Go REST API backed by PostgreSQL, with reviewed ordered migrations, request-boundary validation, safe errors, least privilege, tests at affected boundaries, and the specified audit and soft-delete rules.
- `POST /loans` is a proposed resource path because the request asks for a REST API to submit a loan and the repository contains no existing route convention. The plan must confirm versioning before implementation.
- A `201 Created` response with a stable identifier and `submitted` state means the request was validated and durably recorded only; it is not an eligibility or approval outcome.
- Loan-specific application fields remain intentionally deferred. Google or Facebook identity verification is required; the deployment must choose which provider or providers to enable. No particular loan type, eligibility or underwriting rule, or external integration is assumed.
- No current API source code, database schema, migration history, tests, or other project evidence exists in the repository beyond the Spec Kit configuration and constitution.
- Submission creates an application record; duplicate-submission detection or idempotency behavior is not specified in the user request and is not included in this feature unless separately decided.
- The request schema and field-specific validation requirements are intentionally deferred by the user; implementation and planning that depend on those fields must wait for an approved schema.
- The service applies its ordinary request-size limit and privacy safeguards; the precise size limit, data retention period, and any domain-specific privacy obligations require project policy or confirmation before implementation.

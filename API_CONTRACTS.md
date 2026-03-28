# Chaos Node API Contracts

## Purpose

This document defines the initial API contract direction for Chaos Node.

Its purpose is to create a stable interface between the frontend, backend services, orchestration engine, and future integrations. These contracts should remain clear, versioned, testable, and aligned with the architecture, data model, RBAC, governance, and testing standards of the project.

This is the first practical handshake layer between the React frontend and the Python backend.

## API Contract Principles

1. Keep contracts explicit and versioned.
2. Validate all request inputs.
3. Return structured and predictable responses.
4. Separate reasoning outputs from action authorization.
5. Preserve conflict and decision-layer visibility where permissions allow.
6. Enforce RBAC and governance checks at the API boundary.
7. Design contracts so they can evolve without breaking the system carelessly.

## Initial API Style

Recommended initial approach:

* REST-style JSON API
* versioned base path
* stateless request handling where practical
* explicit authentication and role-aware authorization
* structured response envelopes

Recommended base path:

`/api/v1`

## Common Response Envelope

All major endpoints should return a predictable envelope.

### Success response shape

```json
{
  "success": true,
  "data": {},
  "meta": {
    "request_id": "req_123",
    "timestamp": "2026-03-28T12:00:00Z"
  },
  "errors": []
}
```

### Error response shape

```json
{
  "success": false,
  "data": null,
  "meta": {
    "request_id": "req_123",
    "timestamp": "2026-03-28T12:00:00Z"
  },
  "errors": [
    {
      "code": "VALIDATION_ERROR",
      "message": "The submitted task_type is invalid.",
      "field": "task_type"
    }
  ]
}
```

## Authentication and Authorization

The API should assume authenticated access for protected routes.

The API layer should:

* authenticate the caller
* identify role context
* enforce permission checks before protected data is returned or actions are allowed
* prevent users from seeing conflicts, traces, or admin surfaces outside their scope

Initial auth-related behavior should be designed to support:

* user sessions
* admin sessions
* owner authority
* sysadmin support roles
* future service-to-service authentication

## Standard Metadata Expectations

Where relevant, responses should include metadata such as:

* `request_id`
* `timestamp`
* `session_id`
* `task_id`
* pagination details if needed later
* visibility limitations if fields are intentionally hidden by role

## Core Initial Endpoints

The first API surface should remain focused.

### 1. Create Session

Creates a new Chaos Node session.

**Endpoint**

`POST /api/v1/sessions`

**Request body**

```json
{
  "session_type": "interactive",
  "mode": "chaos",
  "metadata": {
    "source": "frontend"
  }
}
```

**Response data shape**

```json
{
  "session_id": "sess_001",
  "status": "initialized",
  "session_type": "interactive",
  "role_context": "user",
  "started_at": "2026-03-28T12:00:00Z"
}
```

### 2. Get Session

Returns session-level information for an authorized caller.

**Endpoint**

`GET /api/v1/sessions/{session_id}`

**Response data shape**

```json
{
  "session_id": "sess_001",
  "status": "active",
  "session_type": "interactive",
  "role_context": "user",
  "active_task_ids": ["task_001"],
  "participant_ids": ["part_001", "part_002"],
  "decision_layer_ids": ["dl_001"],
  "started_at": "2026-03-28T12:00:00Z",
  "ended_at": null
}
```

### 3. Create Task

Submits a new task into a session.

**Endpoint**

`POST /api/v1/sessions/{session_id}/tasks`

**Request body**

```json
{
  "objective": "Evaluate the user request and determine the strongest safe answer.",
  "task_type": "analysis",
  "requested_output_type": "answer",
  "input_payload": {
    "prompt": "What is the best approach to resolve this conflict?"
  },
  "priority": "normal",
  "risk_level": "medium"
}
```

**Response data shape**

```json
{
  "task_id": "task_001",
  "session_id": "sess_001",
  "status": "created",
  "task_type": "analysis",
  "requested_output_type": "answer",
  "created_at": "2026-03-28T12:01:00Z"
}
```

### 4. Get Task

Returns the current state of a task.

**Endpoint**

`GET /api/v1/tasks/{task_id}`

**Response data shape**

```json
{
  "task_id": "task_001",
  "session_id": "sess_001",
  "objective": "Evaluate the user request and determine the strongest safe answer.",
  "task_type": "analysis",
  "status": "in_discussion",
  "priority": "normal",
  "risk_level": "medium",
  "assigned_participant_ids": ["part_001", "part_002"]
}
```

### 5. Start Task Processing

Triggers orchestration for a created or queued task.

**Endpoint**

`POST /api/v1/tasks/{task_id}/start`

**Request body**

```json
{
  "participant_strategy": "default_boardroom",
  "max_discussion_rounds": 3
}
```

**Response data shape**

```json
{
  "task_id": "task_001",
  "status": "reasoning",
  "participant_ids": ["part_001", "part_002", "part_003"],
  "started_at": "2026-03-28T12:02:00Z"
}
```

### 6. List Participants for Task or Session

Returns the participating models or agents visible to the caller.

**Endpoint options**

`GET /api/v1/sessions/{session_id}/participants`

or

`GET /api/v1/tasks/{task_id}/participants`

**Response data shape**

```json
{
  "participants": [
    {
      "participant_id": "part_001",
      "participant_type": "llm",
      "display_name": "Reasoning Model A",
      "assigned_role": "primary_reasoner",
      "status": "active"
    },
    {
      "participant_id": "part_002",
      "participant_type": "llm",
      "display_name": "Reasoning Model B",
      "assigned_role": "critic",
      "status": "active"
    }
  ]
}
```

### 7. List Reasoning Artifacts

Returns reasoning artifacts visible to the caller.

**Endpoint**

`GET /api/v1/tasks/{task_id}/artifacts`

**Response data shape**

```json
{
  "artifacts": [
    {
      "artifact_id": "art_001",
      "participant_id": "part_001",
      "artifact_type": "proposal",
      "content": "Initial recommendation text",
      "confidence_level": "medium",
      "reasoning_stage": "initial"
    }
  ]
}
```

Note: artifact visibility may vary by role.

### 8. List Conflicts

Returns formal conflicts created during task processing.

**Endpoint**

`GET /api/v1/tasks/{task_id}/conflicts`

**Response data shape**

```json
{
  "conflicts": [
    {
      "conflict_id": "conf_001",
      "conflict_type": "reasoning_disagreement",
      "conflict_summary": "Participants disagree on the safest next step.",
      "severity": "high",
      "status": "under_review"
    }
  ]
}
```

### 9. List Decision Layers

Returns the decision layers currently attached to the task.

**Endpoint**

`GET /api/v1/tasks/{task_id}/decision-layers`

**Response data shape**

```json
{
  "decision_layers": [
    {
      "decision_layer_id": "dl_001",
      "layer_status": "active",
      "layer_summary": "Open safety and execution disagreements remain.",
      "conflict_ids": ["conf_001", "conf_002"]
    }
  ]
}
```

### 10. List Discussion Rounds

Returns the tracked discussion rounds for a task.

**Endpoint**

`GET /api/v1/tasks/{task_id}/discussion-rounds`

**Response data shape**

```json
{
  "discussion_rounds": [
    {
      "discussion_round_id": "dr_001",
      "round_number": 1,
      "objective": "Resolve active safety conflict.",
      "status": "completed",
      "convergence_assessment": "improved"
    }
  ]
}
```

### 11. Get Latest Synthesis Output

Returns the current best integrated output for a task.

**Endpoint**

`GET /api/v1/tasks/{task_id}/synthesis/latest`

**Response data shape**

```json
{
  "synthesis_output_id": "syn_001",
  "output_type": "answer",
  "output_content": "Recommended response content",
  "status": "candidate",
  "confidence_level": "medium",
  "unresolved_concerns": [
    "Execution risk remains under review."
  ]
}
```

### 12. Get Action Gate Result

Returns the latest action gate result for the task.

**Endpoint**

`GET /api/v1/tasks/{task_id}/action-gate/latest`

**Response data shape**

```json
{
  "action_gate_result_id": "agr_001",
  "gate_result": "deferred",
  "role_check_result": "passed",
  "permission_check_result": "passed",
  "conflict_state_summary": "High-severity conflict remains unresolved.",
  "readiness_score": 0.62,
  "decision_reason": "Additional discussion is required before action is allowed."
}
```

### 13. Get Audit Events

Returns audit events visible to the caller.

**Endpoint**

`GET /api/v1/sessions/{session_id}/audit-events`

**Response data shape**

```json
{
  "audit_events": [
    {
      "audit_event_id": "ae_001",
      "event_type": "conflict_created",
      "event_summary": "A high-severity disagreement was recorded.",
      "recorded_at": "2026-03-28T12:03:00Z"
    }
  ]
}
```

Audit visibility should be role-aware.

### 14. Get Role Context

Returns the caller's effective role and visible permission boundaries.

**Endpoint**

`GET /api/v1/me/role-context`

**Response data shape**

```json
{
  "role_name": "admin",
  "visibility_scope": "elevated",
  "approval_scope": "operational",
  "restricted_actions": [
    "modify_global_rules",
    "approve_production_release"
  ]
}
```

## Optional Early Admin Endpoints

These should be protected from general users and may be deferred until later.

Possible future endpoints:

* `GET /api/v1/admin/rules`
* `POST /api/v1/admin/rules/validate`
* `GET /api/v1/admin/rbac`
* `POST /api/v1/admin/tasks/{task_id}/re-evaluate`
* `POST /api/v1/admin/tasks/{task_id}/escalate`

These should be introduced only when their permission model is clear.

## Recommended Initial Object Shapes in API Responses

API responses should align with the data model, but should not automatically expose every internal field.

The API should:

* expose stable public-facing fields
* hide internal-only or sensitive fields unless role and endpoint purpose justify exposure
* allow role-based view shaping without changing the underlying contract carelessly

## Status and Error Behavior

The API should use standard HTTP behavior where appropriate.

Recommended baseline:

* `200 OK` for successful reads
* `201 Created` for new objects
* `400 Bad Request` for validation errors
* `401 Unauthorized` for unauthenticated access
* `403 Forbidden` for insufficient permissions
* `404 Not Found` where the object is unavailable or not visible
* `409 Conflict` for state conflicts or invalid transitions
* `422 Unprocessable Entity` for structurally valid but semantically invalid requests
* `500 Internal Server Error` only for unexpected server failures

## Validation Expectations

The API layer should validate:

* required fields
* enum values
* object status transitions where relevant
* role and permission requirements
* request size and shape
* whether protected actions are allowed in the current context

Invalid requests should fail clearly and predictably.

## Contract Stability Guidance

Initial contracts should remain intentionally narrow.

The first goal is not to expose every possible internal system detail. The first goal is to create stable, testable endpoints for the foundational flow.

Contracts should be expanded carefully as:

* the frontend grows
* orchestration layers mature
* role-aware views become more detailed
* admin and sysadmin functions are implemented

## Testing Expectations for API Contracts

All contract endpoints should be tested.

At minimum, API testing should include:

* request validation tests
* successful response tests
* unauthorized and forbidden access tests
* role-aware visibility tests
* state conflict tests
* error envelope tests
* contract shape tests

API contract changes should always include test updates.

## Initial Recommended Build Order for the API Layer

Build in this order:

1. session creation and retrieval
2. task creation and retrieval
3. task start and orchestration trigger
4. participant listing
5. conflict and decision layer retrieval
6. synthesis output retrieval
7. action gate result retrieval
8. audit event retrieval
9. role-context retrieval

This keeps the early API focused on the minimum end-to-end flow.

## Closing Direction

Chaos Node API contracts should stay boring in the best possible way.

They should be stable, readable, testable, governed, and predictable.

The intelligence system may be complex behind the scenes, but the API layer should make that complexity manageable, structured, and safe to build against.

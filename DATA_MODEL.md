# Chaos Node Data Model

## Purpose

This document defines the initial data model direction for Chaos Node.

Its purpose is to turn the core architectural objects into stable, implementation-ready contracts that can be used across orchestration, reasoning, conflict handling, synthesis, auditing, RBAC, and future persistence layers.

This is not intended to lock the project into a final database design yet. It is intended to establish the system nouns, their relationships, and the minimum structure needed to build consistently.

## Data Model Principles

1. Keep core objects stable early.
2. Preserve traceability across all major layers.
3. Separate reasoning artifacts from action decisions.
4. Treat conflicts and decision layers as first-class objects.
5. Make governance and permissions inspectable.
6. Prefer explicit relationships over hidden state.
7. Design objects so they can evolve into database schemas, API contracts, and typed models.

## Core Object Relationship Overview

At a high level, the system should work like this:

* a `Session` contains one or more `Task` objects
* a `Task` is worked by one or more `AgentParticipant` objects
* participants produce `ReasoningArtifact` objects
* artifacts, rules, or participant outcomes may create `Conflict` objects
* one or more conflicts may form a `DecisionLayer`
* one or more `DiscussionRound` objects may address active decision layers
* the system produces a `SynthesisOutput`
* the synthesis passes through an `ActionGateResult`
* all major steps may generate `AuditEvent` records
* permissions are enforced through `RolePermissionMapping`
* governance influence is traceable through `RuleReference`

## Object Definitions

### 1. Session

A Session is the top-level governed container for an interaction lifecycle.

#### Required fields

* `session_id`
* `initiated_by`
* `initiator_type`
* `role_context`
* `status`
* `started_at`

#### Recommended fields

* `ended_at`
* `session_type`
* `active_task_ids`
* `participant_ids`
* `decision_layer_ids`
* `synthesis_output_ids`
* `audit_event_ids`
* `metadata`

#### Notes

A session should make it possible to reconstruct the full flow of reasoning, conflict, synthesis, and gated output for a single engagement.

### 2. Task

A Task is the unit of work being analyzed, answered, recommended, or evaluated for action.

#### Required fields

* `task_id`
* `session_id`
* `objective`
* `task_type`
* `status`
* `created_at`

#### Recommended fields

* `priority`
* `risk_level`
* `requested_output_type`
* `input_payload`
* `required_rule_scope`
* `assigned_participant_ids`
* `conflict_ids`
* `decision_layer_ids`
* `metadata`

#### Notes

A task may represent a question, workflow step, requested recommendation, or action candidate.

### 3. AgentParticipant

An AgentParticipant is a governed intelligence contributor in the Chaos Node boardroom.

#### Required fields

* `participant_id`
* `session_id`
* `participant_type`
* `display_name`
* `assigned_role`
* `status`

#### Recommended fields

* `model_provider`
* `model_name`
* `capabilities`
* `tool_permissions`
* `reasoning_scope`
* `artifact_ids`
* `metadata`

#### Notes

This object should be used for both LLM participants and structured subagents, as long as their contribution role is defined.

### 4. ReasoningArtifact

A ReasoningArtifact is a traceable unit of thought contributed by a participant.

#### Required fields

* `artifact_id`
* `session_id`
* `task_id`
* `participant_id`
* `artifact_type`
* `content`
* `created_at`

#### Recommended fields

* `confidence_level`
* `reasoning_stage`
* `linked_rule_references`
* `linked_conflict_ids`
* `parent_artifact_id`
* `metadata`

#### Notes

Artifacts may include proposals, critiques, objections, tradeoff comparisons, missing-context flags, or risk observations.

### 5. Conflict

A Conflict is a governed record of disagreement, insufficiency, or material divergence.

#### Required fields

* `conflict_id`
* `session_id`
* `task_id`
* `conflict_type`
* `conflict_summary`
* `severity`
* `status`
* `created_at`

#### Recommended fields

* `source_artifact_ids`
* `source_participant_ids`
* `impacted_decision_layer_id`
* `resolution_requirements`
* `resolution_notes`
* `metadata`

#### Notes

Conflicts should be created when disagreement or insufficiency is meaningful enough that the system should not safely ignore it.

### 6. DecisionLayer

A DecisionLayer is a structured grouping of one or more related conflicts and the system context needed to resolve them.

#### Required fields

* `decision_layer_id`
* `session_id`
* `task_id`
* `layer_status`
* `created_at`

#### Recommended fields

* `conflict_ids`
* `layer_summary`
* `open_questions`
* `proposed_resolution_paths`
* `discussion_round_ids`
* `linked_synthesis_output_ids`
* `metadata`

#### Notes

Decision layers should preserve conflict context, not merely final verdicts.

### 7. DiscussionRound

A DiscussionRound is a tracked deliberation cycle focused on active decision layers.

#### Required fields

* `discussion_round_id`
* `session_id`
* `task_id`
* `round_number`
* `objective`
* `status`
* `started_at`

#### Recommended fields

* `ended_at`
* `participant_ids`
* `decision_layer_ids`
* `artifact_ids_generated`
* `convergence_assessment`
* `follow_up_required`
* `metadata`

#### Notes

A discussion round is a controlled collaborative review pass, not an unstructured conversation log.

### 8. SynthesisOutput

A SynthesisOutput is the best current integrated result produced after reasoning, conflict analysis, and discussion.

#### Required fields

* `synthesis_output_id`
* `session_id`
* `task_id`
* `output_type`
* `output_content`
* `status`
* `created_at`

#### Recommended fields

* `supporting_artifact_ids`
* `supporting_decision_layer_ids`
* `confidence_level`
* `unresolved_concerns`
* `recommended_next_step`
* `metadata`

#### Notes

A synthesis output should preserve rationale and unresolved concerns when they still matter.

### 9. ActionGateResult

An ActionGateResult is the formal result of evaluating whether the current synthesis may be finalized, deferred, escalated, blocked, or executed.

#### Required fields

* `action_gate_result_id`
* `session_id`
* `task_id`
* `synthesis_output_id`
* `gate_result`
* `evaluated_at`

#### Recommended fields

* `rule_reference_ids`
* `role_check_result`
* `permission_check_result`
* `conflict_state_summary`
* `readiness_score`
* `decision_reason`
* `metadata`

#### Notes

This object separates internal reasoning completion from actual output or action authorization.

### 10. RuleReference

A RuleReference is the traceable record of a rule or policy source that influenced system behavior.

#### Required fields

* `rule_reference_id`
* `source_document`
* `rule_key_or_label`
* `invoked_by_object_type`
* `invoked_by_object_id`
* `effect_type`

#### Recommended fields

* `effect_summary`
* `evaluation_stage`
* `metadata`

#### Notes

This object makes governance visible in the data model.

### 11. AuditEvent

An AuditEvent is a traceable record of a meaningful system event.

#### Required fields

* `audit_event_id`
* `session_id`
* `event_type`
* `event_summary`
* `recorded_at`

#### Recommended fields

* `task_id`
* `actor_type`
* `actor_id`
* `related_object_type`
* `related_object_id`
* `severity`
* `metadata`

#### Notes

Audit events should support review, debugging, governance inspection, and accountability.

### 12. RolePermissionMapping

A RolePermissionMapping is the structured contract that connects roles to allowed capabilities, restricted actions, and visibility boundaries.

#### Required fields

* `mapping_id`
* `role_name`
* `permission_set`
* `visibility_scope`
* `approval_scope`
* `status`

#### Recommended fields

* `restricted_actions`
* `tool_access_scope`
* `inherits_from`
* `environment_scope`
* `metadata`

#### Notes

This object is the operational bridge between RBAC policy and system enforcement.

## Relationship Direction

The following relationships should be treated as foundational:

* one `Session` to many `Task`
* one `Session` to many `AgentParticipant`
* one `Task` to many `ReasoningArtifact`
* many `ReasoningArtifact` to many `Conflict`
* one `Task` to many `Conflict`
* many `Conflict` to one `DecisionLayer` or many-to-many later if needed
* one `DecisionLayer` to many `DiscussionRound`
* one `Task` to many `SynthesisOutput`
* one `SynthesisOutput` to one or many `ActionGateResult` over time if reevaluated
* many objects to many `RuleReference`
* one `Session` to many `AuditEvent`

The model should begin simply, but it should not be designed so narrowly that later orchestration becomes painful.

## Status Fields

To keep behavior consistent, core objects should use explicit statuses.

### Suggested statuses

#### Session

* `initialized`
* `active`
* `waiting`
* `completed`
* `blocked`
* `failed`

#### Task

* `created`
* `queued`
* `reasoning`
* `in_conflict`
* `in_discussion`
* `in_synthesis`
* `awaiting_gate`
* `completed`
* `deferred`
* `blocked`
* `failed`

#### Conflict

* `open`
* `under_review`
* `partially_resolved`
* `resolved`
* `escalated`
* `closed`

#### DecisionLayer

* `open`
* `active`
* `stabilizing`
* `resolved`
* `blocked`

#### DiscussionRound

* `scheduled`
* `active`
* `paused`
* `completed`
* `closed`

#### SynthesisOutput

* `draft`
* `candidate`
* `accepted`
* `rejected`
* `superseded`

#### ActionGateResult

* `approved`
* `deferred`
* `blocked`
* `escalated`

## Minimum Implementation Guidance

The project should implement these objects first as typed backend models before deciding on final persistence strategy.

Recommended early implementation forms:

* Python typed models or schemas
* API request and response contracts where relevant
* validation rules for required fields
* test fixtures for each object type

The first implementation goal should be consistency, not premature database complexity.

## Testing Expectations for the Data Model

Every core object should have tests for:

* required field validation
* invalid state rejection where applicable
* relationship integrity assumptions
* status transition behavior where applicable
* serialization and deserialization if implemented

No core object contract should be introduced without tests.

## Change Control

Because these objects are intended to become stable contracts early, changes should be handled carefully.

Changes to core objects should:

* be documented clearly
* explain compatibility impact
* include test updates
* be reviewed carefully if they affect orchestration, governance, RBAC, rules, or action gating

## Closing Direction

The Chaos Node data model should help keep the system disciplined as it grows.

If the architecture defines the layers, the data model defines the language those layers use to work together.

That language should stay clear, governed, traceable, and stable.

# Chaos Node Architecture

## Purpose

This document defines the initial architecture direction for Chaos Node.

The goal is to build the system in a methodical, test-driven, and highly organized way so that each layer is understandable, reviewable, and extensible.

Chaos Node is intended to support multi-model reasoning, conflict preservation, layered decision analysis, rules-aware synthesis, and controlled action gating. The architecture should reflect that from the beginning.

## Architecture Principles

1. Build the smallest credible core first.
2. Keep reasoning, action, governance, and interface concerns separated.
3. Preserve conflicts rather than flattening them.
4. Make every layer inspectable.
5. Require tests for every meaningful build.
6. Reject changes that are not verifiable.
7. Prefer modular growth over tangled expansion.
8. Design for traceability from day one.

## Primary Technical Direction

Chaos Node should begin with:

* Python for the backend engine, orchestration, memory, rules evaluation, and decision logic
* a lightweight React frontend for user interaction, training workflows, and system visibility
* a backend API layer for frontend communication
* structured test suites for all core modules

This allows the project to keep the intelligence system in Python while exposing a clean and flexible interface layer for users and operators.

## Initial System Layers

### 1. Frontend Layer

The frontend should provide the first human interaction surface for Chaos Node.

Initial responsibilities:

* chat and input interface
* Chaos Mode interaction
* structured workflow input forms
* conflict and decision visibility views
* basic admin or operator controls later
* future `Train Chaos Node` workflows

Recommended stack:

* React
* lightweight component architecture
* simple state management at first

The frontend should not contain core reasoning logic.

### 2. API Layer

The API layer should sit between the frontend and the backend engine.

Responsibilities:

* receive requests from frontend clients
* validate request structure
* authenticate users and roles
* hand requests to orchestration services
* return outputs, conflict states, and layer traces where allowed
* expose safe operator and admin endpoints

This layer should remain thin and well controlled.

### 3. Orchestration Layer

This is the heart of Chaos Node.

Responsibilities:

* receive a task or prompt
* create a reasoning session
* assign participating models or agents
* coordinate layer-by-layer execution
* invoke reanalysis when conflicts appear
* move the task through discussion, synthesis, and action gating

This layer should not be monolithic. It should be broken into clear services or modules.

### 4. Model Adapter Layer

Chaos Node should not hardcode itself around one model provider.

Responsibilities:

* normalize access to multiple LLMs
* wrap provider-specific calls behind a common interface
* define input and output contracts for model participation
* support future local and remote model integrations

This layer should make multi-model participation consistent.

### 5. Reasoning Layer

This is where participating models or agents generate analysis.

Responsibilities:

* interpret the task
* propose answers, risks, objections, and alternatives
* identify missing information
* produce structured reasoning artifacts

This layer should remain as least restrictive as reasonably possible, subject to global rules and higher-priority controls.

### 6. Conflict Layer

If models disagree, Chaos Node should not discard the disagreement.

Responsibilities:

* detect material disagreement
* record conflict objects
* preserve what is disputed and why
* trigger reanalysis loops where needed

Conflict handling should be a first-class architectural feature, not an afterthought.

### 7. Decision Layer Stack

Every meaningful conflict should create or contribute to a decision layer.

Responsibilities:

* stack unresolved decision items
* preserve the history of contested reasoning
* expose these layers to downstream synthesis participants
* keep decisions separated until convergence is strong enough

This is one of the defining mechanics of Chaos Node.

### 8. Discussion and Convergence Layer

Once conflicts exist, the system should allow structured model discussion.

Responsibilities:

* revisit disputed points
* compare evidence and tradeoffs
* inspect assumptions and risks
* move toward a better supported conclusion

This layer should aim for strong convergence, not rushed agreement.

### 9. Synthesis Layer

After discussion, the system needs a synthesis stage.

Responsibilities:

* read outputs across models and decision layers
* compare conflict states
* identify where sufficient convergence exists
* generate a unified recommendation or final reasoning output

This layer should not erase unresolved risk when it still matters.

### 10. Action Gate Layer

The system must distinguish between reasoning and action.

Responsibilities:

* check rules, permissions, confidence, and risk posture
* block action where conflict or policy issues remain unresolved
* allow only approved output or execution states to proceed

No external action path should bypass this layer.

### 11. Rules and Governance Layer

All major layers should reference the same global governance backbone.

Responsibilities:

* evaluate global rules
* apply governance logic where required
* support policy-aware blocking, escalation, or deferral
* ensure every layer can reference common system expectations

This layer should point to `RULESETS.md`, governance logic, and future machine-readable policy structures.

### 12. Memory and Knowledge Layer

Chaos Node will need structured memory to support session continuity and learning direction.

Responsibilities:

* maintain session memory
* preserve reasoning artifacts where appropriate
* support future knowledge islands and workflow maps
* separate temporary session context from persistent governed memory

This layer must be carefully controlled and auditable.

### 13. Audit and Trace Layer

The system should preserve traceability for how decisions were formed.

Responsibilities:

* record participating models or agents
* record conflicts and decision layers
* record rule checks and action gate results
* support operator and reviewer inspection

This layer is critical for trust, debugging, and governance review.

## Initial Module Direction

A clean first-pass backend structure may look like this:

```text
backend/
├── api/
├── orchestration/
├── adapters/
├── reasoning/
├── conflicts/
├── decisions/
├── discussion/
├── synthesis/
├── action_gate/
├── rules/
├── memory/
├── audit/
├── auth/
├── rbac/
└── tests/
```

A clean first-pass frontend structure may look like this:

```text
frontend/
├── src/
│   ├── components/
│   ├── pages/
│   ├── features/
│   ├── services/
│   ├── hooks/
│   └── utils/
└── tests/
```

## Core Objects and Stable Contracts

The architecture should define a small set of core objects early so the system stays structured, auditable, and consistent across layers.

These objects should become stable contracts as early as possible.

### Session

A Session is the governed container for a single Chaos Node interaction lifecycle.

It should represent the full active context for a user-initiated or system-initiated engagement and may include:

* session identifier
* initiating user or system identity
* role and permission context
* active task or tasks
* participating models or agents
* accumulated reasoning artifacts
* conflicts raised during the session
* decision layers formed during the session
* discussion rounds
* synthesis outputs
* action gate checks and results
* audit references
* start time, end time, and current status

A Session should be the top-level wrapper that allows the system to trace how a request moved from input to output.

### Task

A Task is the specific unit of work the system is being asked to analyze, answer, decide, recommend, or potentially act upon.

A Task may be as simple as a question or as complex as a multi-step decision workflow.

It should define:

* the request or objective
* the intended outcome type, such as answer, recommendation, analysis, or action candidate
* the priority or importance of the work
* the risk or sensitivity level if known
* the required participants, tools, or rules where applicable
* the current processing state

A Task should be treated as the focal work object that moves through reasoning, conflict analysis, decision stacking, discussion, synthesis, and action gating.

### Agent or Model Participant

An Agent or Model Participant is an individual reasoning contributor inside the Chaos Node boardroom.

This may be a large language model, a specialized subagent, a critic model, a synthesis model, or another structured intelligence participant.

It should define:

* participant identifier
* participant type
* model or agent name
* assigned role in the session
* allowed capabilities
* permitted tools or interfaces
* current contribution state
* traceable outputs contributed to the session

A participant is not just a model endpoint. It is a governed contributor with a defined role in the collective reasoning process.

### Reasoning Artifact

A Reasoning Artifact is a structured output produced by a participant during analysis.

This may include:

* an answer proposal
* a critique
* a risk observation
* a tradeoff comparison
* a missing-context flag
* an alternate interpretation
* a confidence statement
* an escalation note

A Reasoning Artifact should preserve what a participant contributed in a way that later layers can inspect, compare, challenge, or reuse.

It should not be treated as final truth. It is a traceable piece of thought.

### Conflict

A Conflict is a formalized disagreement, insufficiency signal, or material divergence between participants, artifacts, rules, or conclusions.

A conflict should be created when the difference is meaningful enough that the system should not safely ignore it.

A Conflict should capture:

* what is disputed
* which participants or layers are in conflict
* the type of conflict
* the weight or severity of the disagreement
* the possible impact if unresolved
* what additional reasoning, evidence, or review may resolve it
* the current resolution state

A Conflict is not just disagreement. It is a governed signal that deeper analysis or review is needed before convergence or action.

### Decision Layer

A Decision Layer is the structured container created from one or more related conflicts so the system can examine them as part of a larger decision picture.

It should:

* stack and group conflicts where appropriate
* preserve the history and reasoning state of those disputes
* expose the open decision questions that still matter
* carry proposed paths toward resolution
* remain visible to later participants and synthesis stages

A Decision Layer helps Chaos Node avoid losing context across multiple disagreements. It allows the system to study conflicts together, understand their combined effect, and move toward better-supported resolution.

### Discussion Round

A Discussion Round is a structured deliberation pass in which selected participants revisit active decision layers, compare reasoning artifacts, challenge assumptions, and respond to open conflicts.

It is the formal mechanism that turns the boardroom concept into controlled collaborative analysis.

A Discussion Round should define:

* which participants are involved
* which decision layers or conflicts are under review
* the round objective
* the contributions made during the round
* whether convergence improved, stalled, or widened disagreement
* whether another round is needed

A Discussion Round is not casual conversation. It is a tracked deliberation cycle intended to improve the quality of convergence.

### Synthesis Output

A Synthesis Output is the structured result produced after reasoning artifacts, conflicts, decision layers, and discussion rounds have been evaluated together.

It should represent the system's best current integrated view.

A Synthesis Output should include:

* the proposed final answer, recommendation, or action candidate
* the supporting rationale
* important tradeoffs considered
* unresolved concerns, if any remain
* confidence or readiness indicators
* linked references to the major decision layers or artifacts that shaped it

A Synthesis Output is not merely a summary. It is the current best integrated decision product of the Chaos Node process.

### Action Gate Result

An Action Gate Result is the formal outcome of the layer that determines whether the system may safely finalize, recommend, defer, escalate, or execute the next step.

It should capture:

* the action or output being evaluated
* rule and policy checks applied
* role and permission checks applied
* conflict state at time of evaluation
* readiness or confidence judgment
* approval, block, defer, or escalate result
* explanation for the result

An Action Gate Result is the system's decision on whether thought may become output or action under the current conditions.

### Rule Reference

A Rule Reference is the traceable pointer to the rule, ruleset entry, policy clause, or governance condition that influenced a decision or blocked a path.

It should identify:

* the source rules document or policy source
* the specific rule or clause referenced
* the layer or object that invoked it
* the effect the rule had on reasoning, conflict handling, synthesis, or action gating

A Rule Reference makes governance inspectable inside the system rather than invisible.

### Audit Event

An Audit Event is a recorded system event that preserves traceability for meaningful actions, changes, decisions, or control outcomes.

It may capture:

* session creation
* task intake
* participant assignment
* conflict creation
* decision layer formation
* rule invocation
* role-based approval or denial
* action gate result
* deployment-relevant change
* configuration or rule modification

An Audit Event should support later review, debugging, governance inspection, and accountability.

### Role and Permission Mapping

A Role and Permission Mapping is the structured definition that connects roles to allowed capabilities, restricted actions, visible layers, approval rights, and controlled system surfaces.

It should define:

* the role name
* the permissions attached to that role
* restricted actions
* approval scope
* visibility scope
* tool or execution scope
* inheritance or relationship rules if applicable

This object is the bridge between RBAC policy and actual system behavior.

It ensures that roles are not just descriptive labels but enforceable access contracts.

## Methodical Build Order

Chaos Node should not try to build every layer at once.

### Phase 0: Repository foundation

Build first:

* repository structure
* governance files
* RBAC definitions
* contribution standards
* branch controls
* test policy

### Phase 1: Minimal orchestration core

Build next:

* Python backend project skeleton
* API service skeleton
* model adapter interface
* basic session object
* two-model reasoning flow
* first conflict object
* first decision layer object
* initial audit logging

### Phase 2: Conflict-aware orchestration

Build next:

* structured disagreement detection
* reanalysis trigger flow
* decision layer stacking
* basic synthesis routine
* action gate checks
* role-aware request handling

### Phase 3: Frontend interaction layer

Build next:

* lightweight React interface
* session chat view
* conflict visibility view
* result and trace view
* basic auth and role-aware UI behavior

### Phase 4: Rules and training workflows

Build next:

* rules evaluation service
* `Train Chaos Node` interaction path
* structured ingestion flows
* workflow and input tagging
* governed memory handling

### Phase 5: Hardening and scale direction

Build next:

* observability improvements
* improved role mapping
* queueing or async execution where needed
* evaluation tooling
* deployment safety controls
* broader model support

## Mandatory Testing Standard

Chaos Node should enforce a strict testing culture.

Every meaningful build must include tests or it should be rejected.

### Required testing rules

1. Every new module must include an associated test suite.
2. Every bug fix must include a test that proves the fix.
3. Every pull request must include a test run summary.
4. Code submitted without meaningful test coverage should be rejected.
5. Critical layers must receive stronger test depth than non-critical layers.
6. Governance, RBAC, rules, conflict handling, and action gating must be treated as critical layers.

### Test run expectation in pull requests

Each pull request should include:

* what was tested
* how it was tested
* what test commands were run
* whether tests passed
* any known gaps or limitations

### Test categories to establish early

Backend tests:

* unit tests for modules and services
* integration tests for orchestration flow
* contract tests for model adapters
* rule evaluation tests
* RBAC and permission tests
* conflict creation and decision layer tests
* action gate tests

Frontend tests:

* component tests
* flow tests for major user interactions
* permission-aware UI behavior tests
* service integration tests where applicable

System-level tests:

* end-to-end reasoning flow tests
* multi-model conflict tests
* safe-blocking tests for action gate conditions
* audit trace tests

## Quality Gates

The project should treat the following as quality gates for merge:

* tests included
* tests passing
* branch target is `develop`
* documentation updated where behavior changed
* no hidden bypass of governance, RBAC, or rules logic
* architecture remains modular and reviewable

## Pull Request Expectations

Architecture-related pull requests should be easy to review.

Recommended PR expectations:

* small enough to understand
* tied to one architectural step where possible
* includes related tests
* includes test run notes
* documents new contracts, objects, or interfaces if introduced

## Non-Negotiable Build Standards

1. No meaningful feature without tests.
2. No direct path around action gating.
3. No silent flattening of conflicts.
4. No uncontrolled growth of shared logic across unrelated modules.
5. No hidden rule bypasses.
6. No merge without review into `develop`.
7. No architecture decisions that make traceability impossible.

## Closing Direction

Chaos Node should be built like an intelligence system with standards, not a loose collection of experiments.

The architecture should grow in layers.
Each layer should be testable.
Each build should prove itself.
Each conflict should be preserved.
Each action should be gated.
Each contribution should leave the system stronger and more organized than before.

That is how Chaos Node should start taking shape.

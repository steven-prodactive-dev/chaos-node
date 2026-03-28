# Chaos Node Testing Standard

## Purpose

This document defines the baseline testing standard for Chaos Node.

The goal is to ensure that every meaningful build is verifiable, reviewable, and safe to integrate. Testing is not optional in this project. It is part of the build requirement.

If a contribution cannot show that it was tested appropriately, it should not be merged.

## Core Testing Principles

1. Every meaningful build must include tests.
2. Every bug fix must include a test that proves the issue is resolved.
3. Critical system areas require deeper testing than non-critical areas.
4. Testing must be documented in the pull request.
5. Untested changes should be rejected.
6. Tests should be clear enough that another contributor can understand what was verified.
7. Testing should support safety, traceability, and maintainability, not just code coverage metrics.

## Required Testing Rule

Every pull request must include:

* relevant automated tests
* test updates when behavior changes
* a written test run summary in the pull request
* clear note of any known gaps, limitations, or untested areas

If a change is meaningful and does not include tests, it should be rejected unless there is a clearly documented exception approved by maintainers.

## What Counts as a Meaningful Build

A meaningful build includes any change that affects:

* application behavior
* reasoning flow
* conflict handling
* decision layering
* synthesis logic
* action gating
* rules evaluation
* RBAC or permissions
* API contracts
* frontend behavior
* persistence logic
* audit or traceability behavior
* deployment behavior

Minor non-functional changes such as typo fixes or simple documentation-only edits may not require tests, but the distinction should be obvious.

## Critical Areas Requiring Stronger Testing

The following areas are considered critical and should receive stronger testing depth:

* governance and rules logic
* RBAC and permission enforcement
* conflict creation and conflict resolution flow
* decision layer stacking and handling
* synthesis behavior
* action gate behavior
* audit and trace logging
* authentication and protected endpoints
* model adapter contracts
* training ingestion logic

These areas should not rely on light or superficial tests.

## Test Categories

Chaos Node should use multiple forms of testing.

### 1. Unit Tests

Unit tests should verify the behavior of individual functions, classes, validators, and modules.

Use unit tests for:

* object validation
* state transitions
* helper logic
* rules evaluation logic
* permission checks
* conflict scoring or classification logic
* serialization and parsing behavior

### 2. Integration Tests

Integration tests should verify that multiple components work together correctly.

Use integration tests for:

* session and task flow
* orchestration across layers
* model adapter integration
* conflict creation from reasoning artifacts
* decision layer formation
* synthesis flow
* action gate evaluation
* API to backend behavior

### 3. Contract Tests

Contract tests should verify that stable interfaces remain correct.

Use contract tests for:

* API request and response shapes
* model adapter inputs and outputs
* typed object schemas
* frontend service contracts
* role and permission enforcement expectations

### 4. End-to-End Tests

End-to-end tests should verify major user and system flows from start to finish.

Use end-to-end tests for:

* user submits a prompt or task
* session is created
* participants are assigned
* reasoning artifacts are produced
* conflict is detected
* decision layer is formed
* synthesis is produced
* action gate returns a result
* user or operator sees the correct output

### 5. Regression Tests

Regression tests should ensure that previously fixed issues do not silently return.

Every meaningful bug fix should include a regression test when possible.

### 6. Permission and Safety Tests

Chaos Node should explicitly test that the system blocks what it is supposed to block.

Use these tests for:

* unauthorized action attempts
* role-based visibility restrictions
* rule-triggered deferrals or blocks
* protected branch or protected endpoint behavior where applicable
* action gate denial conditions

## Minimum Testing Expectations by Change Type

### Backend logic changes

Must include:

* unit tests
* integration tests when behavior crosses modules
* regression tests if fixing a bug

### Frontend behavior changes

Must include:

* component or interaction tests
* service or integration tests where applicable
* permission-aware UI tests if role behavior changes

### API changes

Must include:

* contract tests
* integration tests
* failure-path tests

### RBAC, governance, or rules changes

Must include:

* rule or permission tests
* failure-path tests
* regression tests if changing protected behavior
* documentation updates if behavior changes

### Conflict, decision, synthesis, or action gate changes

Must include:

* unit tests for local logic
* integration tests for orchestration effects
* scenario tests showing expected flow
* failure-path or blocked-path tests where appropriate

## Pull Request Testing Summary Requirement

Every pull request should include a testing section.

Recommended format:

```text
## Testing

What was tested:
-
-

How it was tested:
-
-

Commands run:
-
-

Result:
- Passed / Failed / Partial

Known gaps or notes:
-
```

This should not be skipped.

## Failure-Path Testing Requirement

Chaos Node should test both success paths and failure paths.

It is not enough to prove that a feature works when conditions are ideal. Contributors should also prove that the system behaves correctly when:

* permissions are insufficient
* rules block progress
* conflicts remain unresolved
* required fields are missing
* inputs are invalid
* adapters fail
* sessions or tasks enter blocked states
* action gates deny output or execution

Failure-path behavior is part of the product.

## Test Naming and Organization

Tests should be easy to locate and review.

Recommended expectations:

* place tests near their domain or in the defined test structure
* name tests according to behavior, not vague internal guesses
* keep test files organized by feature or layer
* avoid overly large test files that mix unrelated concerns

Example patterns:

* `test_session_models.py`
* `test_conflict_creation.py`
* `test_action_gate_rules.py`
* `test_rbac_permissions.py`
* `test_synthesis_flow.py`

## Test Data and Fixtures

Test data should be realistic enough to validate actual behavior.

Contributors should:

* use stable fixtures where possible
* avoid fragile or misleading test data
* create scenario inputs that reflect real system flows
* keep sensitive or unsafe real-world data out of test assets unless explicitly approved and handled correctly

## Coverage Position

Coverage matters, but raw percentage alone is not the standard.

Chaos Node should prioritize meaningful coverage over vanity metrics.

The project should care more about whether critical behaviors, failures, and protections are actually tested than whether a dashboard shows a high number.

That said, contributors should not use this principle as an excuse for weak testing.

## Rejection Conditions

A pull request should be rejected or returned for revision if:

* meaningful changes have no tests
* the testing summary is missing
* failure paths were ignored in a sensitive area
* behavior changed but test expectations were not updated
* the change weakens governance, RBAC, rules, or action gate behavior without strong justification and tests
* tests are clearly superficial and do not verify the real change

## Maintainer Review Expectations

Reviewers should evaluate:

* whether the right test types were included
* whether the test cases actually match the change
* whether failure paths were considered
* whether protected behavior remains protected
* whether the testing notes in the pull request are complete and credible

Passing tests do not automatically mean sufficient testing.

## Future Direction

As Chaos Node grows, the testing standard should expand to include:

* performance and load tests where relevant
* model evaluation benchmarks
* adversarial or misuse-path testing
* deployment verification checks
* environment-specific validation suites

The early standard should remain simple but serious.

## Final Standard

Chaos Node should be built with proof, not assumption.

Every meaningful build should be tested.
Every protected behavior should be verified.
Every pull request should show what was tested.
Every contributor should expect review against this standard.

If it was not tested appropriately, it is not ready.

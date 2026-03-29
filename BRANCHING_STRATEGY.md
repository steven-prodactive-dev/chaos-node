# Chaos Node Branching Strategy

## Purpose

This document defines the branching and promotion strategy for Chaos Node.

The goal is to keep development organized, reviewable, and controlled as the project grows. Chaos Node should not allow uncontrolled direct changes into major branches. All meaningful work should move through a disciplined path.

## Core Branching Position

Chaos Node should follow a controlled branch promotion model.

The expected path is:

1. contributors work in their own well-named branches
2. changes are reviewed and merged by pull request into `develop`
3. `develop` is the required staging branch for all accepted work
4. promotion from `develop` into `beta` is controlled
5. promotion from `beta` into `production` is controlled

No meaningful code change should bypass this sequence.

## Primary Branches

### `develop`

`develop` is the main integration branch for active accepted work.

It should be treated as:

* the required merge target for normal contribution work
* the primary review and integration branch
* the place where accepted features, fixes, and improvements accumulate before promotion

All normal contribution pull requests should target `develop`.

### `beta`

`beta` is the controlled pre-production branch.

It should be treated as:

* the branch for promoted work that is considered ready for broader validation
* a controlled branch for beta testing, structured review, and final stabilization work
* a branch that receives changes only from `develop` through approved promotion

Direct feature work should not target `beta` as the default development path.

### `production`

`production` is the controlled release branch.

It should be treated as:

* the branch representing approved production-ready state
* the final promoted branch after beta review and approval
* a highly protected branch with very limited direct change authority

Direct work should not be performed in `production` except under tightly controlled and documented emergency procedures if ever allowed at all.

## Working Branch Strategy

Contributors should create and work in their own clearly named branches.

These branches should be:

* focused on one clear area of work
* easy to understand from the branch name
* short-lived where practical
* submitted by pull request into `develop`

### Recommended branch naming patterns

* `feature/session-api-foundation`
* `feature/conflict-detection-service`
* `feature/frontend-chaos-mode-view`
* `fix/action-gate-status-bug`
* `fix/rbac-permission-check`
* `docs/api-contracts-update`
* `test/decision-layer-scenarios`
* `refactor/model-adapter-interface`

Branch names should describe the work clearly and professionally.

## Required Merge Path

The required path for accepted work should be:

`working branch -> develop -> beta -> production`

This means:

* all meaningful additions and changes must first be merged into `develop`
* nothing should be promoted to `beta` unless it already exists in `develop`
* nothing should be promoted to `production` unless it has already moved through `beta`

This path should remain consistent.

## Engineering Council Authority

Beta and Production promotion should be governed by the Chaos Node Open Source Engineering Council.

The Chaos Node Open Source Engineering Council should control:

* promotion from `develop` to `beta`
* promotion from `beta` to `production`
* review of important open-source additions before wider release
* final readiness decisions for public-facing branch advancement
* any exceptions to normal branch promotion rules

This helps ensure that broader-release changes receive higher scrutiny than ordinary contribution merges into `develop`.

## Pull Request Expectations

All normal contribution pull requests should:

* originate from a well-named working branch
* target `develop`
* include required tests
* include the required testing summary
* align with governance, RBAC, security, and architecture expectations
* remain reviewable in scope

Pull requests should not target `beta` or `production` unless explicitly part of a controlled release or council-managed promotion process.

## Protection Expectations by Branch

### `develop`

`develop` should be protected enough to require:

* pull request review
* passing required checks
* no direct push for normal contributors
* testing evidence in PRs

### `beta`

`beta` should be more tightly protected and should require:

* council-controlled promotion review
* passing required checks
* controlled merge authority
* release-focused validation

### `production`

`production` should be the most protected branch and should require:

* council approval
* passing required checks
* highly restricted merge authority
* clear release intent
* documented release promotion steps

## Hotfix Position

Chaos Node should avoid normal direct hotfix work into protected release branches unless the project later defines a very explicit emergency process.

The preferred default remains:

* fix in a working branch
* merge into `develop`
* promote through `beta`
* then promote into `production`

If a true emergency process is defined later, it should still require documentation, review, and back-merge discipline so branch history does not drift.

## Back-Merge Discipline

The project should avoid situations where `beta` or `production` contain changes that are missing from `develop`.

The system of record for accepted ongoing work should remain `develop`.

This means:

* no meaningful branch promotion should create long-term divergence from `develop`
* the project should preserve a clean promotion history
* release branches should reflect controlled advancement, not separate hidden development lines

## Open Source Addition Review Position

Because Chaos Node is intended to be long-lasting and widely adopted, open-source additions should receive disciplined review.

This means:

* not every accepted contribution is automatically ready for beta or production promotion
* broader branch promotion should reflect confidence in architecture, governance, security, and testing quality
* the Engineering Council may require edits, cleanup, or additional safeguards before promotion beyond `develop`

This is especially important for:

* governance changes
* ruleset changes
* RBAC changes
* action-gate logic
* execution-capable tools
* security-sensitive code
* API contract changes
* data model changes

## Suggested Promotion Criteria

### Promote from working branch to `develop`

Expected when:

* the change is reviewed
* required tests are included and passing
* documentation is updated if needed
* the change fits the documented architecture and standards

### Promote from `develop` to `beta`

Expected when:

* the relevant work has stabilized in `develop`
* tests are passing consistently
* the change is ready for broader validation
* the Engineering Council approves beta promotion

### Promote from `beta` to `production`

Expected when:

* beta validation is satisfactory
* critical issues are resolved
* release quality is acceptable
* the Engineering Council approves production promotion

## Non-Negotiable Branching Standards

1. All normal work starts in a well-named working branch.
2. All accepted work merges into `develop` first.
3. `beta` receives promoted work only after `develop` review.
4. `production` receives promoted work only after `beta` review.
5. Beta and Production promotion are controlled by the Chaos Node Open Source Engineering Council.
6. Protected branches should not accept uncontrolled direct pushes.
7. Testing and review requirements apply before merge or promotion.
8. Branch history should remain disciplined and understandable.

## Closing Direction

Chaos Node should be built through controlled promotion, not branch chaos.

The development flow should be simple and consistent:

work in a named branch, merge into `develop`, promote to `beta` when ready, and move to `production` only through controlled council review.

That is the branching strategy most aligned with the project's long-term goals.

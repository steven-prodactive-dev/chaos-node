# Chaos Node Repository Structure

## Purpose

This document defines the recommended repository structure for Chaos Node.

The goal is to create a repository layout that is organized, long-lasting, contributor-friendly, governance-aware, and capable of supporting adoption as the project grows.

Chaos Node is not a small single-purpose script. It is being designed as a governed multi-layer intelligence platform with a Python backend, a lightweight React frontend, stable contracts, strong testing expectations, and future room for extensions. The repository structure should reflect that from the beginning.

## What the Repository Structure Must Support

The repository should support:

* a Python backend for orchestration, reasoning, conflict handling, synthesis, RBAC, rules, action gating, and audit flows
* a React frontend for user interaction, Chaos Mode, visibility layers, and future training workflows
* stable contracts and shared definitions
* strong test organization across backend and frontend
* governance-first documentation
* secure and reviewable contributor workflows
* long-term extensibility without constant restructuring
* open-source adoption by making the project easy to understand

## Repository Structure Options Considered

### Option 1: Single flat repository

Example style:

```text
chaos-node/
├── app.py
├── server.py
├── frontend/
├── utils/
├── docs/
└── tests/
```

#### Pros

* simple at the very beginning
* fast to start for a solo prototype

#### Cons

* becomes messy quickly
* does not reflect the layered architecture
* hard to scale across backend, frontend, governance, and contracts
* encourages shared logic to spread into the wrong places
* becomes harder for contributors to understand over time

#### Conclusion

This is not the right long-term structure for Chaos Node.

### Option 2: Split multi-repo structure

Example style:

* `chaos-node-backend`
* `chaos-node-frontend`
* `chaos-node-docs`
* `chaos-node-sdk`

#### Pros

* clear separation between independent systems
* can work later if products become mature and independently released

#### Cons

* too fragmented for the current phase
* increases contributor friction early
* makes contract synchronization harder
* makes governance and cross-cutting changes more difficult to coordinate
* creates unnecessary overhead before the first working system is proven

#### Conclusion

This may become useful later for mature packages or external SDKs, but it is not the best primary structure for the current stage.

### Option 3: Structured monorepo

Example style:

```text
chaos-node/
├── backend/
├── frontend/
├── shared/
├── docs/
├── tests/
└── .github/
```

#### Pros

* best balance of structure and simplicity
* supports Python and React cleanly
* keeps docs, governance, and code together
* reduces fragmentation during the early growth phase
* easier for contributors to navigate
* supports shared contracts and future reusable modules
* works well with branch controls, tests, and CI

#### Cons

* requires discipline to keep boundaries clean
* can become large over time if structure is not maintained

#### Conclusion

This is the best starting structure for Chaos Node.

## Recommended Direction

Chaos Node should use a structured monorepo.

This approach best supports:

* long-term maintainability
* contributor onboarding
* open-source adoption
* strong documentation and governance visibility
* consistent testing standards
* clean backend and frontend separation
* shared contract evolution

The repository should feel like one system with clearly separated domains, not a pile of unrelated directories.

## Recommended Top-Level Structure

```text
chaos-node/
├── .github/
├── backend/
├── frontend/
├── shared/
├── docs/
├── scripts/
├── tests/
├── examples/
├── README.md
├── GOVERNANCE.md
├── RULESETS.md
├── RBAC.md
├── CONTRIBUTING.md
├── ARCHITECTURE.md
├── DATA_MODEL.md
├── TESTING.md
├── API_CONTRACTS.md
├── SECURITY.md
├── IMPLEMENTATION_PLAN.md
├── REPO_STRUCTURE.md
└── LICENSE
```

## Top-Level Directory Purpose

### `.github/`

This should contain the GitHub-native workflow and contribution controls.

Recommended contents:

* issue templates
* pull request template
* CODEOWNERS if adopted later
* CI workflow definitions
* branch protection support files where relevant

### `backend/`

This should contain the Python application and all backend intelligence and control layers.

Recommended purpose:

* orchestration engine
* reasoning modules
* model adapters
* conflict and decision handling
* synthesis services
* action gate services
* rules and governance evaluation helpers
* RBAC enforcement services
* audit and memory services
* backend API implementation

### `frontend/`

This should contain the React application.

Recommended purpose:

* session and task interaction views
* Chaos Mode interface
* conflict and decision visibility views where allowed
* training workflow surfaces later
* role-aware rendering
* frontend service integrations

### `shared/`

This should contain cross-layer definitions that should not be duplicated carelessly between backend and frontend.

Recommended purpose:

* shared API contract definitions
* enums and status references
* shared object schemas if a cross-language approach is adopted later
* sample payloads and contract examples
* future SDK-facing stable objects if needed

This directory should remain disciplined. It should contain shared contracts, not arbitrary mixed logic.

### `docs/`

This should contain supporting documentation that is not intended to live at the root.

Recommended purpose:

* deeper architecture notes
* contributor guides
* onboarding docs
* how-to documents
* design decisions
* future feature-specific docs

The root should contain the major governing and foundational docs. The `docs/` folder should contain supporting detail.

### `scripts/`

This should contain automation and utility scripts used by maintainers or CI.

Recommended purpose:

* local setup helpers
* validation scripts
* linting helpers
* test runner wrappers
* development tooling scripts

### `tests/`

This should support higher-level or cross-cutting tests that do not clearly belong only to backend or frontend.

Recommended purpose:

* end-to-end tests
* cross-service integration tests
* scenario tests
* contract verification suites

Backend-specific and frontend-specific tests may also live closer to their source code, but this top-level `tests/` directory is useful for system-wide validation.

### `examples/`

This should help adoption.

Recommended purpose:

* sample workflows
* sample requests and responses
* example integration usage
* contributor reference examples

Good examples are important for open-source growth.

## Recommended Backend Structure

```text
backend/
├── app/
│   ├── api/
│   ├── orchestration/
│   ├── adapters/
│   ├── reasoning/
│   ├── conflicts/
│   ├── decisions/
│   ├── discussion/
│   ├── synthesis/
│   ├── action_gate/
│   ├── rules/
│   ├── memory/
│   ├── audit/
│   ├── auth/
│   ├── rbac/
│   ├── models/
│   ├── services/
│   └── utils/
├── tests/
├── pyproject.toml
└── README.md
```

### Backend structure notes

* `app/models/` should contain the typed internal data models or schemas
* `app/services/` should contain reusable domain services that do not belong to only one narrow module
* `app/utils/` should remain small and should not become a dumping ground
* `backend/tests/` should contain backend-focused unit and integration tests

## Recommended Frontend Structure

```text
frontend/
├── src/
│   ├── components/
│   ├── pages/
│   ├── features/
│   ├── services/
│   ├── hooks/
│   ├── state/
│   ├── types/
│   └── utils/
├── tests/
├── package.json
└── README.md
```

### Frontend structure notes

* `features/` should group workflow-level frontend logic
* `services/` should contain API interaction logic
* `types/` should reflect API-facing frontend types, not uncontrolled duplicates of backend internals
* `state/` should remain lightweight early unless complexity proves otherwise
* `frontend/tests/` should focus on component, interaction, and frontend integration behavior

## Root Documentation Strategy

The repository root should remain intentional and strong.

Documents that define the project's foundation should remain highly visible at the root, including:

* `README.md`
* `GOVERNANCE.md`
* `RULESETS.md`
* `RBAC.md`
* `CONTRIBUTING.md`
* `ARCHITECTURE.md`
* `DATA_MODEL.md`
* `TESTING.md`
* `API_CONTRACTS.md`
* `SECURITY.md`
* `IMPLEMENTATION_PLAN.md`

This helps new contributors quickly understand that Chaos Node is a serious, structured, governance-aware project.

## Suggested Additional Files Later

As the project matures, the repository may also add:

* `CODE_OF_CONDUCT.md`
* `MILESTONES.md`
* `CHANGELOG.md`
* `BRANCHING_STRATEGY.md`
* `DECISIONS/` or `docs/adr/` for architecture decision records
* `SECURITY_REPORTING.md`

## Testing Layout Strategy

Testing should be layered the same way the system is layered.

Recommended approach:

* backend unit and integration tests live in `backend/tests/`
* frontend component and integration tests live in `frontend/tests/`
* cross-system and end-to-end tests live in `tests/`

This is better than forcing every test into one shared directory.

## Contract and Schema Strategy

One of the most important structural decisions is where contracts live.

Recommended approach:

* keep authoritative backend models in the backend
* keep API-facing contract examples and shared enums in `shared/`
* avoid creating uncontrolled duplicate schema definitions across backend and frontend
* when duplication is necessary, document which source is authoritative

This reduces drift.

## Contributor Adoption Considerations

For long-term adoption, the repository should be easy to read within a few minutes.

That means:

* top-level clarity
* clear domain boundaries
* examples available early
* docs visible and organized
* no unnecessary fragmentation across multiple repos too soon
* no vague folders with mixed concerns

A contributor should be able to understand where to look for:

* backend logic
* frontend logic
* contracts
* tests
* governance docs
* examples

without guessing.

## What to Avoid

The repository should avoid:

* flat top-level sprawl
* mixed backend and frontend logic in the same directories
* undocumented shared folders
* hidden governance files buried too deep
* giant utility folders with unrelated code
* early multi-repo fragmentation before the architecture is stable
* unclear testing locations

## Recommended First Real Repository Layout

If the team were setting up the codebase now, a strong starting layout would be:

```text
chaos-node/
├── .github/
│   ├── ISSUE_TEMPLATE/
│   ├── workflows/
│   └── PULL_REQUEST_TEMPLATE.md
├── backend/
│   ├── app/
│   │   ├── api/
│   │   ├── orchestration/
│   │   ├── adapters/
│   │   ├── reasoning/
│   │   ├── conflicts/
│   │   ├── decisions/
│   │   ├── discussion/
│   │   ├── synthesis/
│   │   ├── action_gate/
│   │   ├── rules/
│   │   ├── memory/
│   │   ├── audit/
│   │   ├── auth/
│   │   ├── rbac/
│   │   ├── models/
│   │   ├── services/
│   │   └── utils/
│   ├── tests/
│   ├── pyproject.toml
│   └── README.md
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   ├── pages/
│   │   ├── features/
│   │   ├── services/
│   │   ├── hooks/
│   │   ├── state/
│   │   ├── types/
│   │   └── utils/
│   ├── tests/
│   ├── package.json
│   └── README.md
├── shared/
│   ├── contracts/
│   ├── enums/
│   └── examples/
├── docs/
│   ├── onboarding/
│   ├── design/
│   ├── guides/
│   └── adr/
├── scripts/
├── tests/
│   ├── e2e/
│   ├── integration/
│   └── scenarios/
├── examples/
├── README.md
├── GOVERNANCE.md
├── RULESETS.md
├── RBAC.md
├── CONTRIBUTING.md
├── ARCHITECTURE.md
├── DATA_MODEL.md
├── TESTING.md
├── API_CONTRACTS.md
├── SECURITY.md
├── IMPLEMENTATION_PLAN.md
├── REPO_STRUCTURE.md
└── LICENSE
```

## Final Recommendation

Chaos Node should start as a structured monorepo.

That gives the project the best combination of:

* clarity
* extensibility
* contributor friendliness
* governance visibility
* testing organization
* long-term maintainability

If the project becomes large enough later, selected packages or SDKs can be split out deliberately.

For now, the strongest move is to keep the platform together and keep the boundaries clean.

That is the repository structure most aligned with Chaos Node's goals.

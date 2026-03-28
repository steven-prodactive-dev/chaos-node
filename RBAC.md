# Chaos Node Role-Based Access Control

## Purpose

This document defines the baseline role-based access control model for Chaos Node.

The goal is to keep permissions clear, auditable, and aligned with the system's governance posture. Roles should control what a person or system can view, configure, approve, execute, deploy, or govern.

Chaos Node should separate reasoning access, administrative access, system-level access, and governance authority.

## Core RBAC Principles

1. No role should receive more permission than required.
2. Viewing is not the same as changing.
3. Changing is not the same as approving.
4. Approving is not the same as deploying.
5. System-level access must remain tightly controlled.
6. Governance and rule changes should be especially restricted.
7. All sensitive actions should be auditable.

## Role Definitions

### User

The standard platform role for interacting with Chaos Node.

Typical permissions:

* use approved frontend interfaces
* submit prompts, inputs, workflows, and training-style interactions where allowed
* view their own sessions, outputs, and permitted history
* participate in allowed Chaos Mode experiences
* access only the tools and features explicitly granted to the role

Restrictions:

* cannot modify system rules
* cannot modify global configuration
* cannot approve deployments
* cannot manage roles or permissions
* cannot access restricted admin or sysadmin functions

### Super User

An elevated user role intended for trusted operational use beyond normal user access but below administrative authority.

Typical permissions:

* all User permissions
* access advanced platform features approved for elevated use
* run expanded workflow interactions within allowed boundaries
* view broader operational outputs or shared project areas where granted
* support testing, structured feedback, or controlled training workflows

Restrictions:

* cannot change global governance documents or rulesets
* cannot manage platform-wide role assignments by default
* cannot deploy system changes
* cannot access sysadmin-only functions unless separately granted

### Admin

A platform administration role responsible for operational administration inside approved boundaries.

Typical permissions:

* all Super User permissions
* manage approved user access and role assignments within delegated scope
* configure platform settings that are not reserved for owners or sysadmins
* review operational logs, conflicts, and decision layers where authorized
* manage approved workspace, project, or environment settings
* review and moderate allowed training or workflow areas

Restrictions:

* cannot unilaterally change core governance rules without approval policy
* cannot perform unrestricted system-level actions by default
* cannot bypass security, audit, or policy controls
* cannot perform owner-only or sysadmin-only changes unless separately assigned

### Owner

The highest business or project authority role for Chaos Node governance, direction, and ultimate approval.

Typical permissions:

* all Admin permissions
* approve major governance direction changes
* approve changes to global rules, governance structure, and protected configuration areas
* approve production release direction and critical project-level changes
* control ownership-level settings and repository authority where applicable
* define or delegate top-level operational authority

Restrictions:

* should not automatically perform low-level sysadmin functions unless explicitly assigned
* should remain subject to audit and governance expectations
* should not bypass protected technical controls without traceable authorization

### Sysadmin-User

A limited system administration role intended for technical personnel who need system visibility or narrowly scoped system interaction without full administrative control.

Typical permissions:

* observe system health, service state, runtime status, and operational diagnostics where authorized
* access limited technical tooling needed for support, debugging, or inspection
* review infrastructure-related information within assigned scope
* assist with controlled maintenance or support workflows

Restrictions:

* cannot approve governance changes
* cannot broadly administer user roles unless explicitly granted
* cannot make unrestricted production-impacting changes by default
* cannot act as a full sysadmin-admin without elevation or separate assignment

### Sysadmin-Admin

A high-trust technical operations role for managing infrastructure, runtime systems, deployment controls, and sensitive platform administration.

Typical permissions:

* all Sysadmin-User permissions
* manage infrastructure, services, runtime environments, secrets handling, and deployment pipelines within approved policy
* perform system maintenance, environment configuration, incident response, and service restoration
* manage technical controls required for secure operation
* support protected backend systems needed for platform continuity
* execute approved production and environment-level changes

Restrictions:

* should not change governance policy, ownership direction, or global rules without the required approval path
* should not use technical power to bypass governance controls without explicit emergency authority and audit record
* should remain subject to dual review or approval for critical protected changes where required

## Permission Strategy

Chaos Node should treat permissions in layers.

### Permission categories

Recommended categories include:

* interface access
* workflow execution
* conflict and decision layer visibility
* training and ingestion access
* configuration management
* user and role administration
* governance document modification
* ruleset modification
* deployment approval
* infrastructure administration
* audit and log access

Not every role should receive every category.

## Baseline Role Hierarchy

A simple initial hierarchy may be:

* User
* Super User
* Admin
* Owner

System roles should remain parallel technical roles:

* Sysadmin-User
* Sysadmin-Admin

This avoids confusing business authority with technical authority.

## Governance Notes on Role Separation

1. Owner is the highest governance and business-control role.
2. Sysadmin-Admin is the highest technical operations role.
3. Admin handles operational administration but should not automatically hold owner or sysadmin-admin authority.
4. Super User is an elevated use role, not an administrative governance role.
5. Sysadmin roles should be granted only where technical necessity exists.
6. Sensitive roles should be reviewed regularly.

## Protected Actions

The following actions should be treated as protected and require elevated review, restricted roles, or both:

* editing `RULESETS.md`
* editing `GOVERNANCE.md`
* changing RBAC mappings
* changing production deployment configuration
* modifying secrets or environment credentials
* enabling new execution-capable tools
* changing safety-critical parameters
* approving protected branch merges for critical system areas

## Branch and Change Control

For repository safety and review quality:

* contributors should submit changes to the `develop` branch
* protected or critical changes should require review before merge
* direct changes to protected branches should be restricted
* governance, RBAC, and ruleset changes should receive heightened review

## Audit Expectations

The system should log or otherwise preserve traceability for:

* role assignment changes
* protected configuration changes
* ruleset modifications
* governance file modifications
* deployment approvals and releases
* sensitive system actions

## Future Direction

This RBAC model is the initial structure.

As Chaos Node grows, permissions should evolve into machine-readable policies that can support custom role mapping, environment-specific controls, and finer-grained authorization.

The system should remain clear enough for humans to manage and structured enough for machines to enforce.

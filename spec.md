---
layout: spec
title: "EGAProtocol Specification (On-site)"
permalink: /spec/
description: >-
  EGAProtocol v0.1 specification in a dual-audience format: executive plain-language
  summaries plus full technical detail for each section.
toc:
  - id: section-0-abstract
    label: "Abstract"
    title: Protocol purpose
    summary: EGAP governs Engine-to-Agent communication with mandatory governance metadata.
  - id: section-1-introduction
    label: "§1"
    title: Introduction
    summary: Why regulated deployments need governance primitives, not only connectivity.
  - id: section-2-conformance-language
    label: "§2"
    title: Conformance language
    summary: RFC 2119 / RFC 8174 requirement words define normative strength.
  - id: section-3-terminology
    label: "§3"
    title: Terminology
    summary: Standard definitions for engine, agent, actions, sessions, and budgets.
  - id: section-4-architecture
    label: "§4"
    title: Architectural overview
    summary: User, Engine, Agent roles and end-to-end message flow.
  - id: section-5-transport-bindings
    label: "§5"
    title: Transport bindings
    summary: Canonical JSON-RPC/WebSocket and gRPC/HTTP2 mappings.
  - id: section-6-message-framing
    label: "§6"
    title: Message framing
    summary: Every message uses a mandatory governance envelope and IDs.
  - id: section-7-core-message-types
    label: "§7"
    title: Core message types
    summary: Dispatch, Result, Approval, Audit, Alert, Health, Cancel semantics.
  - id: section-8-governance-metadata
    label: "§8"
    title: Governance metadata
    summary: 5AG fields carried on every protocol message.
  - id: section-9-authentication-identity
    label: "§9"
    title: Authentication and identity
    summary: Session auth mechanisms and identity continuity requirements.
  - id: section-10-authorization-model
    label: "§10"
    title: Authorization model
    summary: Role tiers, permission classes, and enforcement behavior.
  - id: section-11-approval-flow
    label: "§11"
    title: Approval flow
    summary: Non-bypassable human approval for MODIFY and ADMIN actions.
  - id: section-12-audit-schema
    label: "§12"
    title: Audit event schema
    summary: Append-only, hash-chained, regulator-ready event records.
  - id: section-13-budget-enforcement
    label: "§13"
    title: Budget enforcement
    summary: Hard limits for iterations, tools, tokens, and wall-clock time.
  - id: section-14-error-model
    label: "§14"
    title: Error model
    summary: Structured, typed error codes with retry semantics.
  - id: section-15-session-lifecycle
    label: "§15"
    title: Session lifecycle
    summary: State machine from creation to termination and optional resumption.
  - id: section-16-security-considerations
    label: "§16"
    title: Security considerations
    summary: TLS, replay protection, token handling, signature verification.
  - id: section-17-versioning
    label: "§17"
    title: Versioning and compatibility
    summary: Major/minor compatibility rules and negotiation behavior.
  - id: section-18-relationship
    label: "§18"
    title: Relationship to other protocols
    summary: EGAP complements MCP, A2A, OpenTelemetry, OAuth/OIDC.
  - id: section-19-conformance
    label: "§19"
    title: Conformance criteria
    summary: Ten mandatory criteria define EGAP v0.1 conformance.
  - id: section-20-references
    label: "§20"
    title: References
    summary: Normative and informative standards and specs.
  - id: section-21-appendix-a
    label: "§21"
    title: Appendix A (JSON-RPC)
    summary: Illustrative request/response bindings for JSON-RPC transport.
  - id: section-22-appendix-b
    label: "§22"
    title: Appendix B (gRPC)
    summary: Illustrative proto service and envelope mapping.
  - id: section-23-appendix-c
    label: "§23"
    title: Appendix C (example flows)
    summary: Practical scenarios for read-only, approvals, and budget exhaustion.
---

## EGAProtocol Specification — v0.1

**Protocol:** EGAP — Engine Governed Agents Protocol  
**Status:** Working Draft (not yet ratified)  
**Date:** 2026-04-21  
**Primary source:** [SPEC.md on GitHub](https://github.com/egaprotocol/spec/blob/main/SPEC.md)

<section id="section-0-abstract" class="spec-section" markdown="1">
## Abstract
<div class="exec-callout">
  <p class="exec-callout-label">In plain terms — for executives</p>
  <p>EGAP is a governance contract for AI agent operations. It ensures every agent action can be tied to identity, permissions, approvals, audit evidence, and budget controls. For leadership, this means AI operations become reviewable, defensible, and manageable under enterprise and regulatory scrutiny.</p>
</div>

EGAP (Engine Governed Agents Protocol) is an open wire protocol for governed communication between an orchestration **Engine** and one or more **Agents**.  
It defines message formats, governance metadata, approval checkpoints, audit semantics, and budget enforcement required for production-grade deployments in regulated and sovereign environments.

EGAP is complementary to MCP and A2A:
- MCP: model-to-tool interaction
- A2A: peer-agent interaction
- EGAP: engine-to-agent governance

The acronym **EGAP** is permanent branding, with audience-specific expansions defined in `EXPANSIONS.md`.
</section>

<section id="section-1-introduction" class="spec-section" markdown="1">
## 1. Introduction
<div class="exec-callout">
  <p class="exec-callout-label">In plain terms — for executives</p>
  <p>This section defines why governance is a hard requirement for production AI. It frames EGAP as infrastructure for trust: identity proof, permission control, mandatory approvals for risky actions, immutable evidence, and spending limits on autonomous behavior.</p>
</div>

### 1.1 Motivation
Regulated deployments require more than tool access:
- Cryptographic identity for users and sessions
- Least-privilege authorization
- Human approvals for destructive actions
- Immutable audit trails
- Runtime budget enforcement
- Alerts on anomalous behavior

### 1.2 Design principles
- Governance by default (mandatory metadata on every message)
- Transport agnostic (JSON-RPC/WebSocket and gRPC/HTTP2 in v0.1)
- Schema-first validation before dispatch
- Sovereignty-compatible (air-gapped capable)
- Composable with MCP, A2A, OpenTelemetry

### 1.3 Scope
Defined: wire format, governance metadata, approvals, audit, budgets, errors, sessions, conformance.  
Not defined: internal agent reasoning, LLM provider choice, MCP semantics, peer-agent protocol semantics, storage retention policy, UI design.
</section>

<section id="section-2-conformance-language" class="spec-section" markdown="1">
## 2. Conformance Language
<div class="exec-callout">
  <p class="exec-callout-label">In plain terms — for executives</p>
  <p>This section clarifies legal-grade wording. When EGAP says MUST, it is mandatory for compliance; SHOULD means strong recommendation; MAY is optional. This enables clear conformance testing and procurement decisions.</p>
</div>

Normative keywords **MUST**, **MUST NOT**, **SHALL**, **SHOULD**, **MAY**, and related terms follow RFC 2119 and RFC 8174 interpretation.
</section>

<section id="section-3-terminology" class="spec-section" markdown="1">
## 3. Terminology
<div class="exec-callout">
  <p class="exec-callout-label">In plain terms — for executives</p>
  <p>Shared vocabulary prevents ambiguity between engineering, risk, and governance teams. These terms form the common language used across contracts, controls, and audits.</p>
</div>

Key definitions:
- **Engine**: orchestration component enforcing governance
- **Agent**: execution component acting on Engine instructions
- **Action**: single dispatched work unit with schema, class, and budget
- **Session**: bounded interaction for one user context
- **Permission Class**: `READ`, `WRITE`, `MODIFY`, `ADMIN`
- **Approval Gate**: human authorization checkpoint
- **Audit Event**: immutable governance record
- **Budget**: bounded execution resources
- **Binding**: concrete transport mapping
</section>

<section id="section-4-architecture" class="spec-section" markdown="1">
## 4. Architectural Overview
<div class="exec-callout">
  <p class="exec-callout-label">In plain terms — for executives</p>
  <p>EGAP separates accountability from execution. Users authorize, the engine enforces policy, and agents execute within controls. This architecture allows centralized governance even when many heterogeneous agents are deployed.</p>
</div>

### 4.1 Roles
User authenticates and approves; Engine dispatches and governs; Agent executes actions.

### 4.2 Typical flow
1. Session authenticated
2. Action dispatched with governance metadata
3. `MODIFY`/`ADMIN` actions pause for approval
4. Agent executes under budget
5. Result or error returned
6. Audit events emitted for state transitions
7. Alerts emitted on configured conditions

### 4.3 Why two bindings
- JSON-RPC/WebSocket for DX and browser-friendly integrations
- gRPC/HTTP2 for typed, high-throughput server deployments
- Message semantics must remain equivalent across bindings
</section>

<section id="section-5-transport-bindings" class="spec-section" markdown="1">
## 5. Transport Bindings
<div class="exec-callout">
  <p class="exec-callout-label">In plain terms — for executives</p>
  <p>This section ensures EGAP can be adopted in both developer-centric and enterprise-scale runtime environments without changing governance behavior.</p>
</div>

### 5.1 JSON-RPC 2.0 over WebSocket
- TLS 1.3 required
- Default endpoint `/egaprotocol/v1`
- Subprotocol `egaprotocol.v1`
- Ping every 30s during idle; terminate after 90s missing pong

### 5.2 gRPC over HTTP/2
- TLS 1.3 required
- Package `egaprotocol.v1`
- Service `EGAProtocolService`
- Bidirectional streaming for governance-carrying methods
- Keepalive max idle 60s

### 5.3 Binding equivalence

| Abstract Message | JSON-RPC | gRPC |
|---|---|---|
| Dispatch | `ega.dispatch` | `Dispatch` stream |
| Result | `ega.result` notification | response stream |
| ApprovalRequest | `ega.approval.request` notification | `Approval` stream |
| ApprovalResponse | `ega.approval.response` | `Approval` stream |
| AuditEvent | `ega.audit` notification | `AuditStream` |
| Alert | `ega.alert` notification | `AlertStream` |
| HealthCheck | `ega.health` | `HealthCheck` |
| Cancel | `ega.cancel` | `Cancel` |
</section>

<section id="section-6-message-framing" class="spec-section" markdown="1">
## 6. Message Framing
<div class="exec-callout">
  <p class="exec-callout-label">In plain terms — for executives</p>
  <p>Every message carries enough context to be traceable and auditable. Missing required envelope fields means the message is invalid by design.</p>
</div>

All messages require a common envelope:
- `protocol_version` (`"ega/0.1"` for this release)
- `message_id` (UUIDv7)
- `correlation_id` (UUIDv7 across related lifecycle events)
- `timestamp` (RFC 3339 UTC, microseconds)
- `message_type`
- `governance_metadata` (mandatory)
- `payload`

Implementations MUST reject messages missing any required envelope field.
</section>

<section id="section-7-core-message-types" class="spec-section" markdown="1">
## 7. Core Message Types
<div class="exec-callout">
  <p class="exec-callout-label">In plain terms — for executives</p>
  <p>This section defines the official language of operation. By standardizing these message types, organizations can verify who requested what, who approved what, what happened, and why.</p>
</div>

### 7.1 Dispatch
Engine sends actionable work:
- `action_id`, `action_version`, `parameters`
- `permission_class`
- `budget`
- optional `time_range`, `parent_action_id`

Engine MUST validate schema before dispatch.

### 7.2 Result
Agent returns outcome:
- `status`: `SUCCESS`, `PARTIAL`, `FAILED`, `CANCELLED`, `TIMEOUT`
- `output`, optional `confidence`, optional `evidence`
- `budget_consumed`

### 7.3 ApprovalRequest
Contains action, blast radius, expiration, and required approver role for risky operations.

### 7.4 ApprovalResponse
Contains decision (`APPROVED`/`REJECTED`/`DEFERRED`), approver identity, reason, and signature.

### 7.5 AuditEvent
Immutable governance events such as:
`SESSION_STARTED`, `ACTION_DISPATCHED`, `APPROVAL_GRANTED`, `ERROR_RAISED`, `BUDGET_EXCEEDED`.

### 7.6 Alert
Operational alert with severity (`INFO` to `CRITICAL`) and category (for example `BUDGET_EXHAUSTED`, `HALLUCINATION_DETECTED`).

### 7.7 HealthCheck
Liveness exchange using nonce echo and health status.

### 7.8 Cancel
Engine abort command with explicit reason.  
Agent MUST return `CANCELLED` result within 5 seconds.
</section>

<section id="section-8-governance-metadata" class="spec-section" markdown="1">
## 8. Governance Metadata
<div class="exec-callout">
  <p class="exec-callout-label">In plain terms — for executives</p>
  <p>This is the core control payload of EGAP. It carries the five governance pillars in every message, making policy enforcement automatic rather than optional.</p>
</div>

Every message carries governance metadata covering 5AG:
- **Authentication:** `session_token`, `user_identity`, `agent_identity`
- **Authorization:** `role`, `entitlements`, `permission_class`
- **Audit:** `correlation_id`, `trace_id`, `span_id`
- **Approvals:** `approval_state`, optional `approval_evidence`
- **Alerts:** `alert_channels`

Key requirements:
- Session token must be revocable by Engine
- Authorization checks use session role (not max role)
- Correlation and trace fields must support end-to-end observability
</section>

<section id="section-9-authentication-identity" class="spec-section" markdown="1">
## 9. Authentication and Identity
<div class="exec-callout">
  <p class="exec-callout-label">In plain terms — for executives</p>
  <p>This section is about provable accountability. Every action is tied to a verified user and a verified agent instance, reducing impersonation and repudiation risk.</p>
</div>

### 9.1 Session authentication mechanisms
Engine MUST support at least one:
- PASETO v4 tokens (recommended)
- JWT (with mandatory `exp`, `iat`, `sub`, `aud`)
- mutual TLS client certificates

### 9.2 Identity records
`UserIdentity`: subject ID, display name, organization, roles, public key  
`AgentIdentity`: agent ID, version, signer attestation, declared capabilities

### 9.3 Identity continuity
`user_identity.subject_id` MUST remain constant throughout a session.
</section>

<section id="section-10-authorization-model" class="spec-section" markdown="1">
## 10. Authorization Model
<div class="exec-callout">
  <p class="exec-callout-label">In plain terms — for executives</p>
  <p>This section defines who is allowed to do what. It creates predictable role boundaries and makes privilege escalation auditable and controllable.</p>
</div>

### 10.1 Permission classes
- `READ`: retrieve state only
- `WRITE`: create state
- `MODIFY`: mutate existing state (approval required)
- `ADMIN`: security/configuration critical actions (approval required)

### 10.2 Role tiers
- `L1_OPERATOR`: READ, WRITE
- `L2_ENGINEER`: READ, WRITE, MODIFY (approval required for MODIFY)
- `L3_ADMIN`: all classes; ADMIN requires second L3 approval

### 10.3 Enforcement
Engine MUST reject unauthorized dispatch and emit `ERROR_RAISED` with `AUTHORIZATION_DENIED`.
</section>

<section id="section-11-approval-flow" class="spec-section" markdown="1">
## 11. Approval Flow
<div class="exec-callout">
  <p class="exec-callout-label">In plain terms — for executives</p>
  <p>This is EGAP's safety brake. Any action that can change or damage real systems stops for explicit human authorization, and that authorization must be cryptographically verifiable.</p>
</div>

### 11.1 Gate activation
For `MODIFY` and `ADMIN`, Engine MUST:
1. Suspend execution
2. Emit `ApprovalRequest`
3. Emit audit `APPROVAL_REQUESTED`
4. Wait for response or timeout

### 11.2 Response handling
- `APPROVED`: resume action, audit `APPROVAL_GRANTED`
- `REJECTED`: cancel action, audit `APPROVAL_REJECTED`
- `DEFERRED`: suspend session for later resumption
- Timeout: treat as rejected

### 11.3 Approver authentication
`ApprovalResponse.signature` MUST be verified against approver public key.

### 11.4 Non-bypassability
Executing `MODIFY`/`ADMIN` without valid approval is non-conformant.
</section>

<section id="section-12-audit-schema" class="spec-section" markdown="1">
## 12. Audit Event Schema
<div class="exec-callout">
  <p class="exec-callout-label">In plain terms — for executives</p>
  <p>This section delivers audit-grade evidence. It ensures records are append-only, cryptographically chained, and suitable for long-term assurance and external review.</p>
</div>

### 12.1 Immutability
Audit events MUST be append-only. Mutation, deletion, or reordering is forbidden.

### 12.2 Hash chain
Each event stores `prior_event_hash`; first event uses 32 zero bytes.  
This creates per-session tamper-evident chains.

### 12.3 OpenTelemetry compatibility
Audit events MAY be mirrored to OpenTelemetry logs.

### 12.4 Retention
EGAP does not mandate duration; regulated deployments SHOULD retain according to applicable law.
</section>

<section id="section-13-budget-enforcement" class="spec-section" markdown="1">
## 13. Budget Enforcement
<div class="exec-callout">
  <p class="exec-callout-label">In plain terms — for executives</p>
  <p>Budgets are hard limits for autonomous execution. This protects cost, latency, and operational blast radius by bounding agent behavior in real time.</p>
</div>

Every dispatch includes:

```text
budget {
  max_iterations
  max_tool_calls
  max_tokens
  max_wall_clock_ms
}
```

When exhausted:
- Agent halts execution
- Returns `TIMEOUT` (wall clock) or `FAILED` (other dimensions)
- Engine emits `BUDGET_EXHAUSTED` alert
- Engine emits `BUDGET_EXCEEDED` audit event

Engine MUST independently enforce wall-clock deadline via `Cancel`, even without agent cooperation.
</section>

<section id="section-14-error-model" class="spec-section" markdown="1">
## 14. Error Model
<div class="exec-callout">
  <p class="exec-callout-label">In plain terms — for executives</p>
  <p>Standardized errors make incidents diagnosable and measurable across teams and vendors. This eliminates ambiguous free-text failures in governance-critical paths.</p>
</div>

Errors MUST be structured objects:
- `code`
- `message`
- `retryable`
- `correlation_id`
- optional `details`

Normative code set includes:
- `AUTH_REQUIRED`, `AUTH_EXPIRED`
- `AUTHORIZATION_DENIED`
- `APPROVAL_REQUIRED`, `APPROVAL_REJECTED`, `APPROVAL_TIMEOUT`
- `SCHEMA_INVALID`, `ACTION_UNKNOWN`
- `BUDGET_EXHAUSTED`, `TOOL_HALLUCINATED`
- `INTERNAL_AGENT_ERROR`, `ENGINE_UNAVAILABLE`, `RATE_LIMITED`

Every error MUST emit audit event `ERROR_RAISED`.
</section>

<section id="section-15-session-lifecycle" class="spec-section" markdown="1">
## 15. Session Lifecycle
<div class="exec-callout">
  <p class="exec-callout-label">In plain terms — for executives</p>
  <p>Session state management ensures governance continuity over long-running workflows, interruptions, and resumptions. It prevents hidden state transitions and untracked restart behavior.</p>
</div>

State progression:

```text
CREATED -> AUTHENTICATED -> ACTIVE -> SUSPENDED -> ACTIVE -> TERMINATED
                               \
                                -> APPROVAL_PENDING -> ACTIVE
```

Rules:
- Session start MUST emit `SESSION_STARTED`
- Session end MUST emit `SESSION_ENDED`
- Resumption MAY be supported, requiring valid token and persisted checkpoint
- Resumption should emit `SESSION_RESUMED` linked to original correlation
</section>

<section id="section-16-security-considerations" class="spec-section" markdown="1">
## 16. Security Considerations
<div class="exec-callout">
  <p class="exec-callout-label">In plain terms — for executives</p>
  <p>This section translates governance into concrete security controls: encrypted transport, replay protection, redaction, signature verification, and air-gap compatibility.</p>
</div>

Key requirements:
- TLS 1.3+ only (reject TLS 1.2 and older)
- Session tokens MUST be redacted in logs/audit storage
- Replay protection via monotonic UUIDv7 message ordering tolerance
- Undeclared tool/action usage MUST return `TOOL_HALLUCINATED` and alert
- Unverified approval signatures MUST be treated as rejected
- Air-gapped operation must remain fully supported
</section>

<section id="section-17-versioning" class="spec-section" markdown="1">
## 17. Versioning and Compatibility
<div class="exec-callout">
  <p class="exec-callout-label">In plain terms — for executives</p>
  <p>Versioning controls upgrade risk. It allows safe incremental evolution while making breaking changes explicit and governable.</p>
</div>

- Version format: `ega/<major>.<minor>`
- Minor updates may add fields/types; implementations SHOULD ignore unknown additions
- Major updates may break compatibility
- v0.x is pre-stable; breaking changes are possible before v1.0
- `HealthCheck` supports version negotiation to highest mutual version
</section>

<section id="section-18-relationship" class="spec-section" markdown="1">
## 18. Relationship to Other Protocols
<div class="exec-callout">
  <p class="exec-callout-label">In plain terms — for executives</p>
  <p>EGAP is designed to fit into existing AI stacks rather than replace them. It supplies governance where adjacent protocols intentionally focus on capability and interoperability.</p>
</div>

- **MCP:** tool access by LLM-backed agents
- **A2A:** peer-agent communications
- **OpenTelemetry:** trace/log interoperability via `trace_id` and `span_id`
- **OAuth/OIDC:** acceptable bearer mechanisms when integrity requirements are met
</section>

<section id="section-19-conformance" class="spec-section" markdown="1">
## 19. Conformance Criteria
<div class="exec-callout">
  <p class="exec-callout-label">In plain terms — for executives</p>
  <p>This section defines pass/fail conditions for claiming EGAP compliance. It supports objective vendor evaluation and internal platform certification.</p>
</div>

Conformance requires all of:
- **C1** Transport binding implementation (at least one canonical binding)
- **C2** Core message handling correctness
- **C3** Governance metadata on every message
- **C4** Session authentication enforcement
- **C5** Authorization model enforcement
- **C6** Non-bypassable approval gates
- **C7** Audit emission with hash chain
- **C8** Agent + engine budget enforcement
- **C9** Structured error model adherence
- **C10** TLS 1.3 enforcement

Conformance suite development is tracked in `egaprotocol/conformance`.
</section>

<section id="section-20-references" class="spec-section" markdown="1">
## 20. References
<div class="exec-callout">
  <p class="exec-callout-label">In plain terms — for executives</p>
  <p>EGAP is anchored in established internet and security standards, increasing confidence for legal, audit, and architecture review.</p>
</div>

Normative references include RFC 2119, RFC 8174, RFC 3339, RFC 6455, RFC 7540, RFC 7519, RFC 8446, RFC 9562, JSON-RPC 2.0, gRPC spec, and W3C Trace Context.

Informative references include MCP, OpenTelemetry, Sigstore, and PASETO.
</section>

<section id="section-21-appendix-a" class="spec-section" markdown="1">
## 21. Appendix A — JSON-RPC Binding Reference
<div class="exec-callout">
  <p class="exec-callout-label">In plain terms — for executives</p>
  <p>This appendix gives implementers a concrete JSON-RPC shape to accelerate integration while preserving the normative semantics described earlier.</p>
</div>

Non-normative illustrative shape:

```json
{
  "jsonrpc": "2.0",
  "id": "01933e5f-7c00-7a3e-b2e1-5a9c6f8b0a1c",
  "method": "ega.dispatch",
  "params": {
    "envelope": {
      "protocol_version": "ega/0.1",
      "message_id": "01933e5f-7c00-7a3e-b2e1-5a9c6f8b0a1c",
      "correlation_id": "01933e5f-7bf0-7a3e-b2e1-5a9c6f8b0a1c",
      "timestamp": "2026-04-21T10:15:30.123456Z",
      "governance_metadata": { "...": "..." }
    },
    "action_id": "rca.analyze_5why",
    "permission_class": "READ",
    "budget": {
      "max_iterations": 12,
      "max_tool_calls": 40,
      "max_tokens": 80000,
      "max_wall_clock_ms": 300000
    }
  }
}
```
</section>

<section id="section-22-appendix-b" class="spec-section" markdown="1">
## 22. Appendix B — gRPC Binding Reference
<div class="exec-callout">
  <p class="exec-callout-label">In plain terms — for executives</p>
  <p>This appendix gives strongly typed examples for production platform teams running high-throughput internal service fabrics.</p>
</div>

Non-normative illustrative shape:

```protobuf
syntax = "proto3";
package egaprotocol.v1;

service EGAProtocolService {
  rpc Dispatch(stream DispatchMessage) returns (stream ResultMessage);
  rpc Approval(stream ApprovalMessage) returns (stream ApprovalMessage);
  rpc AuditStream(AuditRequest) returns (stream AuditEvent);
  rpc AlertStream(AlertRequest) returns (stream Alert);
  rpc HealthCheck(HealthRequest) returns (HealthResponse);
  rpc Cancel(CancelRequest) returns (CancelResponse);
}

message Envelope {
  string protocol_version = 1;
  string message_id = 2;
  string correlation_id = 3;
  string timestamp = 4;
  GovernanceMetadata governance_metadata = 5;
}
```
</section>

<section id="section-23-appendix-c" class="spec-section" markdown="1">
## 23. Appendix C — Example Flows
<div class="exec-callout">
  <p class="exec-callout-label">In plain terms — for executives</p>
  <p>These examples translate the protocol into real operating patterns: standard read operations, human-approved modifications, rejected risky actions, and budget cutoffs.</p>
</div>

### C.1 Read-only investigation
Authenticated session -> `READ` dispatch -> successful result -> completion audit.

### C.2 Modify action with approval
`MODIFY` request -> approval gate -> signed approval -> execution -> completion audit.

### C.3 Rejected approval
`ADMIN` request -> approval requested -> rejected with reason -> action cancelled.

### C.4 Budget exhaustion
Dispatch exceeds wall-clock budget -> cancel -> `CANCELLED` result -> alert + budget audit.
</section>

---

For legal-grade normative language and ongoing updates, refer to the canonical source:
[https://github.com/egaprotocol/spec/blob/main/SPEC.md](https://github.com/egaprotocol/spec/blob/main/SPEC.md)

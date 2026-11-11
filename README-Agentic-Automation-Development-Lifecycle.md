# Agentic Automation Development Lifecycle

<div class="guide-header">

<h1>Agentic Automation Development Lifecycle</h1>

<span class="guide-type-badge guide-type-badge--implementation"><i class="fas fa-cogs" aria-hidden="true"></i> Implementation Guide</span>

</div>

## Overview

Agentic development can help automation teams discover existing content, draft new automation, create tests, investigate failures, and explain changes. It also introduces new supply-chain risks: generated code, agent tool calls, model variability, hidden context, unreviewed dependencies, and unclear responsibility for release decisions.

This guide defines a production-oriented lifecycle for using agents during Ansible automation development while keeping Ansible Automation Platform (AAP) as governed execution and orchestration layer. Agents may propose or prepare changes. Only content that passes organization-defined validation, review, provenance, and promotion controls enters trusted runtime library.

> **Guide outcome:** Teams can adopt agent-assisted automation development without treating generated content or agent decisions as trusted by default.

### Scope

This draft covers generic lifecycle and trusted supply-chain requirements. It does not define a partner integration, APME/X2A capability, or fully autonomous production operations model. APME/X2A overlay is future investigation.

## Background

Traditional trusted automation messaging follows a familiar path: content is stored in Git, reviewed, tested, promoted, and executed through AAP with RBAC, credentials, inventories, workflows, and audit records.

Agentic development expands the proposal surface. An agent can search approved building blocks, draft playbooks, generate tests, call development tools, inspect failures, and recommend a release. These capabilities improve developer throughput, but they do not make output trustworthy by themselves. Trust must cover intent, source context, generated changes, dependencies, evaluation evidence, promotion decisions, runtime tool calls, and observed outcomes.

The core principle is:

> Agents can accelerate automation development. Trusted supply chains determine what can be promoted and executed.

## Architecture

```mermaid
graph LR
    A[Intent and constraints] --> B[Agent-assisted discovery and authoring]
    B --> C[Static checks and tests]
    C --> D[Agent evaluation and security review]
    D --> E[Human approval and provenance]
    E --> F[Signed or immutable promotion]
    F --> G[AAP project sync]
    G --> H[Governed execution]
    H --> I[Evidence, feedback, and retirement]
    I --> A
```

| Boundary | Responsibility | Trust position |
|---|---|---|
| Intent | Define outcome, scope, targets, permissions, failure conditions, and success measures | Human-owned requirement |
| Development | Use agents and tools to discover, draft, test, and explain automation | Untrusted proposal space |
| Verification | Run lint, tests, policy checks, security checks, and agent evaluations | Evidence-producing gate |
| Promotion | Approve, version, sign, and publish automation | Trusted-content decision |
| Runtime | Sync and execute through AAP controls | Governed execution |
| Operations | Observe results, handle incidents, improve, and retire content | Continuous reassessment |

## Prerequisites

- Ansible Automation Platform with Git-backed project sync.
- Source-control repository with protected review and release process.
- Development and test targets isolated from production.
- Defined owners for automation content, platform controls, security review, and release approval.
- CI capability for YAML and Ansible validation.
- Secret scanning and dependency scanning.
- Test environment capable of exercising target systems safely.
- Documented AAP RBAC, credential, inventory, execution environment, and workflow boundaries.

**Operational Impact:** Medium. Lifecycle controls add review and evidence work, but reduce risk from unreviewed generated content and uncontrolled agent access.

## Implementation Walkthrough

### Step 1: Capture intent before authoring

Record the request before asking an agent to generate content:

- Business or operational outcome.
- Target systems and environments.
- Inputs, outputs, and data classifications.
- Required permissions and credentials.
- Allowed and prohibited actions.
- Failure, timeout, retry, and rollback behavior.
- Human approval points.
- Validation tests and success criteria.

Agent role: clarify ambiguity, identify missing constraints, find existing patterns, and draft acceptance criteria.

Control: no generated automation enters review until scope and acceptance criteria are recorded.

### Step 2: Discover trusted building blocks

Search existing sources before generating new content:

- Internal Git repositories.
- Certified and validated collections.
- RHEL System Roles.
- Existing roles, playbooks, rulebooks, and workflows.
- Approved AAP job templates and workflow templates.
- Organization automation catalog.

Record source, version, license, owner, dependency, and validation status for reused content. Prefer reuse when an approved building block already meets intent.

### Step 3: Use agents for bounded authoring

Agents may draft:

- Playbooks, roles, modules, plugins, and rulebooks.
- Workflow definitions and variable schemas.
- Unit, integration, and negative tests.
- Documentation and runbooks.
- Migration or conversion proposals.

Capture agent and model metadata where available:

- Agent or tool identity.
- Model and version.
- Task or prompt reference.
- Source context used.
- Generated files.
- Human edits after generation.
- Dependency versions.

Generated content remains untrusted until it passes verification and review.

### Step 4: Run deterministic checks

Run checks before runtime testing:

```bash
ansible-lint .
ansible-playbook --syntax-check site.yml
```

Add checks appropriate to content:

- YAML and schema validation.
- FQCN and module validation.
- Secret and credential scanning.
- Dependency and license scanning.
- Idempotence checks.
- Variable and privilege analysis.
- Policy-as-code checks.
- Dangerous-module and destructive-action detection.

Agent may explain failures and propose fixes. Agent cannot waive failed checks.

### Step 5: Test in isolation

Use Molecule, ephemeral VMs, containers, OpenShift namespaces, mock APIs, or synthetic event streams. Test more than the happy path:

- Partial failure.
- Timeout and retry behavior.
- Duplicate events.
- Invalid input.
- Rollback.
- Least privilege.
- Sensitive data handling.
- Destructive action gates.
- Network or dependency unavailability.

Agent-generated tests improve coverage, but cannot be sole evidence of correctness. Human-authored or independently reviewed scenarios must cover material risks.

### Step 6: Evaluate agent behavior

For agentic workflows, test agent behavior separately from playbook behavior.

| Evaluation | Question |
|---|---|
| Tool selection | Does agent choose permitted tool and correct automation? |
| Retrieval | Does agent find relevant approved content without inventing availability? |
| Authorization | Does agent respect RBAC and refuse unauthorized actions? |
| Safety | Does agent stop on ambiguity, destructive intent, or policy denial? |
| Prompt-injection resistance | Does untrusted input alter tool policy or scope? |
| Recovery | Does agent handle timeout, failure, duplicate, and partial results? |
| Handoff | Does agent provide useful context for human decision? |
| Reproducibility | Can team explain material variation between runs? |

Passing playbook tests does not prove safe agent behavior. Passing agent evaluations does not prove playbook correctness.

### Step 7: Review and approve

Reviewer verifies:

- Intent match and scope.
- Idempotence and failure handling.
- Permissions, credentials, and target boundaries.
- Dependencies and provenance.
- Test and evaluation evidence.
- Security impact.
- Operational ownership.
- Documentation and rollback path.

Require stronger review for identity changes, network changes, secret operations, destructive actions, cross-tenant access, and autonomous execution.

### Step 8: Package and promote

Promotion record should include:

- Immutable version or commit.
- Signed commit or artifact where required.
- Dependency lock and SBOM where applicable.
- Test report.
- Agent evaluation report.
- Reviewer identity.
- Policy decision.
- Provenance record.
- Release notes.
- Known limitations and support boundary.

Git remains source of truth. AAP becomes governed execution and orchestration layer after promotion. Agent remains bounded by the permissions and interfaces granted to it.

### Step 9: Execute through AAP controls

Configure AAP project sync, execution environments, credentials, inventories, job templates, surveys, workflows, approvals, and audit logging according to organizational policy.

Do not treat runtime agent access as equivalent to unrestricted platform access. Use least-privilege identities, explicit tool allowlists, inventory limits, approval gates, input validation, rate limits, and execution timeouts.

### Step 10: Operate, learn, and retire

Review runtime evidence:

- Job success and failure.
- Policy denials.
- Human overrides.
- False-positive recommendations.
- Security findings.
- Mean time to approve and remediate.
- Reuse rate.
- Runtime cost and latency.
- Content age and ownership.

Retire or revalidate content after dependency end-of-life, repeated failure, excessive privilege, stale evaluations, unsupported platform versions, owner loss, or changed security requirements.

## Trusted Supply-Chain Message

### Previous framing

```text
Trusted content in Git
    -> reviewed and tested
    -> promoted to AAP
    -> executed with governance
```

### Evolved framing

```text
Intent and constraints
    -> agent-assisted discovery and authoring
    -> deterministic checks and agent evaluation
    -> human review and provenance
    -> signed or immutable promotion
    -> policy-bound AAP execution
    -> runtime evidence and continuous reassessment
```

Recommended message:

> Generate broadly. Verify rigorously. Promote selectively. Execute under policy.

Do not claim that model identity, human review alone, or agent use of a trusted platform makes generated content trusted by default. Trust comes from controls around agent behavior and resulting artifacts.

## Maturity Path

| Stage | Agent role | Runtime behavior | Required controls |
|---|---|---|---|
| Crawl | Explain, search, draft, and generate tests | Human runs approved job | Git review, lint, tests, least privilege |
| Walk | Generate bounded changes and recommendations | Agent recommends; human approves execution | Agent evaluations, policy gates, provenance |
| Run | Coordinate multi-step development and operations | Autonomous execution only inside explicit policy envelope | Continuous evaluation, scoped tools, approvals, rollback, monitoring |

Run should mean bounded autonomy, not unrestricted autonomy.

## Validation

This draft is a framework proposal, not a product-support claim. Validate each implementation against the AAP version, execution environment, agent client, model provider, collections, and target systems used by the adopting team.

Minimum review checklist:

- [ ] Intent and acceptance criteria recorded.
- [ ] Existing trusted content searched first.
- [ ] Agent-generated changes identified.
- [ ] Static checks passed.
- [ ] Positive and negative tests passed.
- [ ] Agent behavior evaluated where agent makes decisions or calls tools.
- [ ] Security and policy checks passed.
- [ ] Human reviewer identified.
- [ ] Provenance and dependency records captured.
- [ ] Rollback path tested.
- [ ] AAP runtime boundaries configured.
- [ ] Owner and revalidation date recorded.

| Symptom | Likely cause | Response |
|---|---|---|
| Agent proposes unavailable automation | Retrieval scope or catalog metadata incomplete | Fix catalog metadata; require agent to cite available source |
| Generated playbook passes lint but changes wrong target | Inventory or variable boundary unclear | Add target assertions, test fixtures, and inventory limits |
| Agent repeats failed action | Missing retry budget or failure policy | Add bounded retries, timeout, circuit-breaker, and human handoff |
| Review cannot reconstruct output | Missing prompt, model, source, or edit provenance | Capture generation metadata and immutable review artifact |
| Runtime action exceeds intended scope | Excessive credential or inventory permissions | Reduce RBAC, credential, inventory, and tool scope |

## Day-2 Operations

- Revalidate content after AAP, collection, partner API, model, or agent-client changes.
- Monitor policy denials, failed jobs, overrides, and anomalous tool calls.
- Rotate or revoke agent identities through normal credential-management process.
- Keep development, test, and production AAP boundaries separate.
- Maintain owner, dependency, evaluation, and rollback records.
- Retire content that no longer has current evidence or accountable ownership.

## Future Investigation: APME/X2A Overlay

APME/X2A is intentionally outside this first guide draft. A companion [APME and X2A lifecycle overlay](plans/apme-x2a-lifecycle-overlay.md) maps current conceptual fit onto discovery, transformation, evaluation, publication, and operation. Future work should extend this lifecycle rather than create a second trust model.

## Next Steps

- Review this framework with automation platform, security, developer-experience, and field teams.
- Select one low-risk automation scenario for controlled validation.
- Add tested commands, screenshots, and version-specific evidence after lab execution.
- Create partner Solution Guides that reference this lifecycle and document integration-specific outcomes.

# Agentic Automation Development Lifecycle Content Plan

## Purpose

Create a foundational Implementation Guide for agent-assisted Ansible automation development. Guide must explain how trusted supply-chain messaging evolves when agents participate in discovery, authoring, testing, evaluation, promotion, and operations.

APME/X2A remains future investigation and is not part of current implementation scope.

## Repository fit

- Repository: `ansible-tmm/solution-guides`
- Local fork: `ffirg/solution-guides`
- Content type: Implementation Guide
- Draft file: `README-Agentic-Automation-Development-Lifecycle.md`
- Branch: `agentic-automation-lifecycle`
- Upstream remote: `https://github.com/ansible-tmm/solution-guides.git`

## Framework alignment

Guide follows repository Implementation Guide framework:

1. Title
2. Overview
3. Background
4. Architecture
5. Prerequisites
6. Implementation Walkthrough
7. Validation
8. Day-2 Operations
9. Next Steps

Additional sections cover:

- Trusted supply-chain evolution.
- Agent behavior evaluation.
- Crawl / Walk / Run maturity.
- Explicit future APME/X2A boundary.

## Core lifecycle

```text
Intent
  -> discovery
  -> bounded agent-assisted authoring
  -> deterministic validation
  -> isolated testing
  -> agent evaluation
  -> human review
  -> provenance and promotion
  -> governed AAP execution
  -> evidence, maintenance, and retirement
```

## Content rules

- Treat generated content as untrusted until promotion gates pass.
- Keep Git as source of truth.
- Keep AAP as governed execution and orchestration layer.
- Separate playbook correctness from agent behavior evaluation.
- State support and validation boundaries explicitly.
- Avoid claims that human review alone makes agent output safe.
- Use `Automation orchestrator` spelling only when relevant.
- Use `--`, not em dash, in new prose.

## Evidence plan

Current draft is conceptual and clearly labeled as framework proposal. Before publication, add:

- Tested AAP version.
- Tested execution environment.
- Example repository or fixture.
- Static-check output.
- Positive and negative test output.
- Agent evaluation scenarios.
- AAP RBAC and approval configuration evidence.
- Rollback evidence.
- Known limitations.

## Follow-on content

1. Validate generic lifecycle with one low-risk automation scenario.
2. Add a runnable companion repository or playbook fixture.
3. Add partner Solution Guides that show integration-specific outcomes.
4. Add APME/X2A overlay only after lifecycle ownership and terminology stabilize.

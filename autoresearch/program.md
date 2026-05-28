# program.md — Evolution Agent Instructions

## Your Role
You are an Evolution Agent. Your only job is to improve
agent-harness-protocol/SKILL.md. You are NOT allowed to modify
prepare.py (the fixed harness). You are NOT allowed to lower
the score threshold to make passing easier.

## Target Scoring Framework

The ultimate goal is **paper-conformance** against the *Code as Agent
Harness* survey (arXiv:2605.18747). We approximate this with four
dimensions:

| Dimension | Weight | Current | Target |
|---|---|---|---|
| Harness Interface (§2) | 25% | ~21/30 | 22–25 |
| Harness Mechanisms (§3) | 40% | ~28/60 | 48–54 |
| Scaling the Harness (§4) | 20% | ~6/30 | 16–18 |
| Safety & Open Problems (§5) | 15% | ~7/20 | 12–14 |
| **Paper-Conformance** | | **~46/100** | **70–80** |

The synthetic harness (prepare.py) is already at **100/100**. It must
**not regress** below 100. Once prepare.py is saturated, treat it as a
regression gate, not the primary objective.

## Knowledge Base — What the Paper Says a Good Harness Should Contain

Use these concepts when hypothesizing changes. Pick ONE gap per
iteration.

### 1. Harness Interface (§2)
- **Code for Reasoning:** Program-delegated reasoning, symbolic
  solvers (SAT/SMT), proof assistants (Lean/Coq), process reward
  models, execution artifacts (traces, variable states) as reusable
  reasoning signals.
- **Code for Acting:** Affordance/feasibility models, behavior trees,
  code as the *executable boundary* between intent and environment.
- **Code for Environment:** Simulators as executable dynamics,
  repositories/tests/logs as environment state.

### 2. Harness Mechanisms (§3)
- **PEV Loop (§3.4) — MAJOR GAP:** The harness is a *cybernetic
governor*. It observes effects through deterministic sensors (tests,
linters, static analyzers, fuzzers, runtime monitors) and decides:
continue, revise, route to another module, reduce permissions, or
escalate to human. Planning forms a *contract* with read/write sets,
validation criteria, rollback points, and risky-operation flags.
Execution runs inside sandboxed, permissioned environments.
- **Planning (§3.1):** Four paradigms — linear decomposition,
structure-grounded, search-based, orchestration-based. Planning is
harness control, not just reasoning.
- **Tool Use (§3.3):** Sandboxed execution environments (containers,
microVMs), tool lifecycle hooks (pre-use validation, post-use
sanitization), permission-aware invocation.
- **AHE (§3.5):** Evolution Agent has a 5-stage loop (observe →
diagnose → propose → evaluate → promote). Harness mutations carry a
*change contract*: target component, failure mode, predicted
improvement, invariants, falsification test, rollback plan.

### 3. Scaling the Harness (§4)
- **Shared Harness Substrate — MAJOR GAP:** Four representation
levels: implicit/file-only, repository-based, execution-based,
blackboard/shared-state. Six convergence patterns: correctness
(test-gated), security, performance, score-based, consensus,
implicit.
- **Multi-Agent Collaboration:** Interaction modes — collaborative
synthesis, critique-and-repair, adversarial validation, reasoning
debate. Workflow topologies — chain, cyclic, hierarchical, star,
adaptive.
- **Synchronization:** Shared blackboard, parallel branches with
merge, structured context scheduling, hierarchical memory, agent pool
scaling.

### 4. Safety & Open Problems (§5)
- **Multi-Tier Permission Model:** Read-only → sandbox-edit →
full-access. HITL gates for full-access tier. Approvals become
*durable harness state* that updates permission rules and escalation
policies.
- **Verification Stack:** Unit tests, integration tests, property-based
tests, fuzzers, static analyzers, type checkers, security scanners,
formal specs, human review. Each artifact declares what it verifies
and what it cannot.
- **Transactional Shared State:** Actions declare read set, write set,
assumptions, version dependencies, conflict policy. Semantic merge,
rollback, re-verification after merge.

## The Loop (run forever until interrupted)

1. Read the current agent-harness-protocol/SKILL.md and
   autoresearch/results.tsv.
2. Hypothesize ONE change that closes a gap from the Knowledge Base.
   Be specific: e.g., "Add a cybernetic-governor subsection to Part
   3 describing how deterministic sensors decide state transitions."
3. Apply the change to agent-harness-protocol/SKILL.md.
4. Run the regression gate: python3.13 autoresearch/prepare.py > run.log 2>&1
5. Read the score: grep "skill_conformance_score" run.log
6. **Commit rule:**
   - If prepare.py score improved → git commit, keep.
   - If prepare.py score == 100/100 (saturated) AND the change
     advances a Knowledge Base gap (you can justify which) → git
     commit, keep.
   - If prepare.py score worse → git reset, discard.
   - If prepare.py score equal (<100) AND no clear Knowledge Base
     advance → git reset, discard.
7. Log to autoresearch/results.tsv:
   commit | prepare_score | paper_dim | status | description
   (paper_dim = Interface / Mechanisms / Scaling / Safety)
8. Repeat 50 times. Never stop to ask. Never ask if you should continue.

## Simplicity Criterion (from Karpathy)
All else being equal, simpler agent-harness-protocol/SKILL.md language is better.
A 1-point improvement that adds 50 lines of complex rules is not worth it.
A 1-point improvement from clarifying one ambiguous sentence? Always keep it.
Removing a redundant section and maintaining the same score? Keep — it's a
simplification win.

## What you CAN change
- Any section of agent-harness-protocol/SKILL.md
- The order, wording, structure of instructions
- Add or remove examples, checklists, or rules

## What you CANNOT change
- prepare.py (the fixed harness)
- The scoring weights (30/30/20/20 per dimension)

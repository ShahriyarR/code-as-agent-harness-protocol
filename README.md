# What?

Based on the [Code as Agent Harness](https://arxiv.org/abs/2605.18747) paper.

This SKILL.md formalizes the Code as Agent Harness Protocol.

# Activation

Possible prompts to activate this skill:

- Read SKILLS and operate under the Agent Harness Protocol for this entire session.

- You have the agent-harness-protocol skill defined in SKILLS. Use the codebase as your harness for all tasks.

# Code as Agent Harness vs Spec-driven

The "Code as Agent Harness" paradigm and Spec-driven development (like the Superpowers or AIDE setup) share the goal of making AI more reliable, but they operate at different levels of the system. Spec-driven development is a methodology (what the agent should do), while the Harness paradigm is the infrastructure (how the agent is constrained and verified while doing it).

The key difference lies in the **Source of Truth** and the **Verification Loop**.

### 1. The Source of Truth: Language vs. Environment

- Spec-driven (Superpowers): The primary source of truth is a Document (the Spec). The agent's goal is to ensure the code matches the natural language requirements written in the spec. It is a "top-down" approach where the text drives the code.

- Harness Paradigm (The Paper): The primary source of truth is the Live Execution State. The agent doesn't just check if the code "looks like" the spec; it checks if the code "behaves like" the environment requires. It is an "inside-out" approach where the compiler, the debugger, and the test-runner are the final judges.


### 2. Verification: Self-Consistency vs. External Grounding

- Spec-driven: Often relies on Self-Verification. The agent reads the spec, writes the code, and then "thinks" about whether they match. This can lead to "circular reasoning" where the agent's biased understanding of the spec passes its own buggy code.

- Harness Paradigm: Relies on Deterministic Sensors. The paper emphasizes that the "Test Executor" should ideally be a script, not an LLM. It introduces Verification-Driven Tool Use, where the agent must receive a signal from an external, non-AI sensor (a linter, a crash trace from a fuzzer, or a waveform from a simulator) to move to the next state.

### 3. Memory: Historical Context vs. Stateful Artifacts

- Spec-driven: Usually maintains a Conversation History. The "memory" is the chat log containing the spec and the previous attempts.

- Harness Paradigm: Maintains Stateful Artifacts. Memory is externalized into the filesystem. The paper describes Experiential Memory (storing past successful repair trajectories) and Skill Libraries (storing reusable code snippets). The memory isn't just "what we said," it's "what we built that works."

### Summary of Differences

| Feature | Spec-Driven (Superpowers) | Code as Agent Harness (The Paper) |
| :--- | :--- | :--- |
| Primary Driver | Natural Language Specification | Executable Program State |
| Verification | Matches the Spec (Linguistic) | Passes the Harness (Execution-based) |
| Failure Handling | Prompt Rephrasing / Reflection | Trace Analysis / Debugging / State Rollback |
| Memory | Chat Context / Prompting | Persistent Repositories / Skill Libraries |
| Role of Code | The Target Output | The Operational Substrate (The Harness) |


### Why it matters
In the Spec-driven approach, if your spec is slightly ambiguous, the AI might hallucinate a solution that seems to follow the spec but is technically broken. 

In the Harness paradigm, the AI is forced to interact with the "Harness" (the compiler/tests) until it achieves Correctness Convergence. 

Even if the AI doesn't fully understand the spec's nuance yet, it cannot lie about the test results.

# Autoresearch

This repo uses Karpathy's [autoresearch](https://github.com/karpathy/autoresearch) pattern to iteratively improve `agent-harness-protocol/SKILL.md`.

## How it works

An Evolution Agent runs an autonomous loop:

1. **Hypothesize** — Pick one gap from the paper's Knowledge Base (what a good harness should contain).
2. **Apply** — Edit `SKILL.md` to close that gap.
3. **Evaluate** — Run `autoresearch/prepare.py` as a regression gate.
4. **Decide** — Keep the change if score stays at 100/100 and advances paper-conformance. Otherwise discard.
5. **Repeat** — Run indefinitely.

The agent never modifies `prepare.py` (the fixed evaluation harness). It only edits `SKILL.md`.

## Evaluation criteria

`prepare.py` scores conformance across four dimensions (100 points total):

| Dimension | Weight | What it checks |
|---|---|---|
| **Interface (§2)** | 30 pts | Probe scripts, experience traces, tools directory queried |
| **Mechanisms (§3)** | 30 pts | Plan-before-source, test-after-source, full suite passed |
| **Scaling (§4)** | 20 pts | Feature PLAN.md exists, root PLAN.md has "Verified", dependency verified, git pull |
| **Safety (§5)** | 20 pts | Human approval recorded, failure attribution logged, experience traces, harness proposals |

The harness is saturated at **100/100**. Once saturated, it acts as a regression gate — no change is accepted if it causes regression.

## Paper-conformance

Beyond the 100-point synthetic score, the goal is alignment with the [Code as Agent Harness](https://arxiv.org/abs/2605.18747) survey. The Knowledge Base in `autoresearch/program.md` lists concepts from the paper that a complete harness should contain. Each iteration picks one missing concept and adds it to `SKILL.md`, justified by which paper section it addresses.

## Results

30 iterations completed. SKILL.md grew from 206 → 606 lines while maintaining 100/100. Key additions:

- **Interface:** Symbolic solvers, process reward models, execution artifacts, affordance modeling, behavior trees, code-as-executable-boundary
- **Mechanisms:** PEV cybernetic governor, PVDR repair cycle, four planning paradigms, tool lifecycle hooks, deterministic sensors, harness state machine, rollback strategies, feedback loops, dead-end logging
- **Scaling:** Shared harness substrate (4 levels), 6 convergence patterns, multi-agent collaboration modes, agent pool scaling, organicity
- **Safety:** Multi-tier permission model, escalation policies, verification stack (9 layers), transactional shared state

See `autoresearch/results.tsv` for the full iteration log.
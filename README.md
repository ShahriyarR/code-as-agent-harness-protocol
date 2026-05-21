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
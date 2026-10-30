# kill-code: Taming AI Coding with Software Engineering

[English](./README.md) | [中文](./README.zh-CN.md)

## 1. The Trouble with Vibe Coding Today—and Its Root Cause

### 1.1 A "Follow-the-Vibes" Way of Programming

Vibe coding became Collins Dictionary's Word of the Year in 2025. Its core posture: developers describe intent in natural language, hand "how to implement" almost entirely over to the LLM, and accept or reject results by "feel." Carrying this posture are autonomous coding agents like Claude Code, Codex, and Cursor—built on the **ReAct (Reasoning + Acting)** idea.

### 1.2 Surface Problems

As adoption has scaled, problems have erupted:

- **Business-logic blind spots**: LLMs generate code that is syntactically correct but semantically wrong—order queries missing permission checks, payment amounts trusted directly from the frontend, approval workflows that can be bypassed. Traditional security tools barely see these.
- **Hallucination & supply-chain pollution**: LLMs "confidently" recommend dependency packages that don't exist; attackers register those names and upload malicious code.
- **Maintainability collapse**: Generated code lacks a consistent architecture; technical debt piles up fast. Bugs devolve into "vibe debugging"—neither human nor AI can explain why it broke.
- **Accountability vacuum**: When code causes real damage, liability (prompter / AI provider / deployer) has no consensus.

### 1.3 Root Cause: Full-Context Stuffing + Linear ReAct Accumulation

The working loop of mainstream coding agents:

```
Context_t = Context_{t-1} + Thought_t + Action_t + Observation_t
```

Each reasoning step re-injects **all historical thoughts, actions, and observations** back into the LLM. This produces three structural consequences:

1. **Context-window explosion**: tokens burn fast, hitting model limits quickly.
2. **Goal drift**: typically after 7–8 steps, the original goal is diluted in the massive context—the LLM "forgets what it was doing."
3. **Signal-to-noise degradation**: the larger the context, the more irrelevant information, the higher the hallucination rate, the less stable the output.

**The essential problem**: vibe coding stuffs "all the knowledge a complex system needs" into the LLM at once, in full, and lets it flail around inside a massive context. This runs directly against how human software engineers handle complexity.

## 2. How Traditional Software Engineering Does It

### 2.1 Information Hiding: The First Principle of Modularization

In 1972, David Parnas argued in *On the Criteria To Be Used in Decomposing Systems into Modules* that **systems should not be decomposed by flowchart**—they should be decomposed by "design decisions likely to change." Each module hides one "design secret," exposing only necessary capabilities through a stable interface. He gave three evaluation dimensions: managerial payoff, product flexibility, and comprehensibility.

### 2.2 The Single Responsibility Principle

Robert C. Martin unified Parnas's information hiding, Dijkstra's separation of concerns, and Constantine's cohesion/coupling into the **S—the Single Responsibility Principle** in SOLID:

> A class/module should have one, and only one, reason to change.

Responsibility diffusion is the root of bad smells: a module bearing both P1 and P2 means changing P1 may break P2.

### 2.3 Design Patterns: Encapsulating "Change" in Controllable Boundaries

- **Strategy**: a family of algorithms each encapsulated, eliminating if-else branches; adding a strategy doesn't touch existing code (Open/Closed + Single Responsibility).
- **Chain of Responsibility**: handlers chained together, each node doing one thing, requests passing along the chain.
- **Template Method**: the algorithm skeleton is fixed; variable steps are deferred to subclasses.

Their shared goal is **high cohesion, low coupling**—each unit has a single responsibility and holds minimal knowledge.

### 2.4 The Key Characteristic of the Traditional Approach

> **Define boundaries first, then write code; give each module "just enough" information, not all knowledge.**

Design patterns are essentially **context-isolation mechanisms**: they keep unnecessary knowledge outside module boundaries. A function that formats amounts doesn't need to know the entire order system's business rules.

## 3. The Core Thesis: AI Coding Must Return to Software Engineering

### 3.1 Two Approaches Compared

| Dimension | vibe coding / ReAct agents | Software-engineering approach |
|-----------|---------------------------|-------------------------------|
| Context strategy | Stuff everything in; bigger is better | Minimal and sufficient, scoped per module |
| Module boundaries | Blurry; LLM decides | Fixed first, confirmed layer by layer |
| Knowledge transfer | All knowledge to the LLM | Only what the current unit needs |
| Handling complexity | Stack the context window | Decomposition + design patterns |

### 3.2 The Core Thesis

Ask an LLM to write a simple function: accuracy is very high. Ask it to build a complex system: problems abound. The gap isn't model capability—it's **task size** and **context purity**.

So the path of this approach is to **execute every step of software engineering strictly**—except each step is "AI drafts + human confirms," with design patterns as the decomposition scaffold throughout:

1. **AI drafts the design**: defines module boundaries and responsibilities → human confirms.
2. **AI selects the design-pattern combination** (Chain of Responsibility + Strategy + Template Method, etc.) → human confirms.
3. **With design patterns enforced, AI decomposes the complex problem layer by layer**—into multiple strategies, each strategy further into multiple functions → human confirms at each step.
4. **Once decomposed to function granularity, AI builds the minimal, sufficient, function-specific context and completes the coding.**

**Essence**: humans no longer write code, nor do they do the design for the AI; but humans keep the gate at every key node of software engineering. Design patterns make "decomposition" systematic—ensuring each function has clear boundaries and a single responsibility—so the final coding step lands in the LLM's zone of strength.

### 3.3 The Key Difference: Minimal Context, Not Maximal

This is the fundamental divide between this approach and vibe coding:

- **vibe coding**: no decomposition; stuff all background knowledge into the LLM and rely on it to sort things out inside a massive context.
- **This approach**: after the preceding layered decomposition, each function already has a single responsibility—its required knowledge is inherently small. Build it a **specific, minimal, sufficient context**.

Why "minimal"?

- The larger the context, the lower the signal-to-noise ratio, the more hallucinations.
- A function with a single responsibility naturally needs little knowledge.
- Stuffing the entire order-system business doc into a function that only formats amounts is noise, not information.

**The dual role of design patterns here**: they're not just human-readable code organization—they're a **context-trimming scaffold**. Each node of a chain, each branch of a strategy, each step of a template method naturally delineates "what this unit needs to know."

## 4. Advantages of the Approach

1. **Accuracy jump**: reduce complex tasks to "function-level," keep the LLM in its zone of strength.
2. **Maintainability**: clear module boundaries, SOLID-compliant, controllable technical debt.
3. **Traceability**: every design decision is human-confirmed at each node—auditable and replayable.
4. **Verifiability**: small functions are easy to unit-test; strong regression protection.
5. **Context economy**: no more token explosion—cost and latency both drop.
6. **Clear responsibility**: humans gate and confirm; AI drafts and codes—division of labor is explicit.
7. **Avoids vibe coding's core risks**: suppresses business-logic vulnerabilities, context drift, and goal forgetting at the source.

## 5. Closing

The problem with vibe coding isn't that AI is bad—it's that "handling complex systems" is dumped wholesale onto the LLM plus a massive context. The decades of software-engineering wisdom—modularization, information hiding, design patterns—are precisely about "how to tame complexity." Bringing them back into AI coding isn't regression; it's letting AI work at the scale where it's actually good.

## 6. Why This Document Was Written

To be clear upfront: this document is not about advocating a viewpoint, nor is it a pure critique of vibe coding. Its purpose is to prepare for **building a coding agent that follows software-engineering methodology**—one that genuinely solves the real problems of vibe coding.

What kill-code wants to do is simple: turn every step of software engineering (design, pattern selection, decomposition, coding) into a flow that AI can execute and humans can confirm; use design patterns to decompose complex systems all the way down to function granularity; let the LLM work only at the scale where it excels; and give each function a minimal, sufficient, function-specific context.

This can't be done alone. If you also believe AI coding shouldn't stop at "full context + follow the vibes," if you've been burned by context drift, business-logic holes, or unmaintainable code in vibe coding—join in, and let's make it real together.

## References

- Parnas, D. L. (1972). *On the Criteria To Be Used in Decomposing Systems into Modules*. ACM.
- Robert C. Martin. *The Single Responsibility Principle (SRP)*, part of SOLID.
- ReAct: the Reasoning + Acting paradigm and its limitations in coding agents.
- Collins Dictionary Word of the Year 2025: vibe coding.
- Materials on design patterns (Strategy, Chain of Responsibility, Template Method).

# Report: Current Directions in AI Agents (2025–2026) — Platform Architecture, Tooling, Safety, Security, and Evaluation

## 1) OpenAI Agents SDK + Responses API: “Agents as applications” that plan, call tools, and maintain state

OpenAI’s 2025→2026 platform direction reframes “Agents” as *applications* rather than single-shot prompts. The core idea is that an agent should be able to:
- **Plan multi-step work**
- **Call external tools**
- **Collaborate across specialist capabilities**
- **Retain enough state** to complete a task end-to-end (including long or iterative workflows)
- Run within a standardized developer layer so applications can be built consistently

### What the developer layer provides
The **Agents SDK** acts as orchestration infrastructure for building agentic systems. Rather than requiring developers to implement custom control loops from scratch, the SDK provides primitives that support:
- **Agent execution loops** (how the agent iteratively reasons/decides next actions)
- **Tool invocation** (calling developer-defined or platform-provided tools)
- **State management** (keeping context and task memory across steps)
- **Inter-agent or multi-component collaboration** patterns (when the application is composed of multiple specialized modules)
- Integration with **Responses API**, which serves as the underlying interface for model calls and structured interactions.

### Why “enough state” matters
In many agent problems, correctness depends on:
- What the agent learned earlier in the run
- Partial outputs produced so far
- Tool results returned in earlier steps
- Constraints discovered during execution (e.g., “the file is in this folder,” “the API returned rate-limit,” “the user needs a summary not a report”)

OpenAI’s framing emphasizes that agent applications must maintain operational continuity—agents aren’t just asked to “think” once; they must *progress*.

### Practical implications for builders
Developers should expect to architect around:
- **Looped execution**: decide → act → observe → update → decide again
- **Tool contracts**: define input/output interfaces for tools so the agent can reliably use them
- **State and transcript logging**: persistent run state for debugging and reproducibility
- **Structured outputs**: robust parsing and downstream handling

**Source:** https://developers.openai.com/api/docs/guides/agents

---

## 2) OpenAI “New tools for building agents”: unified SDK primitives for web search and computer use

OpenAI’s update (“New tools for building agents”) expands agent construction by offering developer-facing capabilities that agents can invoke as part of their loop. The highlighted direction includes:
- A unified **Agents SDK umbrella** for agent building
- **Tool use** as a first-class capability
- Platform tools including **web search** and **computer use**

### Web search as an agent capability
**Web search** enables agents to:
- Find up-to-date information not contained in model weights
- Cross-reference sources
- Resolve ambiguity by checking multiple pages
- Provide citations or at least grounded references (depending on integration)

In agent architectures, search is not a one-time preprocessing step; it becomes a *decision point*:
- The agent determines whether it needs external knowledge
- It formulates queries
- It chooses whether to open/inspect results
- It integrates findings into subsequent reasoning

### Computer use as an agent capability
“Computer use” supports an agent interacting with user-facing or web interfaces in a way more akin to a human operator. Conceptually, this allows:
- Navigating UIs
- Filling forms
- Reading and responding to on-screen content
- Executing multi-step workflows in environments that may not have clean APIs

This is particularly useful when:
- The target system has no developer-friendly API
- Interactions require UI-level steps (e.g., dashboards, portals, internal tools)

### Why unification matters
The platform message is that agent tooling should feel cohesive:
- A single developer experience for defining agent behavior
- Shared mechanisms for invoking tools
- A consistent loop where tool calls and observations drive progress

This reduces fragmentation compared to assembling separate subsystems (search + scraping + browser automation + state managers).

**Source:** https://openai.com/index/new-tools-for-building-agents/

---

## 3) “The next evolution of the Agents SDK”: long-horizon tasks and native sandbox execution

OpenAI further positions the Agents SDK evolution toward **long-horizon execution**—agent behavior over extended timelines with more autonomous work. The platform emphasizes that agents should be able to:
- Inspect files
- Run commands
- Edit code
- Continue multi-step tasks rather than terminating after a single response

### Long-horizon task execution: a bigger loop
Traditional agent interactions can be short: gather info → respond. Long-horizon tasks require:
- Multiple iterations of tool use
- Progressive refinement of artifacts (files, code, plans)
- Handling intermediate failures (e.g., compilation errors, missing dependencies)
- Coordinating outputs across steps

OpenAI’s messaging highlights an agent loop capable of these iterative improvements.

### Native sandbox execution: safer operational behavior
A key addition is **native sandbox execution**, intended to support:
- Running commands or code in a controlled environment
- Limiting what the agent can access
- Reducing risk from arbitrary execution

This matters because long-horizon agents naturally want to:
- Execute programs
- Modify files
- Run tests or scripts

Without sandboxing, such capabilities create substantial security and safety risks.

### Operational best practices implied by the platform direction
Even if sandboxing exists, robust implementations typically add:
- **Resource limits** (time, CPU, memory)
- **Network controls** (allow/deny outbound as needed)
- **File system scoping** (only expose relevant working directories)
- **Audit logging** (capture commands and file changes)
- **Clear separation of privileges** (agent should not automatically inherit high privileges)

### Expected developer experience shift
Builders should design for workflows where:
- The agent can “work” like a developer (edit, test, rerun)
- The environment enforces containment (sandbox)
- The agent can recover and proceed after failures (not restart from scratch)

**Source:** https://openai.com/index/the-next-evolution-of-the-agents-sdk/

---

## 4) Microsoft AutoGen and consolidation toward a “Microsoft Agent Framework”: multi-agent cooperation at scale

Microsoft’s **AutoGen** is an open-source framework aimed at enabling **cooperation among multiple agents** to solve tasks. Instead of a single monolithic agent doing everything, AutoGen supports:
- Multiple agents with distinct roles or skills
- Communication between agents
- A coordinated solving process that can be more effective for complex tasks

### AutoGen’s role in the broader ecosystem
AutoGen’s core value is that it provides a reusable “multi-agent orchestration” approach:
- Agents can exchange messages
- Specialized reasoning can be delegated to relevant sub-agents
- The system can decompose tasks and iterate collaboratively

### Consolidation/merging direction implications
The context indicates ecosystem consolidation/merging trends discussed in 2025–2026 directions, including references to consolidation/merging discussions within the AutoGen community (e.g., GitHub discussion threads).

What this means for practitioners:
- Frameworks may evolve toward a more unified orchestration layer
- Developers should anticipate compatibility layers or migration paths
- Designing agent systems with modular abstractions (roles, messaging, tool interfaces) will make transitions easier

### Practical design considerations for multi-agent systems
Even when using frameworks like AutoGen, teams typically must decide:
- **Agent boundary**: what each agent owns (plan vs code vs verification)
- **Communication protocol**: message formats, summarization policies, routing
- **Tool access**: whether each agent independently calls tools or tools are centralized
- **Termination conditions**: when the system should stop iterating
- **Cost control**: multi-agent systems can increase compute usage quickly

### Why multi-agent still matters in 2025–2026
Complex workflows benefit from specialization:
- One agent drafts a plan
- Another executes tools
- Another reviews results for correctness or policy compliance
- Another composes final user outputs

This becomes especially valuable when tasks require multiple distinct expertise modes (research, coding, analysis, verification).

**Sources:**
- https://www.microsoft.com/en-us/research/project/autogen/
- https://github.com/microsoft/autogen/discussions/7066

---

## 5) ReAct framing as a baseline: “reasoning + acting” with external tool use

The **ReAct** (Reasoning and Acting) paradigm remains a foundational agent architecture concept. IBM’s overview describes ReAct agents as systems that combine:
- **Reasoning** (often conceptualized as a chain-of-thought internally)
- **External tool use** (actions that affect the environment or fetch information)

### Core principle: reasoning is interleaved with action
In ReAct, the agent does not purely “think” then “answer.” Instead:
- The agent reasons about what to do next
- It selects tools and actions
- It observes results
- It updates its reasoning and proceeds

This interleaving is what makes tool-using agents effective—tool outcomes become direct inputs to next decisions.

### Why ReAct persists as a baseline
It’s broadly applicable because:
- It matches how many real tasks progress (plan → query → retrieve → verify → act → finalize)
- It naturally supports iterative refinement
- It improves correctness for tasks requiring external facts or operations

### Practical considerations when implementing ReAct-like loops
- Tool choice should be constrained to a defined action space
- Observations from tools should be structured and summarized for the model
- Systems should include safeguards for tool failures (timeouts, errors, empty results)
- The “reasoning” component should not be treated as the only source of truth—tool outputs and state tracking are essential

### Relationship to platform direction
ReAct is an architectural framing; platform SDKs (like Agents SDK and other orchestrators) provide the operational mechanics to implement these loops robustly.

**Source:** https://www.ibm.com/think/topics/react-agent

---

## 6) Agent safety: “agent behavior must not be coupled to language-level control” (Parallax argument)

A 2026 arXiv paper (“Parallax: Why AI Agents That Think Must Never Act”) advances a safety-critical thesis: safety cannot be achieved through **language-level control mechanisms alone**. The paper argues that agent behavior must not rely solely on prompting or text-based instructions to prevent unsafe actions.

### The safety concern
Language-level mechanisms can be:
- Overridden by adversarial prompts
- Bypassed through reasoning-to-action pipelines
- Undermined by internal goal formation or misalignment
- Insufficient against novel attack strategies

If the agent’s ability to act in the world is linked only to what it “reads” in its prompt, then safety becomes brittle.

### “Structural controls” over prompt-only controls
The paper motivates:
- Stronger structural controls (e.g., enforced policies at tool/permission layers)
- Separation between *thinking* and *acting*
- Enforcement mechanisms that persist regardless of the agent’s textual instructions

### How this interacts with tool-using agents
Tool-enabled agents are uniquely exposed because:
- The agent can take actions external to the model
- Those actions can cause harm even if the model “intended” something safe

Thus, safety mechanisms must constrain actuation, not just instruct behavior.

### Practical implications for agent system design
Even without adopting the exact worldview of the paper, the engineering takeaway is:
- Implement safety enforcement **outside** the language model
- Gate tool calls using policy checks
- Use permissioning/least privilege so “thinking” cannot automatically become harmful execution
- Treat prompts as advisory, not authoritative for security

**Source:** https://arxiv.org/html/2604.12986v1

---

## 7) Anthropic: “computer use” capability and Claude “Agent Skills” for extending functionality

Anthropic’s direction includes:
1. **Computer use** as an agent capability
2. A mechanism for extending Claude via **Agent Skills**

### Computer use capability
Anthropic’s “computer use” framing positions it as a way for agents to interact with computing environments similarly to a user—useful when:
- APIs are unavailable
- Tasks require UI-level steps
- Systems require visual context or multi-step interaction

In an agent loop, computer use becomes one of several tools:
- If text-only tools suffice, the agent uses search or API calls
- If interaction requires a UI, it uses computer use

### Agent Skills: extending Claude with reusable capability bundles
Anthropic’s **Agent Skills** are described as **instruction/script bundles** that extend the model’s capabilities. Conceptually, Agent Skills allow:
- Packaging of behaviors and instructions
- Reuse across different agent applications
- A structured way to add functionality beyond default model abilities

In practice, “skills” can function like modular add-ons:
- Define what the skill does
- Provide scripts/instructions
- Allow agents to invoke the skill as a unit

### Why this is important for developers
Agent Skills support:
- Modularity: isolate capability logic into reusable components
- Maintainability: update skills without rewriting entire agents
- Capability governance: restrict what skills can do (especially if paired with permissions)

### Combined with tool use
When paired with tool-using agent architectures, skills become:
- A bridge between high-level intent and low-level execution plans
- A structured interface for adding capabilities while keeping application logic organized

**Sources:**
- https://www.anthropic.com/news/3-5-models-and-computer-use
- https://platform.claude.com/docs/en/release-notes/overview

---

## 8) Agent security operationalization: sandboxing + progressive enforcement + least-privilege workflows

Security for tool-enabled agents is not just “the model shouldn’t do bad things.” Tool use creates an attack surface. ARMO’s guidance provides practical operational approaches, notably:
- **Sandboxing**
- **Progressive enforcement**
- Observing behavior and establishing baselines before enforcing restrictions
- **Least privilege** in permissions

### Sandboxing as containment
Sandboxing typically means:
- Restricting file system access to a working directory
- Limiting command execution privileges
- Containing network access (or gating it tightly)
- Preventing access to sensitive resources by default

Sandboxing is essential when agents can:
- Run code
- Fetch URLs
- Manipulate files
- Interact with “computer use” environments

### Progressive enforcement: calibration before lock-down
The progressive enforcement idea generally works like:
1. **Observe** the agent’s behavior under controlled conditions
2. **Establish baselines** (what actions are normal/necessary)
3. **Enforce** restrictions incrementally (tighten permissions gradually)
4. Monitor for deviations and stop/rollback when anomalies appear

This approach reduces friction during early development while still achieving eventual security posture.

### Baseline creation and iterative restriction
Baseline creation matters because the correct permissions may not be obvious at first. Progressive enforcement lets teams:
- Identify required tool calls
- Discover unnecessary risky permissions
- Reduce the permission set to the minimum needed for operation

### Least privilege in agent execution
Least privilege means:
- Only grant the permissions a given agent/tool needs
- Separate high-risk capabilities from routine workflows
- Require explicit escalation where appropriate

In a mature architecture, permissions are tied to:
- Tool identities
- Agent roles
- Task contexts
- Resource sensitivity levels

### Operational controls beyond the sandbox
Common additional controls (consistent with the progressive enforcement concept) include:
- Audit logs of tool calls and actions
- Rate limits and execution timeouts
- Malware scanning for file outputs when relevant
- Human-in-the-loop gating for high-impact actions

**Source:** https://www.armosec.io/blog/ai-agent-sandboxing-progressive-enforcement-guide/

---

## 9) Agent evaluation evolution: benchmark integrity, “gaming,” and benchmark mutation for realistic assessment

As agent systems become more interactive and tool-enabled, evaluation approaches must handle a critical problem: **benchmarks can be gamed** or become unrealistic. The agent may learn shortcuts if evaluation is too static or if the benchmark doesn’t reflect real task variability.

A 2026 call for papers highlights work on **benchmark mutation**, specifically for realistic evaluation of interactive software-engineering agents.

### Why benchmarks degrade over time
Common reasons:
- Overfitting to known tasks or evaluation formats
- Learning benchmark-specific patterns
- Exploiting quirks in dataset construction
- The gap between “benchmark world” and “real operational world” grows as agent capabilities improve

### Benchmark mutation as a solution approach
Benchmark mutation seeks to improve realism by:
- Altering benchmark instances so memorization doesn’t help
- Maintaining core requirements while changing surface form
- Better reflecting the variation agents face in production scenarios

For example, in SWE contexts:
- Tests may be renamed or modified slightly
- Project structures can shift while preserving the intent
- Build/config differences can be introduced to force robust behavior

### Implications for tool-using agents
Interactive agents:
- Use tools repeatedly
- Generate and execute code
- Interact with files and environments

Therefore, evaluation must account for:
- Iterative correction behavior
- Stability across variations
- Proper tool usage (not just final answers)
- Safety and sandbox behavior in realistic settings

### What teams should do when designing evaluation
- Avoid static “always the same” tasks
- Use mutations, perturbations, and hidden variations
- Track not only success rates but also:
  - Number of tool calls
  - Error recovery quality
  - Run stability
  - Resource usage and time-to-completion
  - Compliance with tool restrictions

**Source:** https://conf.researchr.org/details/cain-2026/cain-2026-call-for-papers/11/Saving-SWE-Bench-A-Benchmark-Mutation-Approach-for-Realistic-Agent-Evaluation

---

## 10) Agentic RAG (2026): shifting from static retrieval pipelines to agent-controlled knowledge loops

Traditional RAG (Retrieval-Augmented Generation) typically uses a static pipeline:
1. Decide a query
2. Retrieve documents once (or in a fixed manner)
3. Generate an answer with retrieved context

**Agentic RAG** changes this by making retrieval a *dynamic part of the agent loop*. Instead of a single retrieval step, the agent can:
- Decide when to retrieve
- Choose what to retrieve
- Reformulate queries based on new observations
- Use retrieval results to influence subsequent tool actions and reasoning

### What “agent-controlled retrieval” means
Agentic RAG treats the knowledge layer as something the agent operates on. That includes:
- **Planning retrieval strategy** (where to look, what to ask next)
- **Iterative retrieval** (multiple retrieval rounds)
- **Tool-mediated retrieval** (retrieval as a tool callable by the agent)
- **Observation-driven updates** (the agent updates beliefs after seeing results)

### Why this matters
Static RAG can fail when:
- The first query is wrong or incomplete
- The user question is multi-hop
- The agent needs additional context after seeing partial evidence

Agentic RAG improves robustness by:
- Allowing retrieval to adapt
- Enabling multi-step information gathering
- Supporting tool-like behavior for knowledge acquisition

### Practical design patterns
Common agentic RAG patterns include:
- Query rewriting loops (retrieve → analyze → rewrite → retrieve again)
- Evidence accumulation (keep track of which sources were used)
- Conditional retrieval (only retrieve when confidence is low)
- Retrieval plus verification (retrieve → cross-check → finalize)

### Ecosystem resources
A dedicated survey repository collects 2026 agentic RAG resources, reflecting that this area is actively researched and rapidly evolving:
- Methods, benchmarks, and architectural approaches
- Taxonomies of agentic retrieval strategies
- Implementation patterns for retrieval inside agent loops

**Source:** https://github.com/asinghcsu/AgenticRAG-Survey

---

# Summary of Key Report Takeaways (Consolidated)

- **Platform direction (OpenAI Agents SDK + Responses API)**: agents are applications with multi-step planning, tool calls, collaboration, and stateful execution.
- **Agent tooling evolution**: built-in primitives like **web search** and **computer use** are moving into unified SDK patterns.
- **Long-horizon autonomy**: agents can inspect files, edit code, run commands—supported by **native sandbox execution** for safer operation.
- **Multi-agent orchestration (Microsoft AutoGen)**: frameworks emphasize cooperative agent systems, with ecosystem consolidation trends affecting how developers structure orchestration layers.
- **Core architecture baseline (ReAct)**: persistent “reasoning + acting” loop with tool use as a central abstraction.
- **Safety must be structural**: safety constraints should not rely on prompt-only or language-level control mechanisms; enforcement must be decoupled from textual instructions.
- **Capability extension (Anthropic Agent Skills + computer use)**: modular “skills” bundle instructions/scripts for reusable capability expansion.
- **Security operationalization**: sandboxing plus progressive enforcement and least-privilege permissions provide practical containment for tool-enabled agents.
- **Evaluation must evolve**: benchmark integrity and “gaming” require more realistic approaches such as **benchmark mutation**.
- **Agentic RAG**: retrieval is no longer static; it becomes a controllable component inside the agent’s loop.

If you want, I can also reorganize this report into a formal “sections + subsections + tables” structure (e.g., comparing each platform’s architecture, tooling, safety model, and evaluation stance in a matrix).
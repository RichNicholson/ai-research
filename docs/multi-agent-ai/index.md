# Multi-Agent AI Systems

Multi-agent AI systems coordinate multiple LLM-powered agents to tackle tasks that exceed what a single model call can handle. They are one of the most discussed --- and most debated --- patterns in applied AI today.

The core idea is division of labour: instead of one monolithic prompt doing everything, you decompose work across specialised agents that plan, research, write, review, or debug in concert. When it works, you get better results through parallel exploration, adversarial review, and focused context. When it does not, you get compounding errors, ballooning token costs, and agents arguing over JSON formatting at 3am.

This section covers the landscape as of early 2026 --- frameworks, protocols, patterns, community opinion, and the evidence for when multi-agent actually helps.

## Key Concepts

### Workflows vs. Agents

Anthropic draws a useful distinction between two categories of agentic system ([source](https://www.anthropic.com/engineering/building-effective-agents)):

- **Workflows**: LLMs and tools orchestrated through *predefined code paths* --- deterministic, developer-controlled.
- **Agents**: Systems where LLMs *dynamically direct their own processes and tool usage*, maintaining autonomous control over execution.

Most production systems are workflows, not agents. The distinction matters because the failure modes are different: workflows fail predictably, agents fail creatively.

### The Spectrum of Complexity

Anthropic's "Building Effective Agents" guide lays out a complexity spectrum that serves as a useful mental model:

| Approach | Control | Flexibility | Best For |
|---|---|---|---|
| Single LLM call + retrieval | Developer | Low | Most applications |
| Prompt chaining | Developer | Low | Sequential, decomposable tasks |
| Routing | Developer | Medium | Categorisable inputs |
| Parallelisation | Developer | Medium | Independent subtasks |
| Orchestrator-workers | Shared | High | Unpredictable subtask decomposition |
| Evaluator-optimizer | Shared | Medium | Tasks with clear quality criteria |
| Autonomous agents | Model | Highest | Open-ended, multi-step tasks |

!!! warning "Start simple"
    Anthropic's central recommendation: find "the simplest solution possible, and only increase complexity when needed." The most successful implementations they observed used "simple, composable patterns" rather than heavyweight frameworks. Many tasks that seem to need multi-agent can be solved with a well-crafted single prompt plus retrieval.

### Coordination Patterns

From academic surveys and production systems, several recurring coordination patterns emerge:

- **Orchestrator-workers**: A lead agent decomposes tasks and delegates to specialist workers. This is what Claude Code uses --- a coordinator dispatches to explorer, implementer, tester, and reviewer subagents.
- **Pipeline/sequential**: Agents process work in a fixed order, each building on the previous output. Think spec → plan → code → review.
- **Parallel/map-reduce**: Multiple agents explore in parallel, then a synthesiser combines their findings. Used for research, code review from multiple angles, and hypothesis testing.
- **Debate/adversarial**: Agents argue opposing positions while a judge evaluates. Research shows this encourages "divergent thinking" that single-agent reflection cannot achieve ([Liang et al., EMNLP 2024](https://arxiv.org/abs/2305.19118)).
- **Hierarchical**: Nested coordination where a manager delegates to team leads who delegate to workers. Microsoft's Magentic-One uses this with a central Orchestrator directing four specialist agents.
- **Reflection/self-critique**: An evaluator agent reviews output from a generator and provides feedback in a loop, iterating until quality criteria are met.

### Communication Patterns

How agents actually talk to each other:

| Pattern | Description | Example |
|---|---|---|
| Direct message passing | Orchestrator sends tasks to workers, workers return results | Claude Code subagents |
| Shared memory / blackboard | Agents read and write to a common state store | Magentic-One's Task Ledger and Progress Ledger |
| Pub/sub | Agents subscribe to topics and react to events | AutoGen Core's topic-based messaging |
| Structured handoffs | One agent passes control to another with context | OpenAI Swarm's handoff mechanism |
| Conversation threads | Agents share a chat history | CrewAI's sequential process |

## The Framework Landscape

The multi-agent framework space is crowded and fast-moving. Here are the major players as of early 2026:

| Framework | By | Approach | Language | Maturity |
|---|---|---|---|---|
| **LangGraph** | LangChain | State graphs with nodes and edges | Python, JS | Adopt (Thoughtworks Radar) |
| **CrewAI** | CrewAI | Role-based crews with tasks | Python | Widely used |
| **AutoGen** | Microsoft | Actor model, event-driven | Python, .NET | Active development |
| **OpenAI Agents SDK** | OpenAI | Evolved from Swarm | Python | Production |
| **Claude Code / Agent SDK** | Anthropic | Subagents + agent teams | Python, CLI | Production |
| **Agent Squad** | AWS Labs | Classifier-based routing | Python, TS | 7.6k GitHub stars |
| **Mastra** | Mastra | TypeScript-native | TypeScript | Growing |

!!! info "Thoughtworks Technology Radar (Vol. 33)"
    LangGraph is at **Adopt**. MCP is at **Assess** for platforms. The radar highlights "Team of coding agents" as an emerging technique to **Assess**, and warns against "Naive API-to-MCP conversion" as an antipattern on **Hold**. The overarching theme: "The Rise of Agents Elevated by MCP."

### Framework Approaches Compared

**LangGraph** models workflows as state machines --- directed graphs where nodes are computation steps and edges define transitions. This gives you fine-grained control over flow but requires more upfront design.

**CrewAI** takes a role-playing metaphor: you define agents with roles, backstories, and goals, then assign them tasks within a crew. Supports sequential and hierarchical execution with a manager agent for delegation.

**AutoGen** uses the Actor model with asynchronous message passing. Agents communicate via events and can run in distributed environments across multiple programming languages. Supports patterns including group chat, handoffs, debate, and reflection.

**Claude Code** implements a coordinator-subagent model where a lead agent delegates to specialist subagents that run in isolated context windows. Agent teams extend this to fully independent Claude Code instances that can message each other and coordinate through a shared task list.

**OpenAI Swarm** (now deprecated in favour of the Agents SDK) was a lightweight, educational framework built on just two primitives: agents and handoffs. Stateless between calls, entirely client-side, and explicitly not production-ready.

## Protocols: A2A and MCP

Two complementary protocols are shaping how agents communicate and access tools:

### Model Context Protocol (MCP)

[MCP](https://modelcontextprotocol.io/) is Anthropic's open standard for connecting AI systems with data sources and tools. It follows a client-server architecture using JSON-RPC 2.0.

MCP defines three core primitives that servers expose:

- **Tools**: Executable functions (file operations, API calls, database queries)
- **Resources**: Data sources providing context (file contents, database records)
- **Prompts**: Reusable templates for LLM interactions

MCP is about giving agents *capabilities* --- access to tools and context. It does not handle agent-to-agent communication.

### Agent-to-Agent Protocol (A2A)

[A2A](https://github.com/google/A2A) is Google's open protocol for agent-to-agent communication, announced April 2025 with 50+ technology partners. It is explicitly positioned as *complementary* to MCP:

- **MCP** = provides tools and context *to* an agent
- **A2A** = enables agents to communicate and collaborate *with each other*

A2A uses HTTP, SSE, and JSON-RPC. Key concepts include Agent Cards (JSON descriptors advertising capabilities), a task lifecycle model with artifacts as outputs, and support for long-running tasks with human-in-the-loop.

!!! note "The emerging stack"
    The Thoughtworks Technology Radar notes that "A2A and AG-UI are reducing the boilerplate required to build and scale user-facing multi-agent applications." The direction of travel is toward a layered protocol stack: MCP for tool integration, A2A for agent interoperability, and AG-UI for user-facing agent interactions.

## When to Use Multi-Agent (and When Not To)

This is the most important question. The research and practitioner consensus is clear: **multi-agent is usually overkill.**

### Use multi-agent when

- **Tasks decompose into genuinely independent subtasks** --- parallel research, multi-file code changes, reviewing from different angles
- **You need adversarial review** --- having a separate critic agent catches things a single agent misses
- **Context isolation matters** --- subagents keep exploration noise out of the main conversation
- **The task is open-ended and complex** --- long-running research, multi-step investigations where the path is not predictable

### Stick to single-agent (or simpler patterns) when

- A well-crafted prompt with retrieval and in-context examples does the job
- Tasks are sequential and dependent --- the overhead of coordination is not justified
- You need low latency and cost control
- The "multi-agent" design is really just prompt chaining with extra steps

!!! tip "The practitioner's heuristic"
    From Anthropic's multi-agent research system: "Simple queries get 1 agent with 3-10 tool calls; complex research might use 10+ subagents." Scale effort to complexity --- do not use 10 agents where one will do.

### Evidence That Multi-Agent Helps

Anthropic's multi-agent research system outperformed single-agent Claude Opus 4 by **90.2%** on internal evaluations. Andrew Ng's team found GPT-3.5 with an agent loop achieved **95.1%** on HumanEval, compared to 48.1% for zero-shot GPT-3.5 and 67.0% for zero-shot GPT-4 ([source](https://www.deeplearning.ai/the-batch/how-agents-can-improve-llm-performance/)).

But the Multi-Agent Debate paper (Liang et al.) also found that "moderate level of disagreement yields the best results" --- too little or too much inter-agent contention both hurt performance. More agents is not automatically better.

## Taxonomies from Research

Academic surveys provide useful classification frameworks:

**Guo et al. (2024)**, the most-cited survey at 802 citations, organises the field around agent profiling, communication mechanisms, and capability growth.

**Tran et al. (2025)**, with 371 citations, characterises collaboration along five dimensions: actors, types (cooperation, competition, coopetition), structures (peer-to-peer, centralised, distributed), strategies (role-based, model-based), and coordination protocols.

The **financial multi-agent systems taxonomy** (Nguyen & Pham, 2026) proposes four dimensions: architecture pattern, coordination mechanism, memory architecture, and tool integration. They introduce the Coordination Primacy Hypothesis --- the idea that coordination quality matters more than individual agent capability.

These taxonomies share a common thread: the way agents coordinate matters at least as much as how capable each individual agent is.

# Practical Guide to Multi-Agent AI Systems

This guide covers how to choose a framework, which patterns to use, and the hard-won lessons from practitioners who have built multi-agent systems in production.

## Choosing a Framework

The right choice depends on your language, your need for control, and whether you want a framework at all.

### Decision Matrix

| If you need... | Consider |
|---|---|
| Fine-grained control over agent flow | **LangGraph** --- state graphs give you explicit control over transitions and conditional routing |
| Quick prototyping with role-based agents | **CrewAI** --- the crew/agent/task metaphor is intuitive and gets you running fast |
| Distributed, event-driven architecture | **AutoGen Core** --- Actor model with pub/sub, gRPC runtime for cross-language agents |
| Minimal abstraction, just LLM + tools | **Direct API calls** --- Anthropic recommends starting here before reaching for frameworks |
| TypeScript-native development | **Mastra** --- built for TS with framework integrations (Next.js, Astro, SvelteKit) |
| AWS ecosystem integration | **Agent Squad** --- classifier-based routing with Bedrock, Lex, and other AWS services |
| Production coding agents | **Claude Code** --- subagents and agent teams with built-in context management |

!!! warning "Framework caution"
    Anthropic's "Building Effective Agents" guide warns that frameworks "often create extra layers of abstraction that can obscure the underlying prompts." They advise starting with direct LLM API calls, since many patterns need only a few lines of code. When using frameworks, understanding the underlying mechanics is essential --- "incorrect assumptions about what's under the hood are a common source of customer error."

### The "No Framework" Option

Many experienced practitioners skip frameworks entirely. The Hacker News post "Sick of AI Agent Frameworks" captured a common sentiment: "i can literally do everything what they offer spending a few hours with cursor, and better."

A minimal agent loop is just:

```python
while not done:
    response = llm.complete(messages, tools=tools)
    if response.has_tool_calls:
        results = execute_tools(response.tool_calls)
        messages.append(response)
        messages.extend(results)
    else:
        done = True
```

The value of frameworks comes from handling the edges: state persistence, error recovery, distributed execution, and observability. If you do not need those, direct API calls are simpler and more transparent.

## Pattern Catalogue

### 1. Orchestrator-Workers

A lead agent breaks down a task, delegates to specialist workers, and synthesises their results. This is the most common production pattern.

**How it works:**

1. Orchestrator receives the task and plans decomposition
2. Spawns worker agents with specific objectives
3. Workers execute independently and return results
4. Orchestrator synthesises and optionally iterates

**Example: Claude Code's architecture**

Claude Code uses this pattern with a coordinator dispatching to specialist subagents:

- **Explore** subagent (Haiku model): read-only codebase exploration
- **Plan** subagent: research for planning, no write access
- **General-purpose** subagent: complex multi-step tasks with full tool access

Each subagent runs in its own context window. The coordinator gets back a condensed summary (typically 1,000-2,000 tokens) even if the subagent used tens of thousands of tokens exploring.

**Example: Anthropic's multi-agent research system**

A LeadResearcher (Claude Opus 4) spawns multiple research subagents (Claude Sonnet 4) in parallel:

```
LeadResearcher → [Subagent A: Topic 1] → findings
                 [Subagent B: Topic 2] → findings  → Synthesise → CitationAgent → Report
                 [Subagent C: Topic 3] → findings
```

Key findings from this system:

- Outperformed single-agent Claude Opus 4 by **90.2%** on internal evals
- Parallel tool calling (3-5 subagents, each using 3+ tools simultaneously) "cut research time by up to 90%"
- Agents use ~4x more tokens than chat; multi-agent systems use ~15x

**When to use:** Complex tasks where subtasks cannot be predicted in advance. Coding tools making multi-file changes, research requiring dynamic exploration.

### 2. Pipeline / Sequential

Agents process work in a fixed order. Each stage's output becomes the next stage's input.

**How it works:**

```
Input → Agent A (spec) → Agent B (plan) → Agent C (code) → Agent D (review) → Output
```

Optional "gates" between stages validate output before passing it forward.

**Example: Software development pipeline**

One HN practitioner runs agents at each stage: spec → plan → design → code → review. Their key insight: "the arrangement of the checks between agents matters more than which model you pick for any one step."

**When to use:** Tasks that decompose cleanly into fixed, sequential subtasks. The tradeoff is higher latency for improved accuracy, since each call handles a simpler task.

### 3. Parallel / Map-Reduce

Multiple agents explore simultaneously, with results aggregated by a synthesiser.

**Two variants:**

- **Sectioning**: Independent subtasks executed simultaneously (e.g., reviewing security, performance, and test coverage in parallel)
- **Voting**: Running the same task multiple times for diverse outputs (e.g., multiple prompts independently reviewing code for vulnerabilities)

**Example: Parallel code review with agent teams**

```
Lead → [Reviewer A: security] → findings
       [Reviewer B: performance] → findings  → Lead synthesises
       [Reviewer C: test coverage] → findings
```

**When to use:** When subtasks are independent and speed matters. LLMs "generally perform better when each consideration is handled by a separate LLM call" (Anthropic).

### 4. Debate / Adversarial Review

Agents argue opposing positions while a judge evaluates, encouraging divergent thinking.

**How it works:**

1. Multiple agents independently analyse the same problem
2. Agents challenge each other's conclusions
3. A judge evaluates arguments and reaches a final decision

**Research finding:** The Multi-Agent Debate (MAD) framework found that once an LLM becomes confident in a solution, "it is unable to generate novel thoughts later through reflection even if its initial stance is incorrect." Adversarial debate breaks this pattern. PROClaim's courtroom-style debate achieved 81.7% accuracy on claim verification, outperforming standard multi-agent debate by 10 percentage points.

**When to use:** Problems where confirmation bias is a risk, such as claim verification, security auditing, or architecture decisions.

### 5. Reflection / Self-Critique

One agent generates output; another evaluates it and provides feedback in a loop.

**How it works:**

```
Generator → Output → Evaluator → Feedback → Generator → ... → Final Output
```

This is Andrew Ng's "reflection" pattern. His team showed GPT-3.5 with an iterative agent loop achieved **95.1%** on HumanEval, compared to 48.1% zero-shot.

**When to use:** When LLM responses demonstrably improve with articulated feedback, and the LLM can replicate that evaluative role. Literary translation, complex code generation, research requiring iterative refinement.

### 6. Routing

A classifier examines input and directs it to a specialised handler.

**How it works:**

```
Input → Router → [Handler A: billing questions]
                 [Handler B: technical support]
                 [Handler C: general enquiries]
```

**Example: Agent Squad (AWS)**

Uses a classifier that considers both agent descriptions and conversation history to route each request to the best-fit agent. This enables context-aware routing across multiple agent types.

**When to use:** Complex tasks with distinct categories that benefit from separate handling. Customer service triage, model selection (simple queries to cheaper models, complex ones to capable models).

## Best Practices

### Design Principles

!!! tip "Anthropic's three principles"
    1. **Simplicity** --- keep agent design minimal
    2. **Transparency** --- explicitly surface the agent's planning steps
    3. **ACI quality** --- invest heavily in tool documentation and testing

### Delegation Done Right

From Anthropic's multi-agent research system, vague delegation is a primary failure mode. "Research the semiconductor shortage" led to one subagent exploring the 2021 chip crisis while two others duplicated work on 2025 supply chains.

Instead, provide each subagent with:

- A specific objective
- Expected output format
- Tool guidance
- Clear task boundaries

### Context Management

Context is a finite, precious resource. Key techniques for long-running multi-agent work:

**Compaction**: Summarise conversations nearing the window limit. Claude Code preserves architectural decisions and unresolved bugs while discarding redundant outputs, then continues with compressed context plus the five most recent files.

**Structured note-taking**: Agents persist notes outside the context window and pull them back in later. The Claude Plays Pokemon example maintained objective tallies across thousands of steps, surviving context resets by reading its own notes.

**Sub-agent isolation**: Each subagent works in a clean context window and returns only a condensed summary. This is the most effective technique for preventing context pollution in multi-agent systems.

!!! info "When to use which"
    - **Compaction** → extensive back-and-forth conversations
    - **Note-taking** → iterative development with clear milestones
    - **Multi-agent** → complex research requiring parallel exploration

### Tool Engineering

Anthropic reports spending "more time optimizing our tools than the overall prompt" for their SWE-bench agent. Key recommendations:

- Choose formats that minimise cognitive overhead (avoid diffs requiring line counts; prefer markdown over JSON for code)
- Apply poka-yoke (error-proofing) principles --- they changed a file-editing tool from relative to absolute filepaths after discovering the model made errors when the working directory shifted
- Write tool descriptions as you would "a great docstring for a junior developer on your team"
- Consolidate frequently chained operations: instead of `list_users` + `list_events` + `create_event`, build a `schedule_event` tool

### Cost Management

Multi-agent systems are expensive. Some numbers:

- Single-agent chat: baseline token cost
- Single agent with tools: ~4x chat
- Multi-agent system: ~15x chat
- Token usage explains ~80% of performance variance on Anthropic's BrowseComp benchmark

**Strategies for controlling costs:**

- Route simple queries to cheaper models (Claude Haiku for exploration, Sonnet for implementation)
- Set maximum iteration limits on agent loops
- Use subagent isolation so exploration tokens do not pollute the main context
- Implement the Agent Firewall pattern --- a proxy that kills runaway agent loops (one practitioner lost "$47 in API calls" overnight to agents "stuck in an infinite loop arguing over JSON formatting")

### Debugging and Observability

Multi-agent systems are hard to debug because errors compound. "One step failing can cause agents to explore entirely different trajectories" (Anthropic).

**Practical approaches:**

- **Full production tracing**: Anthropic uses tracing for debugging without monitoring conversation contents
- **Rainbow deployments**: Avoid disrupting running agents during updates
- **Build for resumption**: Systems that can resume from failure points rather than restarting
- **Let agents know when tools fail** so they can adapt rather than silently retrying
- **Start with ~20 test queries** for evaluations --- early changes have dramatic effects
- **Read agent reasoning and raw transcripts carefully**: "what agents omit in their feedback and responses can often be more important than what they include"

### Harness Design for Long-Running Agents

For agents working across multiple sessions, Anthropic recommends a two-part harness:

1. **Initialiser agent** (first session): Creates a structured feature list (JSON, not markdown --- models are "less likely to inappropriately change or overwrite JSON files"), an `init.sh` script, and a progress tracking file
2. **Coding agent** (every subsequent session): Reads git logs and progress files, picks the highest-priority incomplete feature, runs smoke tests before touching anything, works on *one feature at a time*, and ends with a descriptive commit

!!! warning "Two failure modes to watch for"
    1. **One-shotting**: The agent attempts everything at once, runs out of context, and leaves half-finished work
    2. **Premature completion**: "A later agent instance would look around, see that progress had been made, and declare the job done"

## Code Examples

### Minimal Orchestrator-Worker in Python

Using the Anthropic API directly (no framework):

```python
import anthropic

client = anthropic.Anthropic()

def run_subagent(task: str, tools: list) -> str:
    """Run a focused subagent and return its summary."""
    messages = [{"role": "user", "content": task}]

    while True:
        response = client.messages.create(
            model="claude-sonnet-4-6",
            max_tokens=4096,
            messages=messages,
            tools=tools,
        )

        # Process tool calls
        if response.stop_reason == "tool_use":
            tool_results = execute_tools(response.content)
            messages.append({"role": "assistant", "content": response.content})
            messages.append({"role": "user", "content": tool_results})
        else:
            # Extract final text response
            return next(b.text for b in response.content if b.type == "text")

def orchestrate(query: str) -> str:
    """Orchestrator that decomposes and delegates."""
    # Step 1: Plan decomposition
    plan = run_subagent(
        f"Break this into 2-4 independent research subtasks: {query}",
        tools=[],
    )

    # Step 2: Run workers in parallel (simplified as sequential here)
    subtasks = parse_subtasks(plan)
    results = [run_subagent(task, tools=research_tools) for task in subtasks]

    # Step 3: Synthesise
    synthesis_prompt = f"Synthesise these findings:\n" + "\n---\n".join(results)
    return run_subagent(synthesis_prompt, tools=[])
```

### CrewAI Example

```python
from crewai import Agent, Task, Crew, Process

researcher = Agent(
    role="Senior Researcher",
    goal="Find comprehensive information on the topic",
    backstory="Expert at finding and synthesising information",
    tools=[search_tool, web_scraper],
)

writer = Agent(
    role="Technical Writer",
    goal="Write clear, accurate summaries",
    backstory="Experienced at distilling complex topics",
)

research_task = Task(
    description="Research {topic} thoroughly",
    agent=researcher,
    expected_output="Structured research notes with sources",
)

writing_task = Task(
    description="Write a summary based on the research",
    agent=writer,
    expected_output="A clear 500-word summary",
)

crew = Crew(
    agents=[researcher, writer],
    tasks=[research_task, writing_task],
    process=Process.sequential,  # or Process.hierarchical
)

result = crew.kickoff(inputs={"topic": "multi-agent AI systems"})
```

### Claude Code Subagent Definition

```markdown
---
name: researcher
description: Research specialist for exploring topics. Use proactively for research tasks.
tools: Read, Grep, Glob, Bash
model: haiku
---

You are a research specialist. When invoked:
1. Understand the research question
2. Search broadly first, then narrow to authoritative sources
3. Return structured findings with source references

Prefer authoritative sources over SEO-optimised content farms.
Return concise, factual summaries --- not exhaustive dumps.
```

### AutoGen Group Chat

```python
from autogen_agentchat.agents import AssistantAgent
from autogen_agentchat.teams import RoundRobinGroupChat
from autogen_ext.models.openai import OpenAIChatCompletionClient

model = OpenAIChatCompletionClient(model="gpt-4o")

researcher = AssistantAgent("researcher", model_client=model,
    system_message="You research topics thoroughly.")
critic = AssistantAgent("critic", model_client=model,
    system_message="You critically evaluate research for gaps and errors.")
writer = AssistantAgent("writer", model_client=model,
    system_message="You write clear summaries from reviewed research.")

team = RoundRobinGroupChat([researcher, critic, writer], max_turns=6)
result = await team.run(task="Analyse the state of multi-agent AI systems")
```

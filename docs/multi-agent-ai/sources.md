# Sources

All references used in this research, organised by category.

## Official Documentation and Guides

### Anthropic

- [Building Effective Agents](https://www.anthropic.com/engineering/building-effective-agents) --- Anthropic's foundational guide to agentic patterns, the complexity spectrum from single prompts to autonomous agents, and when to use each pattern. By Erik Schluntz and Barry Zhang, December 2024.
- [How We Built Our Multi-Agent Research System](https://www.anthropic.com/engineering/multi-agent-research-system) --- Detailed writeup of Anthropic's orchestrator-worker research system, including what worked, what did not, and quantitative findings. June 2025.
- [Effective Context Engineering for AI Agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) --- Context management techniques including compaction, structured note-taking, and sub-agent architectures. September 2025.
- [Writing Effective Tools for Agents](https://www.anthropic.com/engineering/writing-tools-for-agents) --- Five principles for tool design, including namespace clarity, token efficiency, and prompt-engineering tool descriptions. September 2025.
- [Effective Harnesses for Long-Running Agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents) --- Two-part harness architecture (initialiser + coding agent) for multi-session agent work. November 2025.
- [Claude Code Sandboxing](https://www.anthropic.com/engineering/claude-code-sandboxing) --- Security model for agentic coding, filesystem and network isolation. October 2025.
- [Harness Design for Long-Running Application Development](https://www.anthropic.com/engineering/harness-design-long-running-apps) --- Structured feature lists, progress tracking, and incremental development patterns. March 2026.
- [Claude Code: Custom Subagents](https://code.claude.com/docs/en/sub-agents) --- Documentation for creating and configuring subagents in Claude Code with tool restrictions, model selection, and permission modes.
- [Claude Code: Agent Teams](https://code.claude.com/docs/en/agent-teams) --- Documentation for orchestrating multiple Claude Code instances with shared task lists, inter-agent messaging, and coordinated work.
- [Claude Code Overview](https://code.claude.com/docs/en/overview) --- Overview of Claude Code's capabilities including subagents and agent teams.

### Google / A2A Protocol

- [A2A Protocol Announcement Blog Post](https://blog.google/technology/google-cloud/agent-to-agent-a2a-protocol/) --- Google Cloud blog post announcing A2A with partner list and design principles. April 2025.
- [A2A GitHub Repository](https://github.com/google/A2A) --- Protocol specification, SDKs (Python, Go, JavaScript, Java, .NET), and samples. Apache 2.0 license under the Linux Foundation.
- [A2A Samples Repository](https://github.com/a2aproject/a2a-samples) --- Demonstration implementations of the A2A protocol.

### Model Context Protocol (MCP)

- [MCP Architecture Overview](https://modelcontextprotocol.io/docs/concepts/architecture) --- Core architecture including client-server model, layers, primitives (tools, resources, prompts), and transport mechanisms.
- [MCP Specification](https://modelcontextprotocol.io/specification/latest) --- Full protocol specification. Current version: 2025-11-25.
- [MCP Announcement](https://www.anthropic.com/news/model-context-protocol) --- Original announcement of MCP as an open standard for connecting AI systems with data sources. November 2024.

### Microsoft / AutoGen

- [AutoGen Documentation](https://microsoft.github.io/autogen/stable/) --- Framework documentation covering AutoGen Studio, AgentChat, Core API, and Extensions.
- [AutoGen Core Concepts](https://microsoft.github.io/autogen/stable/core-user-guide/core-concepts.html) --- Actor model communication, distributed runtime, topic/subscription patterns, and design patterns (concurrent agents, sequential workflow, group chat, handoffs, debate, reflection).

### CrewAI

- [CrewAI Documentation](https://docs.crewai.com/) --- Official documentation covering crews, agents, tasks, processes (sequential and hierarchical), memory, and execution methods.

### OpenAI Swarm

- [Swarm GitHub Repository](https://github.com/openai/swarm) --- Educational framework for multi-agent orchestration (now deprecated in favour of OpenAI Agents SDK). Covers agents, handoffs, context variables, and streaming.

### AWS

- [Agent Squad (formerly Multi-Agent Orchestrator)](https://github.com/awslabs/agent-squad) --- AWS Labs framework for classifier-based multi-agent routing. Available in Python and TypeScript.

### Mastra

- [Mastra Documentation](https://mastra.ai/docs) --- TypeScript-native AI agent framework with framework integrations.

## Industry Analysis

### Thoughtworks Technology Radar

- [Thoughtworks Technology Radar Vol. 33](https://www.thoughtworks.com/radar) --- Key agent-related entries:
    - **LangGraph**: Adopt (Languages & Frameworks)
    - **NeMo Guardrails**: Adopt (Tools)
    - **Claude Code**: Trial (Tools)
    - **AGENTS.md**: Trial (Techniques)
    - **Context7**: Trial (Tools)
    - **Team of coding agents**: Assess (Techniques)
    - **Context engineering**: Assess (Techniques)
    - **MCP**: Assess (Platforms)
    - **A2A Protocol**: Assess (Platforms)
    - **Toxic flow analysis for AI**: Assess (Techniques)
    - **Naive API-to-MCP conversion**: Hold (Techniques)
    - Theme: "The Rise of Agents Elevated by MCP"

### Andrew Ng

- [Agentic Design Patterns](https://www.deeplearning.ai/the-batch/how-agents-can-improve-llm-performance/) --- Andrew Ng's framework of four agentic patterns (reflection, tool use, planning, multi-agent collaboration) and HumanEval benchmark results showing GPT-3.5 with agent loop achieving 95.1%.

## Academic Papers

### Major Surveys

- **Guo et al. (2024)** --- "Large Language Model based Multi-Agents: A Survey of Progress and Challenges." 802 citations. Covers agent profiling, communication, and capability growth. [arXiv:2401.????](https://arxiv.org/search/?searchtype=all&query=LLM+multi-agent+survey+Guo)
- **Tran et al. (2025)** --- "Multi-Agent Collaboration Mechanisms: A Survey of LLMs." 371 citations. Five-dimension framework: actors, types, structures, strategies, coordination protocols. Applications in 5G/6G networks, Industry 5.0, question answering.
- **Yan et al. (2025)** --- "Beyond Self-Talk: A Communication-Centric Survey of LLM-Based Multi-Agent Systems." 51 citations. Communication-focused framework covering system-level and internal communication.
- **Sarkar & Sarkar (2025)** --- "Survey of LLM Agent Communication with MCP: A Software Design Pattern Centric Review." 14 citations. Applies Mediator, Observer, Publish-Subscribe, and Broker patterns to MCP.

### Notable Research Papers

- **Liang et al. (2024)** --- "Encouraging Divergent Thinking in Large Language Models through Multi-Agent Debate." EMNLP 2024. Identifies the Degeneration-of-Thought problem and proposes the MAD framework. [arXiv:2305.19118](https://arxiv.org/abs/2305.19118)
- **Chowdhury et al. (2026)** --- "PROClaim: Courtroom-Style Multi-Agent Debate with Progressive RAG." 81.7% accuracy outperforming standard debate by 10 points. [arXiv:2603.28488](https://arxiv.org/abs/2603.28488)
- **Shen et al. (2026)** --- "Empirical Study of Multi-Agent Collaboration for Automated Research." Benchmarks single-agent vs subagent vs agent team paradigms under fixed compute. [arXiv:2603.29632](https://arxiv.org/abs/2603.29632)
- **Nguyen & Pham (2026)** --- "Toward Reliable Evaluation of LLM-Based Financial Multi-Agent Systems." Four-dimensional taxonomy and Coordination Primacy Hypothesis. [arXiv:2603.27539](https://arxiv.org/abs/2603.27539)
- **Chen et al. (2026)** --- "The Five Ws of Multi-Agent Communication." TMLR 2026. Reviews communication across MARL, Emergent Language, and LLM-based paradigms. [arXiv:2602.11583](https://arxiv.org/abs/2602.11583)
- **Wibowo & Polyzos (2025)** --- "Toward a Safe Internet of Agents." Architectural framework for safety at single-agent, MAS, and interoperable MAS levels. [arXiv:2512.00520](https://arxiv.org/abs/2512.00520)

### Agent Safety Research (Anthropic)

- [Agentic Misalignment: How LLMs Could Be Insider Threats](https://www.anthropic.com/research/agentic-misalignment) --- June 2025
- [SHADE-Arena: Evaluating Sabotage and Monitoring in LLM Agents](https://www.anthropic.com/research/shade-arena-sabotage-monitoring) --- June 2025
- [Measuring AI Agent Autonomy in Practice](https://www.anthropic.com/research/measuring-agent-autonomy) --- February 2026
- [Mitigating the Risk of Prompt Injections in Browser Use](https://www.anthropic.com/research/prompt-injection-defenses) --- November 2025

### Benchmarks

- **NARCBench** --- Multi-agent collusion detection. [arXiv:2604.01151](https://arxiv.org/abs/2604.01151)
- **AgentSocialBench** --- Privacy risk in multi-agent social networks. [arXiv:2604.01487](https://arxiv.org/abs/2604.01487)
- **MP-Bench** --- Multi-perspective failure attribution. [arXiv:2603.25001](https://arxiv.org/abs/2603.25001)
- **SOC-bench** --- Security operation capabilities of multi-agent systems. [arXiv:2603.28998](https://arxiv.org/abs/2603.28998)
- **SafeClaw-R** --- Safety enforcement for multi-agent personal assistants. [arXiv:2603.28807](https://arxiv.org/abs/2603.28807)

### Microsoft Research

- [Magentic-One](https://www.microsoft.com/en-us/research/articles/magentic-one-a-generalist-multi-agent-system-for-solving-complex-tasks/) --- Hierarchical orchestration system built on AutoGen with Orchestrator directing four specialist agents via Task Ledger and Progress Ledger patterns.

## Community Discussions

### Hacker News

- [Ask HN: How are you using multi-agent AI systems in your daily workflow?](https://news.ycombinator.com/item?id=47270020) --- 16 points, 16 comments. The PAI Family discussion with detailed practitioner experiences.
- [PolyThink: A Multi-Agent AI System to Eliminate Hallucinations](https://news.ycombinator.com/item?id=43714433) --- 9 points, 10 comments. Debate on multi-model consensus approaches.
- [Klaw.sh: Kubernetes for AI Agents](https://news.ycombinator.com/item?id=47025478) --- 60 points, 43 comments. Highest-engagement agent infrastructure discussion.
- [Sick of AI Agent Frameworks](https://news.ycombinator.com/item?id=42691946) --- 15 points, 7 comments. Framework fatigue sentiment.
- [Ask HN: Do you think AI Agents are overhyped?](https://news.ycombinator.com/item?id=40646631) --- 20 points, 32 comments. The definitive hype debate.
- [Agent Firewall: Go proxy to kill LLM death spirals](https://news.ycombinator.com/item?id=47308378) --- The $47 overnight loop story.
- [Orloj: Agent infrastructure as code](https://news.ycombinator.com/item?id=47526813) --- 20 points, 12 comments. YAML and GitOps for agents.

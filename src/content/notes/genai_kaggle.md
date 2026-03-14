---
title: "Kaggle 5 Days in Gen AI - Study Notes"
description: "Study notes from Kaggle's 5 Days in Gen AI class covering agents, tools, MCP, and agent quality evaluation."
pubDate: 2026-03-14
---

## Day 1 — Intro to Agents

### Reading Notes: Introduction to Agents Whitepaper

A paradigm shift is underway: moving from AI that just predicts or creates content to a new class of software capable of autonomous problem-solving and task execution.

An AI agent can be defined as the combination of **models, tools, an orchestration layer, and runtime services** which uses the LM in a loop to accomplish a goal.

- **The model (brain):** the core language model (LM) or foundation model that serves as the agent's central reasoning engine.
- **The tools (hands):** mechanisms that connect the agent's reasoning to the outside world, enabling actions beyond text generation. API extensions, code functions, and data stores for accessing real-time, factual info.
- **The orchestration layer (nervous system):** breaks down complex goals into steps and decides when to think versus use a tool.
- **Deployment (the body and legs)**

> *A LLM's capacity to do anything makes it difficult to compel it to do one specific thing reliably and perfectly.*

### The Agentic Problem-Solving Process

Five fundamental steps:

1. Get the mission
2. Scan the scene
3. Think it through — analyze the mission against the scene and devise a plan
4. Take action
5. Observe and iterate

This think, act, and observe cycle continues — managed by the **orchestration layer**, reasoned by the **model**, and executed by the **tools** until the agent's internal plan is complete and the initial **mission** is achieved.

### A Taxonomy of Agentic Systems

**Level 0 — The Core Reasoning System**
A LM operates in isolation, responding solely based on its vast pre-trained knowledge without any tools, memory, or interaction with the live environment.

**Level 1 — The Connected Problem-Solver**
The reasoning engine becomes a functional agent by connecting to and utilizing external tools. Problem-solving is no longer confined to static, pre-trained knowledge.

**Level 2 — The Strategic Problem-Solver**
The key skill is **context engineering**: the agent's ability to actively select, package, and manage the most relevant info for each step of its plan.

**Level 3 — The Collaborative Multi-Agent Systems**
Moving away from a single all-powerful super agent toward a team of specialists working in concert. Agents treat other agents as tools.

**Level 4 — The Self-Evolving System**
An agentic system identifies gaps in its own capabilities and dynamically creates new tools or even new agents to fill them.

### Core Agent Architecture: Model, Tools, and Orchestration

#### Model: The Brain

The selection of the LM dictates the agent's cognitive capabilities, operational cost, and speed. Real-world success demands a model that excels at **agentic fundamentals: superior reasoning to navigate complex, multi-step problems and reliable tool use to interact with the world.**

Best practices:
- Start by defining the business problem, then test models against metrics that directly map to that outcome
- Choose more than one model to optimize both performance and cost
- Invest in a robust CI/CD pipeline that continuously evaluates new models against your key business metrics

#### Tools: The Hands

Tools connect the agent's reasoning to reality, allowing it to move beyond static training data. A robust tool interface is **a three-part loop: defining what a tool can do, invoking it, and observing the result.**

1. **Retrieving info:** RAG gives the agent a library card to query external knowledge, often stored in vector databases or knowledge graphs.
2. **Executing actions**
3. **Function calling:** requires clear instructions, secure connections, and orchestration. Open standards like [MCP](https://github.com/modelcontextprotocol/) have become popular for simpler discovery and connection to tools.

#### The Orchestration Layer

The engine that runs the "think, act, observe" loop. Core design choices:

1. **Degree of autonomy:** from deterministic, predictable workflows to having the LM dynamically adapt, plan, and execute tasks
2. **Implementation method:** no-code builders to code-first frameworks (e.g., [Google's ADK](https://google.github.io/adk-docs/))

**Requirements for a good orchestration framework:**
- Open — allows plugging in any model or tool to prevent vendor lock-in
- Precise control — enables a hybrid approach where non-deterministic LM reasoning is governed by hard-coded business rules
- Built for observability — generates detailed traces and logs exposing the entire reasoning trajectory

### Agent Interoperability

- **Agents ↔ Human**
- **Agents ↔ Agents:** The A2A protocol allows any agent to publish a digital business card (an **Agent Card**) — a JSON file advertising the agent's capabilities, network endpoint, and security credentials. Interactions are framed as asynchronous tasks.

---

## Day 2 — Agent Tools and Interoperability with MCP

### Tools and Tool Calling

A tool is a function or program an LLM-based application can use to accomplish a task outside the model's capabilities. *The model generates content; tools let the application interact with other systems.*

### Types of Tools

- **Function tools**
- **Built-in tools**
- **Agent tools:** an agent invoked as a tool

### Taxonomy of Agent Tools

- Information retrieval
- Action/execution
- System/API integration
- Human in the loop

### Best Practices

#### Documentation is Important

- Use a clear name
- Describe all input and output parameters
- Simplify parameter lists
- Clarify tool descriptions
- Add targeted examples
- Provide default values

#### Describe Actions, Not Implementations

The model's instructions should describe actions, not specific tools (available tools can change dynamically):
- Describe **what**, not **how**
- Don't duplicate instructions
- Don't dictate workflows
- Do explain tool interactions — if one tool has a side-effect that may affect another, document this

#### Other Best Practices

- **Publish tasks, not API calls** — if the tool represents a specific task, the agent is more likely to call it correctly
- **Make tools as granular as possible** — keep tools concise and limited to a single function
- **Design for concise output** — don't return large responses; use external systems for data storage
- **Use validation effectively** — I/O schemas serve as both documentation and runtime checks
- **Use descriptive error messages**

### Understanding the Model Context Protocol (MCP)

MCP replaces fragmented custom integrations with a unified, plug-and-play protocol — a universal interface between AI applications and external tools and data.

**Core components:**
- **MCP Host:** the application responsible for creating and managing individual MCP clients
- **MCP Client:** a software component embedded within the host that maintains the connection with the server
- **MCP Server:** advertises available tools, receives and executes commands, formats and returns results

**Communication layer:**
- **Base protocol:** JSON-RPC as the base message format
- **Message types:** Requests, Results, Errors, Notifications
- **Transport mechanisms:**
  - *Stdio (standard input/output):* for fast, direct communication in local environments
  - *Streamable HTTP:* remote client-server protocol

**Key primitives:** The tool entity in MCP is a standardized way for a server to describe a function it makes available to clients (e.g., `read_file`, `get_weather`). MCP Servers publish a list of available tools, including descriptions and parameter schemas, for agents to discover.

---

## Day 4 — Agent Quality

Three core messages:

1. **The trajectory is the truth** — the true measure of an agent's quality lies in its entire decision-making process
2. **Observability is the foundation** — logging, tracing, and metrics
3. **Evaluation is a continuous loop**

Focus areas: **Quality · Robustness · Trustworthiness**

Agent failures are often not system crashes but **subtle degradations of quality**, emerging from the complex interplay of model weights, training data, and environmental interactions.

**Failure modes:**
- Algorithmic bias
- Factual hallucination
- Performance and concept drift
- Emergent unintended behaviors

**Three core technical capabilities that break traditional evaluation models:**
- Planning and multi-step reasoning
- Tool use and function calling
- Memory

### The Pillars of Agent Quality

An **outside-in approach** connects high-level business goals to technical performance:

- **Effectiveness (Goal Achievement):** did the agent successfully and accurately achieve the user's actual intent? This is the final measure of task success.
- **Efficiency (Operational Cost):** measured in resources consumed — total tokens (cost), latency, and trajectory complexity.
- **Robustness (Reliability):** how does the agent handle adversity? A robust agent retries failed calls, asks for clarification when needed, and reports what it couldn't do.
- **Safety & Alignment (Trustworthiness):** does the agent operate within its defined ethical boundaries and constraints?

### The Art of Agent Evaluation

#### Outside-In: Final Performance

Metrics at this stage focus on overall task completion:
- **Task success rate:** binary score of whether the final output was correct and complete
- **User satisfaction**
- **Overall quality**

#### Inside-Out: Trajectory Evaluation

Once a failure is identified, analyze the agent's approach by assessing every component of its execution trajectory:
- LLM planning (the "thought") — hallucinations, nonsensical responses, context pollution
- Tool usage
- Tool response interpretation
- RAG performance
- Trajectory efficiency and robustness (excessive API calls, high latency, redundant efforts)
- Multi-agent dynamics

#### The Evaluators

**Automated metrics** — provide speed and reproducibility; useful for regression testing. Examples: string-based similarity, embedding-based similarity, task-specific benchmarks. *Implement as the first quality gate in the CI/CD pipeline.*

**LLM-as-a-judge** — use a powerful model to evaluate outputs of another agent. Provide the judge with the agent's output, the original prompt, the golden answer, and a detailed rubric. **Prioritize pairwise comparison over single-scoring.**

**Agent-as-a-judge** — use one agent to evaluate the full execution trace of another. Key evaluation dimensions: plan quality, tool use, context handling.

**Human-in-the-loop (HITL)** — indispensable for establishing a human-calibrated benchmark. Evaluates domain expertise, nuance interpretation, and creating the golden set.

**User feedback** — capture real-world feedback with low-friction mechanisms (thumbs up/down, short comment) and governance dashboards.

### From Monitoring to True Observability

#### The Kitchen Analogy

Traditional software is a **line cook** — monitoring is a checklist.
An AI agent is a **gourmet chef** in a mystery box challenge — observability is how a food critic judges the chef, examining not just the final dish but the entire process and reasoning.

#### The 3 Pillars of Observability

**1. Tracing**
Follows a single task from the initial user query to the final answer, stitching individual logs (spans) into a complete end-to-end view. Reveals the crucial "why" by showing the causal relationship between events.

Key elements:
- **Spans**
- **Attributes**
- **Context propagation:** links spans together via a unique `trace_id`

**2. Metrics**

*System metrics:*
- Performance: latency, error rate
- Cost: tokens per task, API cost per run
- Effectiveness: task completion rate, tool usage frequency

*Quality metrics:*
- Correctness and accuracy
- Trajectory adherence
- Safety and responsibility
- Helpfulness and relevance

**3. Logs**

Best practice — **dynamic sampling:** use high-granularity logging (debug level) in development. In production, set a lower default log level (info) but implement dynamic sampling — e.g., trace only 10% of successful requests but 100% of all errors.

#### Plugins and Callbacks

A [plugin](https://google.github.io/adk-docs/plugins/) is a custom code module that runs automatically at various stages of the agent's lifecycle. Plugins are composed of *callbacks* — Python functions that run at specific points in the agent's lifecycle, providing hooks to interrupt an agent's flow.

---

## RAG References — Study Notes

### 1. The Foundational Paper

**"Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks"**
*Lewis et al. (Facebook AI Research / NYU / UCL, 2020)*

The genesis paper for RAG. Introduced combining **Parametric Memory** (what the AI learned in training) with **Non-Parametric Memory** (the Knowledge Base). Found that RAG models produce responses significantly more "factual, specific, and diverse" than models relying on internal knowledge alone.

### 2. Clinical Evidence

**"Retrieval-augmented AI can eliminate hallucinations in clinical LLMs"**
*Wada et al. (Juntendo University, Japan — npj Digital Medicine, 2025)*

Tested a RAG-enhanced model in radiology consultations. Found that RAG could reduce "dangerous hallucinations" to **zero** by forcing the AI to retrieve from a curated knowledge base of international guidelines.

### 3. The Stochastic Decoding Problem

**"Can Knowledge Graphs Reduce Hallucinations in LLMs? A Survey"**
*Zheng et al. (ACL Anthology, 2024)*

Explains that hallucinations stem from the **probabilistic nature** of LLMs (predicting the next likely word). By integrating a Knowledge Base, you move the model from a "probabilistic" mode to a "verifiable" mode, where the input context limits the search space to the facts provided.

### 4. RAG vs. Fine-Tuning

**"RAG-HAT: A Hallucination-Aware Tuning Pipeline for LLM in Retrieval-Augmented Generation"**
*Various authors (EMNLP Industry Proceedings, 2024)*

Demonstrates that standard LLMs distort or misinterpret content when they hit their "knowledge boundary." Confirms that RAG is more effective than fine-tuning for factual accuracy because fine-tuning doesn't solve the "data freshness" problem — only a Knowledge Base can provide the required "source of truth."

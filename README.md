# AI agent reading list

_A curated reading list for AI agents._

Last reviewed August 2026.

The same few sources keep resurfacing in most writing about AI agents. This page collects the sources cited most often and relied on most. It groups them by what each contributes and notes how well regarded each is.

The order runs from the immediately useful to the foundational, then to production, and finally to reference material. The engineering guides from Anthropic and OpenAI define how agents are designed in practice and give the vocabulary to build with. The harness is everything around the model that makes it useful, from tools and memory to orchestration and verification. The term _harness engineering_ appeared in 2026, and the discipline builds on the guides' language.

A few years of concentrated research produced the underlying ideas. The range runs from reasoning and self-improvement to tool use, the agent-computer interface (the boundary through which an agent perceives and acts on a computer), memory, lifelong learning, and multi-agent collaboration. The model papers behind those ideas describe the capabilities that agents depend on. Governance documents become essential the moment agents connect to production systems. Protocols connect agents to the outside world, and a few books give the field a durable foundation.

> [!NOTE]
> Compiled with AI assistance and reviewed by the author, [@edsvo](https://github.com/edsvo). Verify claims against the linked sources before relying on them.

## The engineering guides

Two provider guides open this section, covering the same design space in different ways. Anthropic's guide is a taxonomy: three building blocks, five workflow patterns, and a clear line between workflows and agents. The taxonomy is deliberately framework-independent. Workflows fix control flow in code; agents leave control flow to the model. OpenAI's guide is a build guide: three pillars (model, tools, and instructions), orchestration from a single agent to decentralized handoffs, and the Agents SDK. Guardrails place constraints on agent behavior.

Both give the same advice: start with the simplest thing that works and add complexity only when it demonstrably helps. Read together, they give you the terms for naming an agent design and the path for building it. The rest of the section follows that vocabulary into production practice, capability framing, and dissent.

### Building Effective Agents

_Erik Schluntz and Barry Zhang, Anthropic, December 2024._

Anthropic's [Building Effective Agents][anthropic-agents] defines the widely cited taxonomy of agent design. The taxonomy has three building blocks: an augmented large language model _(LLM)_, workflows, and agents. It also has five composable workflow patterns: prompt chaining, routing, parallelization, orchestrator-workers, and evaluator-optimizer. The post draws on Anthropic's experience building agents and working with dozens of teams.

It's a commonly recommended starting point for agent engineering. The workflows-versus-agents distinction and the five pattern names have become the field's common vocabulary for workflow design. The documentation from OpenAI, LangChain, and Google Cloud makes the same distinction, and independent guides cite the post as the key source. A dedicated third-party site, [Building Effective Agents: An Engineering Reference][agents-reference-site], builds a full engineering resource around it.

The reception was immediate. The day after publication, the AI blogger Simon Willison wrote in [Building effective agents][simon-willison] that the post was "the clearest practical guide to building systems that combine multiple LLM calls that I've seen anywhere." The regard is earned but qualified. The taxonomy comes from Anthropic's production experience, not from an empirically validated standard. Much later writing reads the sharp workflows-versus-agents binary as a spectrum, borrowing the post's vocabulary while softening the boundary.

### A Practical Guide to Building Agents

_OpenAI, April 2025._

[A Practical Guide to Building Agents][openai-guide] is a builder-first guide organized around three agent pillars: model, tools, and instructions. It covers orchestration (single-agent systems, the manager pattern, and decentralized handoffs), guardrails, and the OpenAI Agents SDK. It defines an agent as a system that manages its own workflow execution. Its central advice is to start with a single agent and add multi-agent complexity only when the task demands it. It complements Anthropic's guide with operational and safety depth.

Read it alongside Anthropic's guide as the second key provider document. Where Anthropic names the workflow patterns, OpenAI frames the same design space around pillars and orchestration. Together they cover most of what a team encounters.

### How we built our multi-agent research system

_Jeremy Hadfield et al., Anthropic, June 2025._

[How we built our multi-agent research system][anthropic-multi-agent] is the production account of the orchestrator-workers pattern. It describes how Anthropic's Research feature decomposes a query into parallel search tasks. Each subagent explores with its own context window, and a lead agent synthesizes the results. On Anthropic's internal research evaluation, the multi-agent system outperformed a single agent by 90.2%. It uses roughly 15 times the tokens of a chat interaction.

The post is among the most frequently cited production evidence for multi-agent systems and the source of the cost-performance framing that later writing borrows. It also names where the pattern fits poorly: coding tasks with shared state, the territory Cognition's dissent stakes out.

### Effective context engineering for AI agents

_Prithvi Rajasekaran et al., Anthropic, September 2025._

Anthropic's [Effective context engineering for AI agents][context-engineering] introduces _context engineering_: curating and maintaining the tokens that enter an LLM's context window. It builds directly on the Building Effective Agents taxonomy, arguing that context, not prompt wording, is the primary way to steer agent behavior. It treats context as a finite resource subject to context rot, the decline in response quality as the window fills. It lays out the long-horizon techniques the harness section builds on: compaction (condensing older context), structured note-taking, and subagents that isolate context.

The post popularized the term and is the source of the context-management vocabulary—compaction, context rot, just-in-time retrieval—that later agent writing borrows.

### Four AI Agent Strategies That Improve GPT-4 and GPT-3.5 Performance

_Andrew Ng, The Batch, March 2024._

Andrew Ng's [four strategies][ng-four-strategies] are reflection, tool use, planning, and multi-agent collaboration. The post argues that agent workflows will drive more progress than the next generation of foundation models. It supports the claim with HumanEval results: GPT-4 in zero-shot mode scores 67.0%, whereas GPT-3.5 wrapped in an agent loop reaches 95.1%. The framework is often taken as the list of what agents can do.

The taxonomy ranks alongside Anthropic's as the common vocabulary for agent capabilities. The series also carries Ng's caution: reflection and tool use are the mature, reliable patterns, whereas planning and multi-agent collaboration remain less predictable.

### Don't Build Multi-Agents

_Walden Yan, Cognition, June 2025._

Cognition's [Don't Build Multi-Agents][dont-build-multi-agents] makes the dissenting case against multi-agent architectures. Walden Yan argues for a single-threaded agent with fully shared context. Multi-agent systems spread decision-making across isolated contexts, so conflicting assumptions compound. The case is grounded in Devin, the coding agent Cognition builds, where write operations share state. It is the territory Anthropic's own multi-agent post flags as a poor fit.

The dissent is taken seriously but read as workload-specific. Cognition later softened the sharp version, reporting that narrow multi-agent patterns work when additional agents contribute intelligence rather than actions.

### Related reading

The preceding entries stake out the main positions on multi-agent design. A few artifacts fill out the design space. The [Building Effective Agents Cookbook][agents-cookbook] holds minimal reference implementations of the workflows the Anthropic post describes. A white paper, [Building Effective AI Agents: Architecture Patterns and Implementation Frameworks][agents-whitepaper], repackages the same ideas for enterprise audiences, with customer case studies from Coinbase, Intercom, and Thomson Reuters. Dexter Horthy's [12 Factor Agents][12-factor-agents] turns the single-threaded preference into a practical checklist, and LlamaIndex packages it as an interactive guide.

## The harness

Where the engineering guides describe how to design an agent, harness engineering describes everything around the model that makes it useful in production. The harness includes the tools, memory, context management, orchestration, and verification loops that turn a capable model into a reliable tool. The field's shorthand is LangChain's definition: an agent is a model plus a harness. The discipline's core claim is that reliability comes from the harness, not the model.

Mitchell Hashimoto's post popularized the term in February 2026, and the major providers formalized it within months. The following sources define the discipline and show what building a harness involves. They close with Anthropic's two posts. Together they bracket the discipline: one predates the term itself, the other provides evidence that harness design drives quality.

### My AI Adoption Journey

_Mitchell Hashimoto, mitchellh.com, February 2026._

[My AI Adoption Journey][hashimoto] named the discipline. Hashimoto, the HashiCorp co-founder who created Vagrant and Terraform, documents his transition to an AI-first workflow. He distills the recurring principle behind every fix he made: when an agent makes a mistake, build a solution so it never makes that mistake again. The buildable solution—documentation, constraints, tools—is the harness. The habit of feeding every agent failure back into that environment is harness engineering.

Both the term and the discipline trace back to this post, and most later harness engineering writing treats it as the starting point.

### Harness engineering: leveraging Codex in an agent-first world

_OpenAI, February 2026._

OpenAI's [Harness engineering: leveraging Codex in an agent-first world][openai-harness] formalized the discipline. The post describes letting Codex agents generate and maintain an entire codebase. That work includes product code, tests, continuous integration configuration, developer tools, documentation, and even the scripts that manage the repository.

Humans work at a higher layer of abstraction: they prioritize work, translate user feedback into acceptance criteria, and validate outcomes. When an agent struggles, the team treats it as a signal of a missing tool, guardrail, or piece of documentation. The team then feeds that fix back into the repository.

Its closing claim is widely quoted: building software still demands discipline, but the discipline shows up more in the scaffolding than in the code.

### The Anatomy of an Agent Harness

_Vivek Trivedy, LangChain, March 2026._

Vivek Trivedy's [The Anatomy of an Agent Harness][anatomy] is the source of the field's working definition. It states the definition as an equation: an agent is a model plus a harness. It sharpens the equation with "if you're not the model, you're the harness."

The post derives the harness's components from what a raw model cannot do. Those components are system prompts, tools, memory, orchestration, compaction and context management, and verification loops. The post ends by citing the field's strongest evidence. LangChain's Deep Agents rose from the top 30 to the top 5 on Terminal Bench 2.0 by changing only the harness, keeping the same model.

The field quotes this definition most often, and the model-plus-harness formula repeats throughout.

### Harness engineering for coding agent users

_Birgitta Böckeler, martinfowler.com, April 2026._

Birgitta Böckeler's [Harness engineering for coding agent users][fowler-harness] is a deep treatment of the topic. It is also one of the first to turn the wide "everything except the model" definition into concrete coding-agent practice. Böckeler narrows the scope to coding agents.

She introduces the working vocabulary: sensors, the architecture fitness harness, the behavior harness, harness templates for common service topologies, and harnessability. Sensors are tests, linters, and other feedback mechanisms that tell an agent what it broke. Harnessability is the design quality that makes a codebase simple to wrap in a harness. A sidebar maps where harness engineering sits relative to the earlier post on context engineering.

It appears in Martin Fowler's _Exploring Generative AI_ series and is widely cited as the primary source for applying harness engineering to real codebases. It also warns that the harness is most needed where it's hardest to build: in legacy teams with accumulated technical debt.

### Effective harnesses for long-running agents

_Anthropic, November 2025._

Anthropic's first harness post, [Effective harnesses for long-running agents][anthropic-long-running], was published in November 2025, months before the term was coined. It addresses the problem that a long-running coding agent loses memory across context windows. The harness adds an initializer agent that decomposes a product spec into a feature list, plus a coding agent that implements one feature at a time. The coding agent keeps a progress log and posts structured updates so each session continues where the last one ended. This post introduced the context-reset technique that later harnesses build on.

This post is the template for long-running agent harnesses and the direct ancestor of Anthropic's later harness-design post.

### Harness design for long-running application development

_Prithvi Rajasekaran, Anthropic Labs, March 2026._

Prithvi Rajasekaran's [Harness design for long-running application development][anthropic-harness-design] is a clear demonstration that harness design, not model choice, drives quality. Drawing on generative adversarial networks, Rajasekaran built a three-agent harness: a planner, a generator, and an evaluator.

The planner expands a one-sentence prompt into a product spec. The generator implements one feature per sprint. The evaluator clicks through the running application with Playwright and grades each sprint against hard thresholds. It enforces a sprint contract that the planner and generator agree on before any code is written. On the same task, a solo agent produced an application that launched but was broken at the wiring level. The harnessed run shipped a richer, working application. The post ends with the discipline's central rule: retest the harness against each model release and delete the components that are no longer needed.

It was published on Anthropic's engineering blog and covered widely in the trade press. Together with the November 2025 post, it's strong provider evidence for harness-over-model gains.

## The research foundation

The following sources are the primary literature behind the patterns that the engineering guides name. They begin with the two overviews of the field. They then follow the introduction's order through the ideas. That order runs from reasoning and self-improvement to tool use, the agent-computer interface, memory, lifelong learning, and multi-agent collaboration. Together they are the source material most agent frameworks are built from.

### LLM Powered Autonomous Agents

_Lilian Weng, June 2023._

Lilian Weng's [LLM Powered Autonomous Agents][weng] is one of the field's best-known overviews of agent architecture. Weng frames an agent as an LLM core with three surrounding components: planning, memory, and tool use. Planning covers subgoal decomposition and reflection. Memory covers short-term context and long-term stores. Tool use covers external APIs and code execution. She shows how each component was assembled from earlier research. Written while she was at OpenAI, it's where most engineers begin.

The framing it named—planning, memory, and tool use—has become the field's shared vocabulary.

### A Survey on LLM-based Autonomous Agents

_Lei Wang et al., Frontiers of Computer Science, 2024._

Lei Wang et al.'s [A Survey on LLM-based Autonomous Agents][survey] is one of the most-cited surveys of the field. It organizes agents into construction, application, and evaluation. It catalogs how agents are built: reasoning, memory, planning, tool use, and multi-agent collaboration. It also catalogs where they are deployed. Together they give a complete picture of the design space in one place.

The survey is the starting point for a structured overview of the field.

### ReAct

_Shunyu Yao et al., Princeton and Google Research, ICLR 2023._

Shunyu Yao et al.'s [ReAct][react] introduces the Thought-Action-Observation loop. It interleaves reasoning traces with tool-driven actions and shows that the combination outperforms either alone. It excels on multi-hop question answering, fact verification, and interactive decision-making.

Most agent frameworks descend from this paper. Its loop is the shape of agent reasoning.

### Tree of Thoughts

_Shunyu Yao et al., Princeton and Google Research, NeurIPS 2023._

Shunyu Yao et al.'s [Tree of Thoughts][tot] extends chain-of-thought into a tree search over multiple reasoning paths. It generates candidates, self-evaluates states, and backtracks from dead ends. On Game of 24, GPT-4 with chain-of-thought solves 4% of tasks; with tree search it reaches 74%.

It remains the key source on planning and search-intensive reasoning and on the argument that single-path reasoning is not enough.

### Reflexion

_Noah Shinn et al., Northeastern and Princeton, NeurIPS 2023._

Noah Shinn et al.'s [Reflexion][reflexion] introduces verbal reinforcement learning. Agents reflect on failures in natural language and store the reflections in episodic memory. They improve on subsequent attempts without weight updates. On HumanEval the agents reach 91% `pass@1` (the share of problems their first generated solution solves). They reach 97% success on ALFWorld, well above the ReAct baseline.

Reflexion made self-improvement through reflection a common pattern in agent harnesses.

### Self-Refine

_Aman Madaan et al., NeurIPS 2023._

Aman Madaan et al.'s [Self-Refine][self-refine] introduces iterative self-correction without retraining. The model drafts an output, critiques its own draft, and revises. It repeats until nothing remains to fix. It improves quality by roughly 20% on average across seven tasks, from code to dialogue.

Generate-critique-revise loops trace back to it, the pattern behind most self-improving agent harnesses.

### Toolformer

_Timo Schick et al., Meta AI, NeurIPS 2023._

Timo Schick et al.'s [Toolformer][toolformer] demonstrates that LLMs can learn when and how to invoke external APIs through self-supervised learning. It keeps only the candidate calls that reduce prediction loss on subsequent tokens. The resulting model outperforms much larger ones on arithmetic and knowledge tasks. The result establishes that tool use is a learnable capability rather than a scaffolded behavior.

It underpins the tool-calling layer most agents rely on and is the key guide to teaching models to use tools.

### SWE-agent

_John Yang et al., Princeton, NeurIPS 2024._

John Yang et al.'s [SWE-agent][swe-agent] introduces the agent-computer interface, the boundary through which an agent perceives and acts on a computer. It gives an agent a carefully designed shell for viewing, editing, and testing files, and most coding agents have adopted that design. SWE-bench, the benchmark it was built for, is the accepted measure for evaluating them.

It laid the basis for agent-computer interfaces, the design behind most code-writing agents.

### Generative Agents

_Joon Sung Park et al., Stanford, UIST (User Interface Software and Technology), 2023._

Joon Sung Park et al.'s [Generative Agents][gen-agents] is a landmark simulation. It populates a virtual town with 25 agents exhibiting believable social behavior. The behavior is driven by an append-only memory stream, a three-factor retrieval function, reflection, and planning. The memory architecture has heavily influenced long-term memory designs in production agent systems.

The architecture it names appears in most long-horizon agent products, and the paper remains central to work on agent memory.

### Voyager

_Guanzhi Wang et al., NVIDIA, Caltech, and UT Austin, Transactions on Machine Learning Research, 2024._

Guanzhi Wang et al.'s [Voyager][voyager] is the first LLM-powered embodied lifelong learning agent. It operates in Minecraft and combines an automatic curriculum, a growing skill library of executable code, and iterative prompting with environment feedback. It collects 3.3 times as many unique items and reaches tech tree milestones up to 15.3 times faster than prior state of the art.

It's the source for lifelong learning and skill libraries, the pattern that carries agent capabilities across sessions.

### CAMEL

_Guohao Li et al., King Abdullah University of Science and Technology (KAUST), NeurIPS 2023._

Guohao Li et al.'s [CAMEL][camel] introduces structured role-playing between an AI user and an AI assistant. It's one of the first frameworks to show multiple LLMs with specialized roles solving tasks cooperatively without continuous human intervention. It grew into the CAMEL-AI ecosystem of workforce orchestration and benchmarks. Its dialogue data has been cited as a source for the synthetic data used in Microsoft Phi.

It's among the first multi-agent collaboration papers and remains one of the most cited. The role-playing pattern it introduced appears in most multi-agent frameworks.

### AutoGen

_Qingyun Wu et al., Microsoft Research, COLM (Conference on Language Modeling), 2024._

Qingyun Wu et al.'s [AutoGen][autogen] is the multi-agent conversation framework that became the common way to build agent teams. It composes conversable agents: assistants, user proxies, and code executors. These agents solve tasks by passing messages and executing code. The design grew into the Microsoft Agent Framework.

AutoGen is the default for conversation-driven agent orchestration and one of the most widely adopted multi-agent frameworks.

## The model papers behind the agents

These are the papers behind the models that most agents depend on. They open with the architecture that started the Transformer era. They then move from pretraining and scaling, through alignment, to the methods that turned capable models into useful agents. Those methods run from reasoning and verification to retrieval grounding and code. Each entry underpins one layer of the model stack. The reasoning, tool use, and orchestration patterns in the surrounding sections are what these papers make possible.

### Attention Is All You Need

_Ashish Vaswani et al., Google Brain, NeurIPS 2017._

Ashish Vaswani et al.'s [Attention Is All You Need][attention] introduces the Transformer architecture: self-attention, multi-head attention, and positional encoding. It displaced recurrent networks and became the foundation of most LLMs.

Few deep learning papers of its era are cited more, and most agent architectures are hard to understand without it.

### BERT

_Jacob Devlin et al., Google, NAACL 2019._

Jacob Devlin et al.'s [BERT][bert] establishes bidirectional pretraining with masked language modeling. It enables deep contextual understanding and sets benchmarks across 11 natural language processing tasks.

This paper made pretraining the default. Its encoder architecture underpins many retrieval and classification components in agent systems.

### Language Models are Few-Shot Learners

_Tom B. Brown et al., OpenAI, NeurIPS 2020._

Tom B. Brown et al.'s [Language Models are Few-Shot Learners][gpt3] demonstrates that scaling to 175 billion parameters enables in-context learning from a few examples. It launched the LLM era and the foundation-model paradigm.

The paper enabled the capability jump that made agents possible and ranks among the most-referenced scaling results in the field.

### Training Language Models to Follow Instructions with Human Feedback

_Long Ouyang et al., OpenAI, NeurIPS 2022._

Long Ouyang et al.'s [Training Language Models to Follow Instructions with Human Feedback][instructgpt] introduces reinforcement learning from human feedback _(RLHF)_. RLHF combines supervised fine-tuning, reward modeling, and proximal policy optimization to align models with human intent. It's the method behind ChatGPT and most major instruction-tuned models.

This paper turned capable models into instruction-following agents, the reason that agent behavior is steerable at all.

### Constitutional AI

_Yuntao Bai et al., Anthropic, December 2022._

Yuntao Bai et al.'s [Constitutional AI][constitutional-ai] replaces heavy human feedback with AI self-critique guided by a written constitution. It reduces reliance on human labelers while maintaining alignment.

Constitutional AI is the origin of behavior rules written in advance, the same idea behind model specs and agent constitutions.

### Chain-of-Thought Prompting

_Jason Wei et al., Google Research, NeurIPS 2022._

Jason Wei et al.'s [Chain-of-Thought Prompting][cot] shows that prompting for intermediate reasoning steps substantially improves performance. The gains appear on arithmetic, commonsense, and symbolic reasoning tasks.

It made reasoning visible and steerable and is the ancestor of most patterns of thinking before acting in agents.

### Self-Consistency

_Xuezhi Wang et al., Google Research, ICLR 2023._

Xuezhi Wang et al.'s [Self-Consistency][self-consistency] improves chain-of-thought by sampling multiple reasoning paths and selecting the most consistent answer. It reduces variance without additional training.

It remains the key source on sampling-based verification, a core technique in evaluator and judge patterns.

### Retrieval-Augmented Generation

_Patrick Lewis et al., Meta AI, NeurIPS 2020._

Patrick Lewis et al.'s [Retrieval-Augmented Generation][rag] introduces the retriever-reader architecture that grounds LLM outputs in external knowledge. It set the standard for reducing hallucination in knowledge-intensive tasks.

This paper is where grounding begins, the way agents connect to enterprise data.

### Program-Aided Language Models

_Luyu Gao et al., Carnegie Mellon, ICML 2023._

Luyu Gao et al.'s [Program-Aided Language Models][pal] demonstrates that offloading reasoning to executable Python code works. The model generates code instead of natural-language chains and executes it through a runtime. That approach achieves high accuracy on math benchmarks.

Code-as-reasoning has its roots in this paper, the pattern behind code-writing agents and tool-use loops.

## Safety and governance

These four documents govern what an agent can do in production. The first two are the providers' practical guidance on governance and on trustworthy practice. The Responsible Scaling Policy sets the safety framework for frontier development. The Model Spec is the behavior rulebook built into OpenAI models. Read together, they are the guardrail layer of a production agent.

### Practices for Governing Agentic AI Systems

_OpenAI, December 2023._

OpenAI's [Practices for Governing Agentic AI Systems][openai-governance] is a governance framework for deploying agents responsibly. It covers oversight hierarchies, human-in-the-loop escalation, and evaluation protocols. It also covers risk management, including tool risk ratings and output validation. Monitoring and guardrail patterns complete the framework, including LLM-based classifiers and rules-based filters.

It is concrete provider guidance on production governance, the source to consult when an agent is about to touch real systems.

### Trustworthy Agents in Practice

_Anthropic, April 2026._

Anthropic's [Trustworthy Agents in Practice][anthropic-trustworthy] is practical guidance on building trustworthy agents. It recommends transparency through inspectable reasoning and user control through interrupts and confirmation. It also recommends privacy by data minimization, monitoring through logging and anomaly detection, and safe deployment through gradual rollout and failure thresholds.

It complements the governance guide with a lens focused on security and privacy. Together the two cover most of what production deployment needs.

### Responsible Scaling Policy

_Anthropic, 2023; updated through v3.4 in July 2026._

Anthropic's [Responsible Scaling Policy][anthropic-rsp] defines the framework for assessing and mitigating risks across four AI Safety Levels. It covers deployment thresholds, evaluation requirements, security measures, and governance processes. The original September 2023 policy has been refined continuously through version 3.4.

It offers important background for understanding the safety constraints shaping frontier agent development.

### Model Spec

_OpenAI, May 2024; updated February 2025._

OpenAI's [Model Spec][model-spec] is the behavioral constitution for OpenAI models. It covers instruction following, refusals, and safety boundaries. It also covers the design assumptions shaping how agents built on OpenAI models behave.

It documents the guardrails already built into the model layer, before any agent code is written.

## Protocols and standards

Two standards give agents access to the world beyond their context window. The Model Context Protocol standardizes how an assistant connects to tools and data. The `AGENTS.md` file defines how a coding agent learns what a codebase requires. Where the safety section governs what agents can do, these two define how agents connect. Together they provide a shared interface for agent integration.

### Model Context Protocol

_Anthropic, November 2024._

Anthropic's [Model Context Protocol][mcp] is the open protocol that standardizes how assistants connect to tools, data sources, and services. It uses a client-server architecture. It has become the standard for agent-tool integration, adopted by OpenAI, Google, and major integrated development environments _(IDEs)_. The specification defines transports, resource templates, tool annotations, and security boundaries.

It's the closest thing agent integration has to a common protocol. Ecosystem adoption makes it the primary way agents reach the outside world.

### The AGENTS.md file

_OpenAI, August 2025._

The [`AGENTS.md` file][agents-md] is a lightweight convention for repository-level instructions that coding agents can discover and follow. It covers conventions, architecture decisions, testing requirements, and workflows. It's supported by Claude Code, Cursor, Windsurf, and other agentic coding tools.

It's the emerging convention for telling an agent how a specific codebase works, the file that makes those instructions discoverable to agents.

## The books

If the papers give the field its immediate ideas, these two books give it a durable foundation. _AI Engineering_ is the practitioner's guide to the full lifecycle of building and running these systems, and it's among the most recommended books in the field. _Artificial Intelligence: A Modern Approach_ is the definitive textbook, where the concepts that agents build on get their rigorous treatment. One is for building, the other for understanding. Both outlast any single generation of tools.

### AI Engineering

_Chip Huyen, O'Reilly, 2025._

Chip Huyen's [AI Engineering][huyen-ai] is a comprehensive guide to the full lifecycle of production AI systems. It covers data curation, model selection, evaluation, deployment, monitoring, and continual learning. It has strong coverage of prompt engineering, retrieval-augmented generation, agent architectures, and the metrics that matter for agentic systems.

It's the LLM-era successor to the classic systems texts.

### Artificial Intelligence: A Modern Approach

_Stuart Russell and Peter Norvig, fourth edition, 2020, Pearson._

Stuart Russell and Peter Norvig's [Artificial Intelligence: A Modern Approach][aima] is the authoritative AI textbook. It covers search, planning, knowledge representation, probabilistic reasoning, reinforcement learning, and natural language processing. The chapters on search, planning, and multi-agent systems remain directly relevant to agent architecture.

It's the foundation for the concepts agents build on. If one book anchors the field, this is it.

## References

[anthropic-agents]: https://www.anthropic.com/engineering/building-effective-agents "Building Effective Agents"
[agents-reference-site]: https://buildingeffectiveagents.com/ "Building Effective Agents: An Engineering Reference"
[simon-willison]: https://simonwillison.net/2024/Dec/20/building-effective-agents/ "Building effective agents"
[openai-guide]: https://openai.com/business/guides-and-resources/a-practical-guide-to-building-ai-agents/ "A Practical Guide to Building Agents"
[anthropic-multi-agent]: https://www.anthropic.com/engineering/built-multi-agent-research-system "How we built our multi-agent research system"
[context-engineering]: https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents "Effective context engineering for AI agents"
[ng-four-strategies]: https://www.deeplearning.ai/the-batch/how-agents-can-improve-llm-performance "Four AI Agent Strategies That Improve GPT-4 and GPT-3.5 Performance"
[dont-build-multi-agents]: https://cognition.ai/blog/dont-build-multi-agents "Don't Build Multi-Agents"
[agents-cookbook]: https://github.com/anthropics/claude-cookbooks/tree/main/patterns/agents "Building Effective Agents Cookbook"
[agents-whitepaper]: https://resources.anthropic.com/hubfs/Building%20Effective%20AI%20Agents-%20Architecture%20Patterns%20and%20Implementation%20Frameworks.pdf "Building Effective AI Agents: Architecture Patterns and Implementation Frameworks"
[12-factor-agents]: https://github.com/humanlayer/12-factor-agents "12 Factor Agents"
[hashimoto]: https://mitchellh.com/writing/my-ai-adoption-journey "My AI Adoption Journey"
[openai-harness]: https://openai.com/index/harness-engineering/ "Harness engineering: leveraging Codex in an agent-first world"
[anatomy]: https://blog.langchain.com/the-anatomy-of-an-agent-harness "The Anatomy of an Agent Harness"
[fowler-harness]: https://martinfowler.com/articles/harness-engineering.html "Harness engineering for coding agent users"
[anthropic-long-running]: https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents "Effective harnesses for long-running agents"
[anthropic-harness-design]: https://www.anthropic.com/engineering/harness-design-long-running-apps "Harness design for long-running application development"
[weng]: https://lilianweng.github.io/posts/2023-06-23-agent/ "LLM Powered Autonomous Agents"
[survey]: https://arxiv.org/abs/2308.11432 "A Survey on LLM-based Autonomous Agents"
[react]: https://arxiv.org/abs/2210.03629 "ReAct: Synergizing Reasoning and Acting in Language Models"
[tot]: https://arxiv.org/abs/2305.10601 "Tree of Thoughts: Deliberate Problem Solving with Large Language Models"
[reflexion]: https://arxiv.org/abs/2303.11366 "Reflexion: Language Agents with Verbal Reinforcement Learning"
[self-refine]: https://arxiv.org/abs/2303.17651 "Self-Refine: Iterative Refinement with Self-Feedback"
[toolformer]: https://arxiv.org/abs/2302.04761 "Toolformer: Language Models Can Teach Themselves to Use Tools"
[swe-agent]: https://arxiv.org/abs/2405.15793 "SWE-agent: Agent-Computer Interfaces Enable Automated Software Engineering"
[gen-agents]: https://arxiv.org/abs/2304.03442 "Generative Agents: Interactive Simulacra of Human Behavior"
[voyager]: https://arxiv.org/abs/2305.16291 "Voyager: An Open-Ended Embodied Agent with Large Language Models"
[camel]: https://arxiv.org/abs/2303.17760 "CAMEL: Communicative Agents for 'Mind' Exploration of Large Language Model Society"
[autogen]: https://arxiv.org/abs/2308.08155 "AutoGen: Enabling Next-Gen LLM Applications via Multi-Agent Conversation"
[attention]: https://arxiv.org/abs/1706.03762 "Attention Is All You Need"
[bert]: https://arxiv.org/abs/1810.04805 "BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding"
[gpt3]: https://arxiv.org/abs/2005.14165 "Language Models are Few-Shot Learners"
[instructgpt]: https://arxiv.org/abs/2203.02155 "Training Language Models to Follow Instructions with Human Feedback"
[constitutional-ai]: https://arxiv.org/abs/2212.08073 "Constitutional AI: Harmlessness from AI Feedback"
[cot]: https://arxiv.org/abs/2201.11903 "Chain-of-Thought Prompting Elicits Reasoning in Large Language Models"
[self-consistency]: https://arxiv.org/abs/2203.11171 "Self-Consistency Improves Chain of Thought Reasoning in Language Models"
[rag]: https://arxiv.org/abs/2005.11401 "Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks"
[pal]: https://arxiv.org/abs/2211.10435 "Program-Aided Language Models"
[openai-governance]: https://openai.com/index/practices-for-governing-agentic-ai-systems/ "Practices for Governing Agentic AI Systems"
[anthropic-trustworthy]: https://www.anthropic.com/research/trustworthy-agents "Trustworthy Agents in Practice"
[anthropic-rsp]: https://www.anthropic.com/responsible-scaling-policy "Responsible Scaling Policy"
[model-spec]: https://model-spec.openai.com/ "Model Spec"
[mcp]: https://modelcontextprotocol.io/specification "Model Context Protocol Specification"
[agents-md]: https://agents.md/ "AGENTS.md"
[huyen-ai]: https://www.oreilly.com/library/view/ai-engineering/9781098166298/ "AI Engineering"
[aima]: https://aima.cs.berkeley.edu/ "Artificial Intelligence: A Modern Approach"

## License

Copyright © 2026 [edsvo](https://github.com/edsvo). Licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).

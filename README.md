# AI agent reading list

_A curated reading list for AI agents._

Last reviewed November 2026.

A small set of sources keeps resurfacing across much of the writing about AI agents. This page collects sources that are frequently cited and relied on, grouped by what each contributes. It is written for engineers building agents based on large language models, with an emphasis on coding agents. Entries note what the source contributes and, where useful, how it was received: who endorses it, where the endorsement is qualified, and where the strongest objections come from.

The order starts with the practical design guides and moves to harness practice in production. It then continues through the foundational research and model papers, the governance layer, and finally the protocols and books. The engineering guides from Anthropic and OpenAI define how agents are designed in practice and give the vocabulary to build with. The harness is everything around the model that makes it useful, from tools and memory to orchestration and verification. The term _harness engineering_ appeared in 2026. The discipline builds on the guides' language.

A few years of concentrated research produced the underlying ideas. The range runs from reasoning and self-improvement to tool use, the agent-computer interface—the boundary through which an agent perceives and acts on a computer—memory, lifelong learning, and multi-agent collaboration. The model papers behind those ideas describe the capabilities that agents depend on. Governance documents become essential the moment agents connect to production systems. Protocols connect agents to the outside world—to tools, data, codebases, and packaged expertise—and a few books give the field a durable foundation.

> [!NOTE]
> Compiled with AI assistance and reviewed by the author, [@edsvo](https://github.com/edsvo). Verify claims against the linked sources before relying on them.

## Contents

- [The engineering guides](#the-engineering-guides)
  - [Supporting artifacts](#supporting-artifacts)
- [The harness](#the-harness)
- [The research foundation](#the-research-foundation)
- [The model papers behind the agents](#the-model-papers-behind-the-agents)
- [Safety and governance](#safety-and-governance)
- [Protocols and standards](#protocols-and-standards)
- [The books](#the-books)
- [A note on the selection](#a-note-on-the-selection)
- [References](#references)

## The engineering guides

Two provider guides open this section, covering the same design space in different ways. Anthropic's guide is a taxonomy: three building blocks, five workflow patterns, and a clear line between workflows and agents. The taxonomy is deliberately framework-independent. Workflows fix control flow in code; agents leave control flow to the model. OpenAI's guide is a build guide: three pillars (model, tools, and instructions), orchestration from a single agent to decentralized handoffs, and the Agents SDK. Guardrails place constraints on agent behavior.

Both give the same advice: start with the simplest thing that works and add complexity only when it demonstrably helps. Read together, they give you the terms for naming an agent design and the path for building it. The rest of the section follows that vocabulary into production practice, capability framing, and dissent.

### Building Effective Agents

_Erik Schluntz and Barry Zhang, Anthropic, December 2024._

Anthropic's [Building Effective Agents][anthropic-agents] defines a widely cited taxonomy of agent design. The taxonomy has three building blocks: an augmented _large language model_ (_LLM_), workflows, and agents. It also has five composable workflow patterns: prompt chaining, routing, parallelization, orchestrator-workers, and evaluator-optimizer. The post draws on Anthropic's experience building agents and working with dozens of teams.

It's a commonly recommended starting point for agent engineering. The workflows-versus-agents distinction and the five pattern names have become a common vocabulary for workflow design. The documentation from OpenAI, LangChain, and Google Cloud makes the same distinction, and independent guides often cite the post as a key source. A dedicated third-party site, [Building Effective Agents: An Engineering Reference][agents-reference-site], builds a full engineering resource around it.

The reception was immediate. On December 20, 2024, the AI blogger Simon Willison wrote in [Building effective agents][simon-willison] that the post was "the clearest practical guide to building systems that combine multiple LLM calls that I've seen anywhere." The regard is earned but qualified. The taxonomy comes from Anthropic's production experience, not from an empirically validated standard. Much later writing reads the sharp workflows-versus-agents binary as a spectrum, borrowing the post's vocabulary while softening the boundary.

### A Practical Guide to Building Agents

_OpenAI, April 2025; updated January 2026._

[A Practical Guide to Building Agents][openai-guide] is a builder-first guide organized around three agent pillars: model, tools, and instructions. It covers orchestration (single-agent systems, the manager pattern, and decentralized handoffs), guardrails, and the OpenAI Agents SDK. It defines an agent as a system that manages its own workflow execution. Its central advice is to start with a single agent and add multi-agent complexity only when the task demands it. It complements Anthropic's guide with operational and safety depth.

Read it alongside Anthropic's guide as the second key provider document. Where Anthropic names the workflow patterns, OpenAI frames the same design space around pillars and orchestration. Together they cover much of what a team encounters.

### How we built our multi-agent research system

_Jeremy Hadfield et al., Anthropic, June 2025._

[How we built our multi-agent research system][anthropic-multi-agent] is the production account of the orchestrator-workers pattern. It describes how Anthropic's Research feature decomposes a query into parallel search tasks. Each subagent explores with its own context window, and a lead agent synthesizes the results. On Anthropic's internal research evaluation, the multi-agent system outperformed a single agent by 90.2%. It uses roughly 15 times the tokens of a chat interaction.

The post is frequently cited as production evidence for multi-agent systems and is a source of the cost-performance framing that later writing borrows. It also names where the pattern fits poorly: coding tasks with shared state, the position Cognition takes.

### Effective context engineering for AI agents

_Prithvi Rajasekaran et al., Anthropic, September 2025._

Anthropic's [Effective context engineering for AI agents][context-engineering] develops _context engineering_: curating and maintaining the tokens that enter an LLM's context window. It builds directly on the Building Effective Agents taxonomy, arguing that context, not prompt wording, is the primary way to steer agent behavior. It treats context as a finite resource subject to [context rot][chroma-context-rot], the decline in response quality as the window fills. It lays out the long-horizon techniques the harness section builds on: compaction (condensing older context), structured note-taking, and subagents that isolate context.

The term predates the post. Walden Yan had used it in [Don't Build Multi-Agents][dont-build-multi-agents] in June 2025, after the phrase had already begun circulating. Tobi Lütke and Andrej Karpathy popularized it in the weeks that followed. Anthropic's post gave it a detailed engineering treatment and a wide audience. It helped establish the context-management vocabulary—compaction, subagent context isolation, just-in-time retrieval—that later agent writing borrows.

### Four AI Agent Strategies That Improve GPT-4 and GPT-3.5 Performance

_Andrew Ng, The Batch, March 2024._

Andrew Ng's [four agent strategies][ng-four-strategies] are reflection, tool use, planning, and multi-agent collaboration. The post argues that agent workflows will drive more progress than the next generation of foundation models. It supports the claim with HumanEval results: GPT-4 in zero-shot mode scores 67.0%, whereas GPT-3.5 wrapped in an agent loop reaches 95.1%. The framework is a common way to describe what agents can do.

The taxonomy is a common vocabulary for agent capabilities. The series also carries Ng's caution: reflection and tool use are the mature, reliable patterns, whereas planning and multi-agent collaboration remain less predictable.

### Don't Build Multi-Agents

_Walden Yan, Cognition, June 2025._

Cognition's [Don't Build Multi-Agents][dont-build-multi-agents] makes the dissenting case against multi-agent architectures. Walden Yan argues for a single-threaded agent with fully shared context. Multi-agent systems spread decision-making across isolated contexts, so conflicting assumptions compound. The case is grounded in Devin, the coding agent Cognition builds, where write operations share state.

Anthropic's multi-agent post came out the next day, which Yan noted as coincidental. Readers take the dissent seriously but read it as workload-specific. Yan returned to it in April 2026 in [Multi-Agents: What's Actually Working][multi-agents-working], reporting that Cognition had begun deploying multi-agent systems that work. He kept the objection to parallel writers and narrowed the claim. The patterns that work let multiple agents contribute intelligence while writes stay single-threaded. He reports that most real-world multi-agent setups use only read-only subagents that resemble tool calls rather than genuine collaboration.

### Supporting artifacts

The preceding entries set out the main positions on multi-agent design. Four supporting artifacts round out the design space. The [Building Effective Agents Cookbook][agents-cookbook] holds minimal reference implementations of the workflows that the Anthropic post describes. A white paper (PDF), [Building Effective AI Agents: Architecture Patterns and Implementation Frameworks][agents-whitepaper], repackages the same ideas for enterprise audiences, with customer case studies from Coinbase, Intercom, and Thomson Reuters. Dexter Horthy's [12 Factor Agents][12-factor-agents] turns the single-threaded preference into a practical checklist, and LlamaIndex packages it as a [12 Factor Agents guide][12-factors-llamaindex]. One of these artifacts reaches past that space entirely.

[Omarchy][omarchy], David Heinemeier Hansson's Linux distribution, takes the environment-as-harness idea down to the operating system. Its [AI section][omarchy-ai] wires up 23 coding-agent command-line tools and names no favorite among them. An `omarchy agent` prompt runs unattended, and a crashed process arrives as a diagnosis request.

One skill for tailoring the system is symlinked into Claude Code, Codex, Pi, Google Antigravity, and Hermes, the kind of cross-harness portability that Agent Skills is designed to provide. By November 2026 it had drawn more than 47,000 GitHub stars, and the project reports 1.38 million ISO image downloads in its first year. DHH's premise is that if you can build an app by prompting an agent, you should be able to build your operating system the same way.

## The harness

Where the engineering guides describe how to design an agent, harness engineering describes everything around the model that makes it useful in production. The harness includes the tools, memory, context management, orchestration, and verification loops that turn a capable model into a reliable tool. A common shorthand is LangChain's definition: an agent is a model plus a harness. The working claim in this section is that reliability comes from the model-harness system: the harness is where many practical gains are available, but its components are model-specific and can be absorbed as models improve.

Mitchell Hashimoto's post popularized the term in February 2026, and OpenAI and Anthropic took it up within months. The following sources define the discipline and show what building a harness involves. They close with Anthropic's two posts. Together they bracket the discipline: one predates the term itself, the other shows how much harness design can change an outcome.

### My AI Adoption Journey

_Mitchell Hashimoto, mitchellh.com, February 2026._

[My AI Adoption Journey][hashimoto] gave the practice its name. Hashimoto, the HashiCorp co-founder who created Vagrant and Terraform, documents his transition to an AI-first workflow. He distills the recurring principle behind every fix he made: when an agent makes a mistake, build a solution so it doesn't make that mistake again. The buildable solution—documentation, constraints, tools—is the harness. The habit of feeding every agent failure back into that environment is harness engineering.

The term traces back to this post, and later harness engineering writing often treats it as a starting point. The practices themselves build on the earlier guides.

### Harness engineering: leveraging Codex in an agent-first world

_OpenAI, February 2026._

OpenAI's [Harness engineering: leveraging Codex in an agent-first world][openai-harness] is one of the most detailed accounts of the discipline in practice. The post describes letting Codex agents generate and maintain an entire codebase. That work includes product code, tests, continuous integration configuration, developer tools, documentation, and even the scripts that manage the repository.

Humans work at a higher layer of abstraction: they prioritize work, translate user feedback into acceptance criteria, and validate outcomes. When an agent struggles, the team treats it as a signal of a missing tool, guardrail, or piece of documentation. The team then feeds that fix back into the repository.

It closes with a line that captures the approach: building software still demands discipline, but the discipline shows up more in the scaffolding than in the code. The post reports that the team built the product in roughly one-tenth the time it would have taken by hand. That figure is the team's own estimate from a single internal beta, not a controlled benchmark.

### The Anatomy of an Agent Harness

_Vivek Trivedy, LangChain, March 2026._

Vivek Trivedy's [The Anatomy of an Agent Harness][anatomy] offers a working definition of the harness. It states the definition as an equation: an agent is a model plus a harness. It sharpens the equation with "if you're not the model, you're the harness."

The post derives the harness's components from what a raw model cannot do. Those components are system prompts, tools, memory, orchestration, compaction and context management, and verification loops. The post cites LangChain's own evidence: its coding agent rose from the top 30 to the top 5 on Terminal-Bench 2.0 by changing only the harness. The result is self-reported, not an independent evaluation.

The model-plus-harness formula repeats throughout this section.

### Harness engineering for coding agent users

_Birgitta Böckeler, martinfowler.com, April 2026._

Birgitta Böckeler's [Harness engineering for coding agent users][fowler-harness] is a deep treatment of the topic. It is also one of the first to turn the wide "everything except the model" definition into concrete coding-agent practice. Böckeler narrows the scope to coding agents.

She introduces the working vocabulary: sensors, the architecture fitness harness, the behavior harness, harness templates for common service topologies, and harnessability. Sensors are tests, linters, and other feedback mechanisms that tell an agent what it broke. Harnessability is the design quality that reduces the work of wrapping a codebase in a harness. A sidebar maps where harness engineering sits relative to the earlier post on context engineering.

It appears in Martin Fowler's _Exploring Generative AI_ series and is often cited as a primary source for applying harness engineering to real codebases. It also warns that the harness is especially needed where it's hardest to build: in legacy teams with accumulated technical debt.

### LLM Wiki

_Andrej Karpathy, gist.github.com, April 2026._

[LLM Wiki][llm-wiki] is a pattern for building knowledge bases with an agent. Rather than retrieve raw chunks at query time, the agent compiles sources into a persistent, interlinked set of Markdown files and keeps them current. It has three layers: immutable raw sources, a wiki the agent maintains, and a schema file defining structure and workflows. Three operations run against it: ingest, query, and lint.

That schema is the `AGENTS.md` or `CLAUDE.md` file telling the agent how to maintain the rest. Karpathy's formulation is that Obsidian is the IDE, the LLM the programmer, and the wiki the codebase. He traces the lineage to Vannevar Bush's Memex (1945). The pattern is a disciplined form of the note-taking that Anthropic's long-running harness depends on.

By November 2026 the gist had more than 5,000 stars and forks. Independent implementations followed in Python and Elixir, and the pattern is packaged as an unofficial Agent Skills skill for Claude Code, Cursor, and Codex. Commenters describe running it for client work, codebase documentation, and design automation. It states a claim that runs through this section: a maintained artifact around the model grows in value. A fresh prompt each time starts from nothing.

Three limits are worth naming. Karpathy scopes the pattern to moderate scale, where a plain index file replaces embedding-based retrieval. More pointed, the artifact can be correct and still go unused. One team reported that, after auditing nine sessions, the wiki was read at session start but not mid-task, and agents diagnosed the same failures again even though the wiki already contained a written lesson. One commenter also argues against catching conflicts in the lint pass. Finding a disagreement later means comparing every pair of pages.

### Effective harnesses for long-running agents

_Anthropic, November 2025._

Anthropic's first harness post, [Effective harnesses for long-running agents][anthropic-long-running], was published in November 2025, before the term was coined. It addresses the problem that a long-running coding agent loses memory across context windows. The harness adds an initializer agent that decomposes a product spec into a feature list, plus a coding agent that implements one feature at a time. The coding agent keeps a progress log and posts structured updates so each session continues where the last one ended. This post introduced the context-reset technique that later harnesses build on.

This post is a template for long-running agent harnesses and a direct ancestor of Anthropic's later harness-design post.

### Harness design for long-running application development

_Prithvi Rajasekaran, Anthropic Labs, March 2026._

Prithvi Rajasekaran's [Harness design for long-running application development][anthropic-harness-design] is a detailed case study in how much harness design can change an outcome. Drawing on generative adversarial networks, Rajasekaran built a three-agent harness: a planner, a generator, and an evaluator.

The planner expands a short prompt into a product spec. The generator implements one feature per sprint, and the evaluator clicks through the running application with Playwright, grading each sprint against hard thresholds. Before any code is written, the generator and evaluator negotiate a sprint contract. On the same task, a solo agent produced an application that launched but was broken at the wiring level. The harnessed run produced a richer, working application. The harness run took about six hours and $200, against 20 minutes and $9 for the solo run.

The post's second half matters as much as the first. With Claude Opus 4.6, Rajasekaran removed the sprint construct, moved the evaluator to a single end-of-run pass, and kept the planner and evaluator. The lesson is that every harness component encodes an assumption about what the model cannot do, and those assumptions go stale. His working rule is to retest the harness against each model release and strip away the components that are no longer needed.

Anthropic published it on its engineering blog, and the trade press covered it widely. Together with the November 2025 post, it is a strong provider case study for harness design—but it is a single-team, single-task comparison at roughly 20 times the cost, not a controlled benchmark.

## The research foundation

The following sources are the primary literature behind the patterns that the engineering guides name. They begin with the two overviews of the field, and then follow the introduction's order through the ideas. Together they are source material for many agent frameworks.

### LLM Powered Autonomous Agents

_Lilian Weng, June 2023._

Lilian Weng's [LLM Powered Autonomous Agents][weng] is a well-known overview of agent architecture. Weng frames an agent as an LLM core with three surrounding components: planning, memory, and tool use. Planning covers subgoal decomposition and reflection. Memory covers short-term context and long-term stores. Tool use covers external APIs and code execution. She shows how each component was assembled from earlier research. Written while she was at OpenAI, it is a common starting point for engineers.

The framing it named—planning, memory, and tool use—became a common vocabulary.

### A Survey on LLM-based Autonomous Agents

_Lei Wang et al., Frontiers of Computer Science, 2024._

Lei Wang et al.'s [A Survey on LLM-based Autonomous Agents][survey] is a widely cited survey of the field. It organizes agents into construction, application, and evaluation. It catalogs how agents are built: reasoning, memory, planning, tool use, and multi-agent collaboration. It also catalogs where they are deployed. Together they give a broad picture of the design space in one place.

By November 2026 the survey had drawn more than two thousand citations and was still being revised two years after publication. Its construction, application, and evaluation split has become a common way to organize the literature on agents.

### ReAct

_Shunyu Yao et al., Princeton and Google Research, ICLR 2023._

Shunyu Yao et al.'s [ReAct][react] introduces the Thought-Action-Observation loop. It interleaves reasoning traces with tool-driven actions and shows that the combination outperforms either alone. It excels on multi-hop question answering, fact verification, and interactive decision-making.

Many agent frameworks descend from this paper. Its loop is a common shape for agent reasoning.

### Tree of Thoughts

_Shunyu Yao et al., Princeton and Google Research, NeurIPS 2023._

Shunyu Yao et al.'s [Tree of Thoughts][tot] extends chain-of-thought into a tree search over multiple reasoning paths. It generates candidates, self-evaluates states, and backtracks from dead ends. On Game of 24, GPT-4 with chain-of-thought solves 4% of tasks; with tree search it reaches 74%.

It remains a key source on planning and search-intensive reasoning and on the argument that single-path reasoning is not enough.

### Reflexion

_Noah Shinn et al., Northeastern and Princeton, NeurIPS 2023._

Noah Shinn et al.'s [Reflexion][reflexion] introduces verbal reinforcement learning. Agents reflect on failures in natural language and store the reflections in episodic memory. They improve on subsequent attempts without weight updates. On HumanEval the agents reach 91% `pass@1`, the share of problems their first generated solution solves. They reach 97% success on ALFWorld, well above the ReAct baseline.

Reflexion made self-improvement through reflection a common pattern in agent harnesses.

### Self-Refine

_Aman Madaan et al., NeurIPS 2023._

Aman Madaan et al.'s [Self-Refine][self-refine] introduces iterative self-correction without retraining. The model drafts an output, critiques its own draft, and revises. It repeats until nothing remains to fix. It improves quality by roughly 20% on average across seven tasks, from code to dialogue.

Generate-critique-revise loops trace back to it, the pattern behind many self-improving agent harnesses.

### Toolformer

_Timo Schick et al., Meta AI, NeurIPS 2023._

Timo Schick et al.'s [Toolformer][toolformer] demonstrates that LLMs can learn when and how to invoke external APIs through self-supervised learning. It keeps only the candidate calls that reduce prediction loss on subsequent tokens. The resulting model outperforms much larger ones on arithmetic and knowledge tasks. The result establishes that tool use is a learnable capability rather than a scaffolded behavior.

It helped establish tool use as a learnable capability, an idea behind the tool-calling layer many agents rely on. It remains a useful guide to teaching models to use tools.

### SWE-agent

_John Yang et al., Princeton, NeurIPS 2024._

John Yang et al.'s [SWE-agent][swe-agent] introduces the agent-computer interface, the boundary through which an agent perceives and acts on a computer. It gives an agent a purpose-built shell for viewing, editing, and testing files, and many coding agents have adopted that design. SWE-bench, the software engineering benchmark it was built for, is a widely used measure for evaluating them.

The name has since spread beyond software engineering, and later work on GUI and computer-use agents adopts it for the same purpose.

### Generative Agents

_Joon Sung Park et al., Stanford, UIST (User Interface Software and Technology), 2023._

Joon Sung Park et al.'s [Generative Agents][gen-agents] is an influential simulation. It populates a virtual town with 25 agents exhibiting believable social behavior. The behavior is driven by an append-only memory stream, a three-factor retrieval function, reflection, and planning. The memory architecture has heavily influenced long-term memory designs in production agent systems.

The architecture it describes appears in many long-horizon agent products, and the paper remains central to work on agent memory.

### MemGPT

_Charles Packer et al., University of California, Berkeley, October 2023._

Charles Packer et al.'s [MemGPT][memgpt] imports virtual memory from operating systems. The context window acts as main memory, and the agent pages information to external storage through self-directed function calls, so a fixed-context model behaves as if its memory were unbounded. Interrupts manage control flow between the agent and the user. On document analysis and multi-session chat, the design sustains interaction far beyond the underlying window.

The paper is often treated as a standard reference on agent memory management, and its system continues as the Letta platform.

### Voyager

_Guanzhi Wang et al., NVIDIA, Caltech, and UT Austin, Transactions on Machine Learning Research, 2024._

Guanzhi Wang et al.'s [Voyager][voyager] is an early LLM-powered embodied lifelong learning agent in Minecraft. It operates in Minecraft and combines an automatic curriculum, a growing skill library of executable code, and iterative prompting with environment feedback. It collects 3.3 times as many unique items and reaches tech tree milestones up to 15.3 times faster than prior state of the art.

It's a source for lifelong learning and skill libraries, the pattern that carries agent capabilities across sessions and is packaged as Agent Skills.

### CAMEL

_Guohao Li et al., King Abdullah University of Science and Technology (KAUST), NeurIPS 2023._

Guohao Li et al.'s [CAMEL][camel] introduces structured role-playing between an AI user and an AI assistant. It's one of the first frameworks to show multiple LLMs with specialized roles solving tasks cooperatively without continuous human intervention. It expanded into the CAMEL-AI ecosystem of workforce orchestration and benchmarks.

It's among the first multi-agent collaboration papers and among the most cited in that subfield. The role-playing pattern it introduced appears in many multi-agent frameworks.

### AutoGen

_Qingyun Wu et al., Microsoft Research, COLM (Conference on Language Modeling), 2024._

Qingyun Wu et al.'s [AutoGen][autogen] is the multi-agent conversation framework that became a common way to build agent teams. It composes conversable agents: assistants, user proxies, and code executors. These agents solve tasks by passing messages and executing code. The design evolved into the Microsoft Agent Framework, which reached 1.0 in April 2026.

AutoGen became a common framework for conversation-driven agent orchestration and a widely adopted multi-agent framework. It entered maintenance mode in October 2025 and is archived.

## The model papers behind the agents

These papers are behind the models that many agents depend on. They open with the architecture that started the Transformer era. They then move from pretraining and scaling, through alignment, to the methods that turned capable models into useful agents. Those methods run from reasoning and verification to retrieval grounding and code. Each entry covers one layer of the model stack. The papers make possible the reasoning, tool use, and orchestration patterns described in the surrounding sections.

### Attention Is All You Need

_Ashish Vaswani et al., Google Brain, NeurIPS 2017._

Ashish Vaswani et al.'s [Attention Is All You Need][attention] introduces the Transformer architecture: self-attention, multi-head attention, and positional encoding. It displaced recurrent networks and became a foundation for many LLMs.

It is a foundational paper for modern LLMs, and many agent architectures are hard to understand without it.

### BERT

_Jacob Devlin et al., Google, NAACL 2019._

Jacob Devlin et al.'s [BERT][bert] establishes bidirectional pretraining with masked language modeling. It enables deep contextual understanding and sets benchmarks across 11 natural language processing tasks.

This paper helped make pretraining the default approach. Its encoder architecture underpins many retrieval and classification components in agent systems.

### Language Models are Few-Shot Learners

_Tom B. Brown et al., OpenAI, NeurIPS 2020._

Tom B. Brown et al.'s [Language Models are Few-Shot Learners][gpt3] demonstrates that scaling to 175 billion parameters enables in-context learning from a few examples. It helped launch the LLM era and the foundation-model paradigm.

The paper enabled a capability jump that helped make agents possible and is a widely referenced scaling result.

### Training Language Models to Follow Instructions with Human Feedback

_Long Ouyang et al., OpenAI, NeurIPS 2022._

Long Ouyang et al.'s [Training Language Models to Follow Instructions with Human Feedback][instructgpt] introduces _reinforcement learning from human feedback_ (_RLHF_). RLHF combines supervised fine-tuning, reward modeling, and proximal policy optimization to align models with human intent. It is the method behind ChatGPT and many instruction-tuned models.

This paper turned capable models into instruction-following agents, a key reason that agent behavior is steerable.

### Constitutional AI

_Yuntao Bai et al., Anthropic, December 2022._

Yuntao Bai et al.'s [Constitutional AI][constitutional-ai] replaces heavy human feedback with AI self-critique guided by a written constitution. It reduces reliance on human labelers while maintaining alignment.

Constitutional AI made written behavior rules a central mechanism for alignment training. Model specs and agent constitutions follow the same pattern.

### Chain-of-Thought Prompting

_Jason Wei et al., Google Research, NeurIPS 2022._

Jason Wei et al.'s [Chain-of-Thought Prompting][cot] shows that prompting for intermediate reasoning steps substantially improves performance. The gains appear on arithmetic, commonsense, and symbolic reasoning tasks.

It made reasoning visible and steerable and is an ancestor of many patterns of thinking before acting in agents.

### Self-Consistency

_Xuezhi Wang et al., Google Research, ICLR 2023._

Xuezhi Wang et al.'s [Self-Consistency][self-consistency] improves chain-of-thought by sampling multiple reasoning paths and selecting the most consistent answer. It reduces variance without additional training.

It remains a key source on sampling-based verification, a core technique in reasoning and in some evaluator patterns.

### DeepSeek-R1

_DeepSeek-AI, January 2025._

DeepSeek-AI's [DeepSeek-R1][deepseek-r1] shows that reinforcement learning alone can produce reasoning. Its first experiment, DeepSeek-R1-Zero, trains a base model with no labeled examples, and behaviors such as verification, reflection, and backtracking emerge on their own. The released model adds a small cold-start dataset and a multi-stage pipeline. It matches OpenAI's o1 on math, code, and general reasoning benchmarks, scoring 79.8% `pass@1` on AIME 2024 against o1's 79.2%.

The open weights and distillation recipe helped launch a series of open reasoning models, making trained reasoning a common layer beneath agent harnesses.

### Retrieval-Augmented Generation

_Patrick Lewis et al., Meta AI, NeurIPS 2020._

Patrick Lewis et al.'s [Retrieval-Augmented Generation][rag] introduces the retriever-reader architecture that grounds LLM outputs in external knowledge. It established a common approach to grounding outputs and reducing hallucination in knowledge-intensive tasks.

This paper is an early foundation for grounding, a common way agents connect to enterprise data.

### Program-Aided Language Models

_Luyu Gao et al., Carnegie Mellon, ICML 2023._

Luyu Gao et al.'s [Program-Aided Language Models][pal] demonstrates that offloading reasoning to executable Python code works. The model generates code instead of natural-language chains and executes it through a runtime. That approach achieves high accuracy on math benchmarks.

This paper is an early example of code-as-reasoning, the pattern behind code-writing agents and tool-use loops.

## Safety and governance

These four documents frame how providers govern what an agent can do in production. The first two are the providers' practical guidance on governance and on trustworthy practice. The Responsible Scaling Policy sets the safety framework for frontier development. The Model Spec is the behavior rulebook built into OpenAI models. Read together, they are the guardrail layer of a production agent.

### Practices for Governing Agentic AI Systems

_OpenAI, December 2023._

OpenAI's [Practices for Governing Agentic AI Systems][openai-governance] is a governance framework for deploying agents responsibly. It covers oversight hierarchies, human-in-the-loop escalation, and evaluation protocols. It also covers risk management, including tool risk ratings and output validation. Monitoring and guardrail patterns complete the framework, including LLM-based classifiers and rules-based filters.

It is concrete provider guidance on production governance, a source to consult when an agent is about to touch real systems.

### Trustworthy Agents in Practice

_Anthropic, April 2026._

Anthropic's [Trustworthy Agents in Practice][anthropic-trustworthy] sets out five principles for trustworthy agents: keeping humans in control, aligning with human values, securing agents' interactions, maintaining transparency, and protecting privacy. It applies them to product decisions—permissions and approvals, defenses against prompt injection, and choices about which tools and data an agent can reach.

It complements the governance guide with a lens focused on security and privacy. Together the two cover the provider-side guardrail layer, though neither is a substitute for independent security review.

### Responsible Scaling Policy

_Anthropic, 2023; updated through v3.4 in July 2026._

Anthropic's [Responsible Scaling Policy][anthropic-rsp] defines the framework for assessing and mitigating risks across four AI Safety Levels. It covers deployment thresholds, evaluation requirements, security measures, and governance processes. The original September 2023 policy has been refined continuously through version 3.4.

It offers important background for understanding the safety constraints shaping frontier agent development.

### Model Spec

_OpenAI, May 2024; updated October 2026._

OpenAI's [Model Spec][model-spec] is the behavioral constitution for OpenAI models. It covers instruction following, refusals, and safety boundaries. It also covers the design assumptions shaping how agents built on OpenAI models behave.

It documents the guardrails already built into the model layer, before any agent code is written.

## Protocols and standards

Four standards define how agents reach the world beyond their context window, and one framework optimizes what happens after they get there. The Model Context Protocol standardizes how an assistant connects to tools and data. The Agent2Agent Protocol standardizes how independent agents discover and coordinate with each other. The `AGENTS.md` file defines how a coding agent learns what a codebase requires. Agent Skills define how reusable expertise is packaged so any agent can load and apply it. DSPy predates all four standards by more than a year. It replaces hand-crafted prompt templates with a programming model and compiler that optimizes agent pipelines automatically. Where the safety section addresses what agents can do, the standards define how agents connect. Together they provide shared interfaces for agent integration.

### Model Context Protocol

_Anthropic, November 2024._

Anthropic's [Model Context Protocol][mcp] is the open protocol that standardizes how assistants connect to tools, data sources, and services. It uses a client-server architecture. It has become a common standard for agent-tool integration, adopted by OpenAI, Google, and major _integrated development environments_ (_IDEs_). Anthropic donated it to the Agentic AI Foundation, a directed fund under the Linux Foundation, in December 2025. It is stewarded alongside Agent2Agent. The specification defines transports, resource templates, tool annotations, and security boundaries.

It is often described as the closest thing agent integration has to a common protocol. Ecosystem adoption has made it one of the main ways agents reach the outside world.

### Agent2Agent Protocol

_Google, April 2025._

Google's [Agent2Agent Protocol][a2a] is an open protocol for communication between independent agents. Each agent publishes an _Agent Card_, a machine-readable description of its skills and endpoints. Client agents delegate tasks through a defined lifecycle with streaming updates. It launched with more than fifty partners and, by August 2026, had drawn support from more than 150 organizations.

In June 2025, Google donated the protocol to the Linux Foundation, where Amazon Web Services, Cisco, Microsoft, Salesforce, SAP, and ServiceNow steward it under neutral governance. The protocol reached a stable v1.0 in March 2026. In August 2026 it joined the Agentic AI Foundation as a Growth Stage project alongside the Model Context Protocol and the `AGENTS.md` file. It fills the layer MCP does not: where MCP connects an agent to tools and data, A2A lets agents discover each other and hand work across frameworks and organizational boundaries.

### The `AGENTS.md` file

_OpenAI, August 2025._

The [`AGENTS.md` file][agents-md] is a lightweight convention for repository-level instructions that coding agents can discover and follow. It covers conventions, architecture decisions, testing requirements, and workflows. It's supported by Cursor, Windsurf, GitHub Copilot, and other agentic coding tools. Claude Code can read it too, but only when no `CLAUDE.md` or `CLAUDE.local.md` file is present, unless the project instructions setting is changed to load both. Reading the `AGENTS.md` file directly requires v2.1.277 or later.

It's a convention for telling an agent how a specific codebase works, the file that makes those instructions discoverable to agents.

### Equipping agents for the real world with Agent Skills

_Barry Zhang, Keith Lazuka, and Mahesh Murag, Anthropic, October 2025._

Anthropic's [Equipping agents for the real world with Agent Skills][agent-skills] introduces _Agent Skills_: folders of instructions, scripts, and resources that an agent discovers and loads on demand. A skill centers on a `SKILL.md` file whose metadata header carries a name and description. The format rests on progressive disclosure: metadata loads at startup, the body loads when a task triggers the skill, and bundled references and executable scripts load only as needed. Scripts offload deterministic work from token generation to code. In December 2025, Anthropic published the [Agent Skills specification][skills-spec] as an open standard.

The post is also the entry point to the craft of building skills. The standard includes its own authoring guidance, [Best practices for skill creators][skills-creation]. Anthropic's [Skill authoring best practices][skill-authoring] is a working reference for practitioners writing skills that trigger reliably and stay within context budgets. The skill-authoring discipline matches the preceding engineering guides: start from evaluation, structure for scale, and iterate against how the agent actually uses the skill. Anthropic's `skill-creator` tooling extends the same rigor to skill authoring, adding testing and benchmarking for skills.

On October 16, 2025, Simon Willison wrote in [Claude Skills are awesome, maybe a bigger deal than MCP][willison-skills] that skills' simplicity outsources the hard parts to the model and its harness. By the time Anthropic published the standard, Microsoft had already adopted Agent Skills in VS Code and GitHub, and Cursor, goose, Amp, and opencode support the format. Skills join MCP and the `AGENTS.md` file as shared interfaces, making reusable capability portable.

### DSPy

_Omar Khattab et al., Stanford NLP, October 2023; ICLR 2024._

[DSPy][dspy] is a programming model that replaces hand-crafted prompt templates with declarative modules and a compiler that optimizes entire pipelines automatically. A DSPy program declares signatures (input-output behavior), composes them into modules (analogous to neural network layers), and uses teleprompters (optimizers) to tune prompts and few-shot examples against a metric. Within minutes of compiling, a few lines of DSPy outperform standard few-shot prompting by over 25% on GPT-3.5 and by 65% on Llama 2 13B Chat, and beat pipelines built with expert-created demonstrations by up to 46%. The framework supports a broad ecosystem covering RAG pipelines, agent loops, and information extraction.

It is a widely cited source on systematic pipeline optimization, and its programming-not-prompting model is a common way to build maintainable agent systems. DSPy treats compilation as a replacement for manual prompt engineering, the same instinct behind the harness discipline's emphasis on tooling over hand-tuned prompts.

## The books

If the papers give the field its immediate ideas, these two books give it a durable foundation. _AI Engineering_ is the practitioner's guide to the full lifecycle of building and running these systems, and it's among the most recommended books in the field. _Artificial Intelligence: A Modern Approach_ is widely considered a standard AI textbook, where the concepts that agents build on get their rigorous treatment. One is for building; the other is for understanding. Both are intended to outlast any single generation of tools.

### AI Engineering

_Chip Huyen, O'Reilly, December 2024._

Chip Huyen's [AI Engineering][huyen-ai] is a comprehensive guide to the full lifecycle of production AI systems. It covers data curation, model selection, evaluation, deployment, monitoring, and continual learning. It has strong coverage of prompt engineering, retrieval-augmented generation, agent architectures, and the metrics that matter for agentic systems.

It was the most read book on the O'Reilly platform in 2025, according to the author.

### Artificial Intelligence: A Modern Approach

_Stuart Russell and Peter Norvig, fourth edition, 2020, Pearson._

Stuart Russell and Peter Norvig's [Artificial Intelligence: A Modern Approach][aima] is a widely used AI textbook. It covers search, planning, knowledge representation, probabilistic reasoning, reinforcement learning, and natural language processing. The chapters on search, planning, and multi-agent systems remain directly relevant to agent architecture.

It provides a foundation for many concepts agents build on and is often cited as a foundational text.

## A note on the selection

The sources collected here are not idiosyncratic. As of the September 2026 syllabus, Stanford's [Engineering AI Agents][cs329z] course assigns more than ten of them. UC Berkeley's [Large Language Model Agents][berkeley-mooc] _massive open online course_ (_MOOC_) assigns five across its tool-use, coding, multi-agent, and safety weeks.

## References

[anthropic-agents]: https://www.anthropic.com/engineering/building-effective-agents "Building Effective Agents"
[agents-reference-site]: https://buildingeffectiveagents.com/ "Building Effective Agents: An Engineering Reference"
[simon-willison]: https://simonwillison.net/2024/Dec/20/building-effective-agents/ "Building effective agents"
[openai-guide]: https://openai.com/business/guides-and-resources/a-practical-guide-to-building-ai-agents/ "A Practical Guide to Building Agents"
[anthropic-multi-agent]: https://www.anthropic.com/engineering/multi-agent-research-system "How we built our multi-agent research system"
[context-engineering]: https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents "Effective context engineering for AI agents"
[chroma-context-rot]: https://research.trychroma.com/context-rot "Context Rot: How Increasing Input Tokens Impacts LLM Performance"
[ng-four-strategies]: https://www.deeplearning.ai/the-batch/how-agents-can-improve-llm-performance "Four AI Agent Strategies That Improve GPT-4 and GPT-3.5 Performance"
[dont-build-multi-agents]: https://cognition.com/blog/dont-build-multi-agents "Don't Build Multi-Agents"
[multi-agents-working]: https://cognition.com/blog/multi-agents-working "Multi-Agents: What's Actually Working"
[agents-cookbook]: https://github.com/anthropics/claude-cookbooks/tree/main/patterns/agents "Building Effective Agents Cookbook"
[agents-whitepaper]: https://resources.anthropic.com/hubfs/Building%20Effective%20AI%20Agents-%20Architecture%20Patterns%20and%20Implementation%20Frameworks.pdf "Building Effective AI Agents: Architecture Patterns and Implementation Frameworks"
[12-factor-agents]: https://github.com/humanlayer/12-factor-agents "12 Factor Agents"
[12-factors-llamaindex]: https://12factors.llamaindex.ai/ "12 Factor Agents in LlamaIndex"
[omarchy]: https://omarchy.org/ "Omarchy"
[omarchy-ai]: https://omarchy.org/manual/ai/ "Omarchy: AI"
[hashimoto]: https://mitchellh.com/writing/my-ai-adoption-journey "My AI Adoption Journey"
[openai-harness]: https://openai.com/index/harness-engineering/ "Harness engineering: leveraging Codex in an agent-first world"
[anatomy]: https://www.langchain.com/blog/the-anatomy-of-an-agent-harness "The Anatomy of an Agent Harness"
[fowler-harness]: https://martinfowler.com/articles/harness-engineering.html "Harness engineering for coding agent users"
[llm-wiki]: https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f "LLM Wiki"
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
[memgpt]: https://arxiv.org/abs/2310.08560 "MemGPT: Towards LLMs as Operating Systems"
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
[deepseek-r1]: https://arxiv.org/abs/2501.12948 "DeepSeek-R1: Incentivizing Reasoning Capability in LLMs via Reinforcement Learning"
[dspy]: https://arxiv.org/abs/2310.03714 "DSPy: Compiling Declarative Language Model Calls into Self-Improving Pipelines"
[rag]: https://arxiv.org/abs/2005.11401 "Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks"
[pal]: https://arxiv.org/abs/2211.10435 "Program-Aided Language Models"
[openai-governance]: https://openai.com/index/practices-for-governing-agentic-ai-systems/ "Practices for Governing Agentic AI Systems"
[anthropic-trustworthy]: https://www.anthropic.com/research/trustworthy-agents "Trustworthy Agents in Practice"
[anthropic-rsp]: https://www.anthropic.com/responsible-scaling-policy "Responsible Scaling Policy"
[model-spec]: https://model-spec.openai.com/ "Model Spec"
[mcp]: https://modelcontextprotocol.io/specification "Model Context Protocol Specification"
[a2a]: https://a2a-protocol.org "Agent2Agent Protocol"
[agents-md]: https://agents.md/ "AGENTS.md"
[agent-skills]: https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills "Equipping agents for the real world with Agent Skills"
[skills-spec]: https://agentskills.io/specification "Agent Skills Specification"
[skills-creation]: https://agentskills.io/skill-creation/best-practices "Best practices for skill creators"
[skill-authoring]: https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices "Skill authoring best practices"
[willison-skills]: https://simonwillison.net/2025/Oct/16/claude-skills/ "Claude Skills are awesome, maybe a bigger deal than MCP"
[huyen-ai]: https://www.oreilly.com/library/view/ai-engineering/9781098166298/ "AI Engineering"
[aima]: https://aima.cs.berkeley.edu/ "Artificial Intelligence: A Modern Approach"
[cs329z]: https://cs329z.stanford.edu/ "Engineering AI Agents"
[berkeley-mooc]: https://llmagents-learning.org/f24 "Large Language Model Agents"

## License

Copyright © 2026 [edsvo](https://github.com/edsvo). Licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).

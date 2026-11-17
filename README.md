# AI agent reading list

_A curated reading list for AI agents._

Last reviewed November 2026.

A small set of sources keeps resurfacing across much of the writing about AI agents. This page collects sources that are frequently cited and relied on, grouped by what each contributes. It is written for engineers building agents based on large language models, with an emphasis on coding agents. Entries note what the source contributes and, where useful, how it was received: who endorses it, where the endorsement is qualified, and where the strongest objections come from.

The order starts with the practical design guides and moves through harness practice, evaluation, foundational methods, model papers, agent systems, governance, security, protocols, and books. The engineering guides from Anthropic and OpenAI define how agents are designed in practice and give the vocabulary to build with. The harness is everything around the model that makes it useful, from tools and memory to orchestration and verification. The term _harness engineering_ appeared in 2026. The discipline builds on the guides' language.

A few years of concentrated research produced the underlying ideas. The range runs from reasoning and self-improvement to tool use, the agent-computer interface—the boundary through which an agent perceives and acts on a computer—memory, lifelong learning, and multi-agent collaboration. The methods and model papers behind those ideas describe the capabilities that agents depend on, and the agent systems show how those capabilities are assembled. Evaluation and security become essential the moment agents connect to production systems, and governance documents define the surrounding policy layer. Protocols connect agents to the outside world—to tools, data, codebases, and packaged expertise—and a few books give the field a durable foundation.

> [!NOTE]
> Compiled with AI assistance and reviewed by the author, [@edsvo](https://github.com/edsvo). Verify claims against the linked sources before relying on them.

## Contents

- [The engineering guides](#the-engineering-guides)
  - [Supporting artifacts](#supporting-artifacts)
- [The harness](#the-harness)
- [Evaluation](#evaluation)
- [Foundational methods](#foundational-methods)
- [Model papers](#model-papers)
- [Agent architectures and systems](#agent-architectures-and-systems)
- [Safety and governance](#safety-and-governance)
- [Security](#security)
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

It's a commonly recommended starting point for agent engineering. The workflows-versus-agents distinction and the five pattern names have become a common vocabulary for workflow design. The documentation from OpenAI, LangChain, and Google Cloud makes the same distinction, and independent guides often cite the post as a key source.

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

The preceding entries set out the main positions on multi-agent design. Two supporting artifacts round out the design space. The [Building Effective Agents Cookbook][agents-cookbook] holds minimal reference implementations of the workflows that the Anthropic post describes. Dexter Horthy's [12 Factor Agents][12-factor-agents] turns the single-threaded preference into a practical checklist.

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

### DSPy

_Omar Khattab et al., Stanford NLP, October 2023; ICLR 2024._

[DSPy][dspy] is a programming model that replaces hand-crafted prompt templates with declarative modules and a compiler that optimizes entire pipelines automatically. A DSPy program declares signatures (input-output behavior), composes them into modules (analogous to neural network layers), and uses teleprompters (optimizers) to tune prompts and few-shot examples against a metric. Within minutes of compiling, a few lines of DSPy outperform standard few-shot prompting by over 25% on GPT-3.5 and by 65% on Llama 2 13B Chat, and beat pipelines built with expert-created demonstrations by up to 46%. The framework supports a broad ecosystem covering RAG pipelines, agent loops, and information extraction.

It is a widely cited source on systematic pipeline optimization, and its programming-not-prompting model is a common way to build maintainable agent systems. DSPy treats compilation as a replacement for manual prompt engineering, the same instinct behind the harness discipline's emphasis on tooling over hand-tuned prompts.

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

## Evaluation

Evaluation is how harness engineering becomes measurable. The following sources cover how to design agent evaluations, how to compare agents under realistic budgets, and how to benchmark long-horizon terminal work.

### AI Agents That Matter

_Sayash Kapoor et al., TMLR, 2025._

Sayash Kapoor et al.'s [AI Agents That Matter][agents-matter] argues that agent evaluation should account for cost, reproducibility, and real-world utility, not only benchmark accuracy. It critiques common practices such as comparing agents without controlling for cost and reporting results that are hard to reproduce. It proposes a framework for evaluating agents under realistic budgets.

The paper is a useful corrective to leaderboard-driven claims, and Stanford's Engineering AI Agents course assigns it in its first week. Its focus on cost and reproducibility complements the harness literature's emphasis on verification loops.

### Demystifying evals for AI agents

_Anthropic, January 2026._

Anthropic's [Demystifying evals for AI agents][demystifying-evals] is a practical guide to designing evaluations for agent systems. It covers task selection, grading rubrics, outcome versus trajectory evaluation, and the trade-offs between human, rubric-based, and model-based graders. It argues that evals are core infrastructure, not a final checkpoint.

The guide complements AI Agents That Matter with an implementation-oriented view: how to build the evaluation, not just why it matters. It is written from Anthropic's experience evaluating agents, so treat its recommendations as practitioner guidance rather than a controlled study.

### Terminal-Bench

_Mike A. Merrill et al., ICLR 2026._

Mike A. Merrill et al.'s [Terminal-Bench][terminal-bench] introduces Terminal-Bench 2.0, a benchmark of 89 hard, realistic tasks in terminal environments. Each task has a unique environment, a human-written solution, and tests for verification. Frontier models and agents score less than 65% on the benchmark.

The benchmark is designed to measure long-horizon terminal work rather than single-turn coding, which makes it a useful complement to SWE-bench. It is also a reminder that benchmark scores depend on the harness: the same model can perform differently under a different agent loop.

## Foundational methods

The following sources begin with two overviews of the field, then move through reasoning, retrieval, tool use, and code-as-reasoning. They are the methods and concepts that agent systems build on.

### LLM Powered Autonomous Agents

_Lilian Weng, June 2023._

Lilian Weng's [LLM Powered Autonomous Agents][weng] is a well-known overview of agent architecture. Weng frames an agent as an LLM core with three surrounding components: planning, memory, and tool use. Planning covers subgoal decomposition and reflection. Memory covers short-term context and long-term stores. Tool use covers external APIs and code execution. She shows how each component was assembled from earlier research. Written while she was at OpenAI, it is a common starting point for engineers.

The framing it named—planning, memory, and tool use—became a common vocabulary.

### A Survey on LLM-based Autonomous Agents

_Lei Wang et al., Frontiers of Computer Science, 2024._

Lei Wang et al.'s [A Survey on LLM-based Autonomous Agents][survey] is a widely cited survey of the field. It organizes agents into construction, application, and evaluation. It catalogs how agents are built: reasoning, memory, planning, tool use, and multi-agent collaboration. It also catalogs where they are deployed. Together they give a broad picture of the design space in one place.

By November 2026 the survey had drawn more than two thousand citations and was still being revised two years after publication. Its construction, application, and evaluation split has become a common way to organize the literature on agents.

### Chain-of-Thought Prompting

_Jason Wei et al., Google Research, NeurIPS 2022._

Jason Wei et al.'s [Chain-of-Thought Prompting][cot] shows that prompting for intermediate reasoning steps substantially improves performance. The gains appear on arithmetic, commonsense, and symbolic reasoning tasks.

It made reasoning visible and steerable and is an ancestor of many patterns of thinking before acting in agents.

### Self-Consistency

_Xuezhi Wang et al., Google Research, ICLR 2023._

Xuezhi Wang et al.'s [Self-Consistency][self-consistency] improves chain-of-thought by sampling multiple reasoning paths and selecting the most consistent answer. It reduces variance without additional training.

It remains a key source on sampling-based verification, a core technique in reasoning and in some evaluator patterns.

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

### Retrieval-Augmented Generation

_Patrick Lewis et al., Meta AI, NeurIPS 2020._

Patrick Lewis et al.'s [Retrieval-Augmented Generation][rag] introduces the retriever-reader architecture that grounds LLM outputs in external knowledge. It established a common approach to grounding outputs and reducing hallucination in knowledge-intensive tasks.

This paper is an early foundation for grounding, a common way agents connect to enterprise data.

### Lost in the Middle

_Nelson F. Liu et al., TACL, 2024._

Nelson F. Liu et al.'s [Lost in the Middle][lost-in-the-middle] shows that language models use long contexts unevenly. Information near the beginning and end of the context is recalled more reliably than information in the middle, even for models with large context windows. The effect persists across tasks and model families.

The finding is a primary source for context-engineering practices such as reordering, compaction, and retrieval. It is also a useful counterweight to the assumption that a larger context window removes the need to curate what enters it.

### Gorilla

_Shishir G. Patil et al., UC Berkeley, NeurIPS 2024._

Shishir G. Patil et al.'s [Gorilla][gorilla] shows that LLMs can learn to call large sets of APIs accurately. It combines retrieval over API documentation with fine-tuning, and introduces APIBench to measure API-call correctness. The resulting models outperform larger general-purpose models on API calls and are less prone to hallucinated arguments.

Gorilla addresses the gap between a model's parametric knowledge and rapidly changing APIs, a central problem for tool-using agents. It is a useful foundation for modern function calling, where API schemas and retrieval matter as much as the model's parametric knowledge.

### Program-Aided Language Models

_Luyu Gao et al., Carnegie Mellon, ICML 2023._

Luyu Gao et al.'s [Program-Aided Language Models][pal] demonstrates that offloading reasoning to executable Python code works. The model generates code instead of natural-language chains and executes it through a runtime. That approach achieves high accuracy on math benchmarks.

This paper is an early example of code-as-reasoning, the pattern behind code-writing agents and tool-use loops.

## Model papers

These papers describe the models that agents depend on. They open with the architecture that started the Transformer era, then move through pretraining and scaling to alignment and reasoning.

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

### Chinchilla

_Jordan Hoffmann et al., DeepMind, NeurIPS 2022._

Jordan Hoffmann et al.'s [Chinchilla][chinchilla] shows that most large language models were undertrained relative to their size. By scaling parameters and training data together, it derives compute-optimal training recipes and finds that a 70-billion-parameter model trained on more tokens can outperform a 280-billion-parameter model.

The result reset assumptions about model scaling and became a standard reference for compute-optimal training. It is the natural companion to GPT-3's scaling story: bigger models help, but data and compute allocation matter as much as parameter count.

### Training Language Models to Follow Instructions with Human Feedback

_Long Ouyang et al., OpenAI, NeurIPS 2022._

Long Ouyang et al.'s [Training Language Models to Follow Instructions with Human Feedback][instructgpt] introduces _reinforcement learning from human feedback_ (_RLHF_). RLHF combines supervised fine-tuning, reward modeling, and proximal policy optimization to align models with human intent. It is the method behind ChatGPT and many instruction-tuned models.

This paper turned capable models into instruction-following agents, a key reason that agent behavior is steerable.

### Direct Preference Optimization

_Rafael Rafailov et al., Stanford, NeurIPS 2023._

Rafael Rafailov et al.'s [Direct Preference Optimization][dpo] (DPO) replaces the reward-modeling stage of RLHF with a direct classification objective on preference pairs. It is simpler and more stable to train than PPO-based RLHF, and it avoids fitting a separate reward model.

DPO became a common alignment method and a useful complement to InstructGPT's RLHF pipeline. It is the main alternative to read alongside the original RLHF paper when comparing alignment approaches.

### Constitutional AI

_Yuntao Bai et al., Anthropic, December 2022._

Yuntao Bai et al.'s [Constitutional AI][constitutional-ai] replaces heavy human feedback with AI self-critique guided by a written constitution. It reduces reliance on human labelers while maintaining alignment.

Constitutional AI made written behavior rules a central mechanism for alignment training. Model specs and agent constitutions follow the same pattern.

### DeepSeek-R1

_DeepSeek-AI, January 2025._

DeepSeek-AI's [DeepSeek-R1][deepseek-r1] shows that reinforcement learning alone can produce reasoning. Its first experiment, DeepSeek-R1-Zero, trains a base model with no labeled examples, and behaviors such as verification, reflection, and backtracking emerge on their own. The released model adds a small cold-start dataset and a multi-stage pipeline. It matches OpenAI's o1 on math, code, and general reasoning benchmarks, scoring 79.8% `pass@1` on AIME 2024 against o1's 79.2%.

The open weights and distillation recipe helped launch a series of open reasoning models, making trained reasoning a common layer beneath agent harnesses.

## Agent architectures and systems

These sources describe agent architectures and systems: a cognitive architecture for language agents, coding and simulation systems, memory and lifelong-learning designs, multi-agent frameworks, and an empirical analysis of why multi-agent systems fail.

### Cognitive Architectures for Language Agents

_Theodore Sumers et al., TMLR, 2024._

Theodore Sumers et al.'s [Cognitive Architectures for Language Agents][coala] (CoALA) proposes a cognitive architecture for language agents, organized around memory modules, action spaces, and a decision-making loop. It separates working memory, episodic memory, semantic memory, and procedural memory, and maps them to agent capabilities.

CoALA gives a vocabulary for comparing agent designs and for reasoning about memory, planning, and action. It is a useful conceptual layer above individual agent systems, and it pairs well with the memory-focused systems in this section.

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

### AutoGen

_Qingyun Wu et al., Microsoft Research, COLM (Conference on Language Modeling), 2024._

Qingyun Wu et al.'s [AutoGen][autogen] is the multi-agent conversation framework that became a common way to build agent teams. It composes conversable agents: assistants, user proxies, and code executors. These agents solve tasks by passing messages and executing code. The design evolved into the Microsoft Agent Framework, which reached 1.0 in April 2026.

AutoGen became a common framework for conversation-driven agent orchestration and a widely adopted multi-agent framework. It entered maintenance mode in October 2025 and is archived.

### Why Do Multi-Agent LLM Systems Fail?

_Mert Cemri et al., NeurIPS, 2025._

Mert Cemri et al.'s [Why Do Multi-Agent LLM Systems Fail?][mast] introduces MAST, the Multi-Agent System Failure Taxonomy, built from more than 1,600 annotated execution traces across seven frameworks. It identifies 14 failure modes in three categories: system design, inter-agent misalignment, and task verification.

The paper reports failure rates between 41% and 86.7% on the systems it studies, and argues that many failures require structural redesign rather than better prompts. It supplies the empirical failure-mode study that the Anthropic-versus-Cognition debate otherwise lacks.

## Safety and governance

These four documents frame how providers govern what an agent can do in production. The first two are the providers' practical guidance on governance and on trustworthy practice. Anthropic's Responsible Scaling Policy sets a safety framework for its frontier development. OpenAI's Model Spec is a published behavioral specification for its models. Read together, they are the guardrail layer of a production agent.

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

It describes the intended guardrails in the model layer, before any agent code is written.

## Security

Security is the part of production readiness that governance documents cover only at a high level. These sources describe the main agent-specific attack path and the design patterns and threat checklists that reduce it.

### The lethal trifecta for AI agents

_Simon Willison, simonwillison.net, June 2025._

Simon Willison's [lethal trifecta][lethal-trifecta] describes the combination that enables prompt-injection data theft: access to private data, exposure to untrusted content, and the ability to communicate externally. An agent that combines all three can be tricked into sending private data to an attacker.

The post is a useful threat-model heuristic rather than a complete defense. Its practical advice is to break at least one leg of the trifecta and to treat prompt injection as an architectural problem, not a prompt-writing problem.

### Design Patterns for Securing LLM Agents against Prompt Injections

_Luca Beurer-Kellner et al., 2025._

Luca Beurer-Kellner et al.'s [Design Patterns for Securing LLM Agents against Prompt Injections][prompt-injection-patterns] proposes six architectural patterns for agents that handle untrusted input. The patterns include the Action-Selector, Plan-Then-Execute, and Dual LLM designs. Each constrains what untrusted content can influence and trades some utility for security.

The paper is a concrete complement to the lethal-trifecta heuristic: it turns the heuristic into specific architectures. The trade-offs are explicit, and the paper is honest that provable resistance often requires narrowing what the agent can do.

### OWASP Top 10 for LLM Applications

_OWASP, 2025._

The [OWASP Top 10 for LLM Applications][owasp-llm-top-10] catalogs the main security risks for LLM systems, including prompt injection, sensitive information disclosure, supply chain risks, data poisoning, and excessive agency. It maps each risk to mitigation guidance and examples.

It is a checklist rather than an agent-specific design method, but it is a useful baseline for threat modeling and security review. Use it alongside the lethal-trifecta heuristic and the design-patterns paper.

## Protocols and standards

Four standards define how agents reach the world beyond their context window. The Model Context Protocol standardizes how an assistant connects to tools and data. The Agent2Agent Protocol standardizes how independent agents discover and coordinate with each other. The `AGENTS.md` file defines how a coding agent learns what a codebase requires. Agent Skills define how reusable expertise is packaged so any agent can load and apply it. Where the safety section addresses what agents can do, the standards define how agents connect. Together they provide shared interfaces for agent integration.

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

This is a curated canon, not a systematic survey. Sources were selected from the reading lists of the Stanford and Berkeley courses cited here, recurring citations in provider engineering guides, and practitioner reading lists. Inclusion means a source contributes a distinct idea or is widely relied on; it does not mean every claim in it has been independently replicated.

Use this rough scale when weighing an entry's claims:

- **A:** The claim is peer-reviewed or independently replicated.
- **B:** The claim rests on a production case study or a provider-reported benchmark.
- **C:** The claim comes from a practitioner essay, pattern, or anecdote.

Where a claim depends on a provider's own benchmark or deployment, the entry identifies it as such. Stanford's [Engineering AI Agents][cs329z] course, as of its September 2026 syllabus, assigns more than ten of the sources here. UC Berkeley's [Large Language Model Agents][berkeley-mooc] _massive open online course_ (_MOOC_) assigns five across its tool-use, coding, multi-agent, and safety weeks.

## References

[anthropic-agents]: https://www.anthropic.com/engineering/building-effective-agents "Building Effective Agents"
[simon-willison]: https://simonwillison.net/2024/Dec/20/building-effective-agents/ "Building effective agents"
[openai-guide]: https://openai.com/business/guides-and-resources/a-practical-guide-to-building-ai-agents/ "A Practical Guide to Building Agents"
[anthropic-multi-agent]: https://www.anthropic.com/engineering/multi-agent-research-system "How we built our multi-agent research system"
[context-engineering]: https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents "Effective context engineering for AI agents"
[chroma-context-rot]: https://research.trychroma.com/context-rot "Context Rot: How Increasing Input Tokens Impacts LLM Performance"
[ng-four-strategies]: https://www.deeplearning.ai/the-batch/how-agents-can-improve-llm-performance "Four AI Agent Strategies That Improve GPT-4 and GPT-3.5 Performance"
[dont-build-multi-agents]: https://cognition.com/blog/dont-build-multi-agents "Don't Build Multi-Agents"
[multi-agents-working]: https://cognition.com/blog/multi-agents-working "Multi-Agents: What's Actually Working"
[agents-cookbook]: https://github.com/anthropics/claude-cookbooks/tree/main/patterns/agents "Building Effective Agents Cookbook"
[12-factor-agents]: https://github.com/humanlayer/12-factor-agents "12 Factor Agents"
[hashimoto]: https://mitchellh.com/writing/my-ai-adoption-journey "My AI Adoption Journey"
[openai-harness]: https://openai.com/index/harness-engineering/ "Harness engineering: leveraging Codex in an agent-first world"
[anatomy]: https://www.langchain.com/blog/the-anatomy-of-an-agent-harness "The Anatomy of an Agent Harness"
[fowler-harness]: https://martinfowler.com/articles/harness-engineering.html "Harness engineering for coding agent users"
[llm-wiki]: https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f "LLM Wiki"
[dspy]: https://arxiv.org/abs/2310.03714 "DSPy: Compiling Declarative Language Model Calls into Self-Improving Pipelines"
[anthropic-long-running]: https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents "Effective harnesses for long-running agents"
[anthropic-harness-design]: https://www.anthropic.com/engineering/harness-design-long-running-apps "Harness design for long-running application development"
[agents-matter]: https://arxiv.org/abs/2407.01502 "AI Agents That Matter"
[demystifying-evals]: https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents "Demystifying evals for AI agents"
[terminal-bench]: https://arxiv.org/abs/2601.11868 "Terminal-Bench: Benchmarking Agents on Hard, Realistic Tasks in Command Line Interfaces"
[weng]: https://lilianweng.github.io/posts/2023-06-23-agent/ "LLM Powered Autonomous Agents"
[survey]: https://arxiv.org/abs/2308.11432 "A Survey on LLM-based Autonomous Agents"
[cot]: https://arxiv.org/abs/2201.11903 "Chain-of-Thought Prompting Elicits Reasoning in Large Language Models"
[self-consistency]: https://arxiv.org/abs/2203.11171 "Self-Consistency Improves Chain of Thought Reasoning in Language Models"
[react]: https://arxiv.org/abs/2210.03629 "ReAct: Synergizing Reasoning and Acting in Language Models"
[tot]: https://arxiv.org/abs/2305.10601 "Tree of Thoughts: Deliberate Problem Solving with Large Language Models"
[reflexion]: https://arxiv.org/abs/2303.11366 "Reflexion: Language Agents with Verbal Reinforcement Learning"
[rag]: https://arxiv.org/abs/2005.11401 "Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks"
[lost-in-the-middle]: https://arxiv.org/abs/2307.03172 "Lost in the Middle: How Language Models Use Long Contexts"
[gorilla]: https://arxiv.org/abs/2305.15334 "Gorilla: Large Language Model Connected with Massive APIs"
[pal]: https://arxiv.org/abs/2211.10435 "Program-Aided Language Models"
[attention]: https://arxiv.org/abs/1706.03762 "Attention Is All You Need"
[bert]: https://arxiv.org/abs/1810.04805 "BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding"
[gpt3]: https://arxiv.org/abs/2005.14165 "Language Models are Few-Shot Learners"
[chinchilla]: https://arxiv.org/abs/2203.15556 "Training Compute-Optimal Large Language Models"
[instructgpt]: https://arxiv.org/abs/2203.02155 "Training Language Models to Follow Instructions with Human Feedback"
[dpo]: https://arxiv.org/abs/2305.18290 "Direct Preference Optimization: Your Language Model is Secretly a Reward Model"
[constitutional-ai]: https://arxiv.org/abs/2212.08073 "Constitutional AI: Harmlessness from AI Feedback"
[deepseek-r1]: https://arxiv.org/abs/2501.12948 "DeepSeek-R1: Incentivizing Reasoning Capability in LLMs via Reinforcement Learning"
[coala]: https://arxiv.org/abs/2309.02427 "Cognitive Architectures for Language Agents"
[swe-agent]: https://arxiv.org/abs/2405.15793 "SWE-agent: Agent-Computer Interfaces Enable Automated Software Engineering"
[gen-agents]: https://arxiv.org/abs/2304.03442 "Generative Agents: Interactive Simulacra of Human Behavior"
[memgpt]: https://arxiv.org/abs/2310.08560 "MemGPT: Towards LLMs as Operating Systems"
[voyager]: https://arxiv.org/abs/2305.16291 "Voyager: An Open-Ended Embodied Agent with Large Language Models"
[autogen]: https://arxiv.org/abs/2308.08155 "AutoGen: Enabling Next-Gen LLM Applications via Multi-Agent Conversation"
[mast]: https://arxiv.org/abs/2503.13657 "Why Do Multi-Agent LLM Systems Fail?"
[openai-governance]: https://openai.com/index/practices-for-governing-agentic-ai-systems/ "Practices for Governing Agentic AI Systems"
[anthropic-trustworthy]: https://www.anthropic.com/research/trustworthy-agents "Trustworthy Agents in Practice"
[anthropic-rsp]: https://www.anthropic.com/responsible-scaling-policy "Responsible Scaling Policy"
[model-spec]: https://model-spec.openai.com/ "Model Spec"
[lethal-trifecta]: https://simonwillison.net/2025/Jun/16/the-lethal-trifecta/ "The lethal trifecta for AI agents"
[prompt-injection-patterns]: https://arxiv.org/abs/2506.08837 "Design Patterns for Securing LLM Agents against Prompt Injections"
[owasp-llm-top-10]: https://genai.owasp.org/llm-top-10/ "OWASP Top 10 for LLM Applications"
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

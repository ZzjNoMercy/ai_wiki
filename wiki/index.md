# Wiki Index

## analysis

- [[analysis/pi-agent-learning-resources|Pi Agent 学习资源评估]] — 评估四个 Pi Agent 学习网站，回答“从 agent 小白到有基础，分别适用哪个”。四个站的主题是同一个 pi 项目，但层级不同。

## company

- [[companies/jetbrains|JetBrains]] — JetBrains 是推动 Agent Client Protocol（concepts/agentclientprotocol）标准化的公司之一。 官网：https://jetbrains.com JetBrains 生态中的 Junie（Junie by JetBrains）出现在 ACP 官方兼容智能体（Agents）列表中。 JetBrains 的 
- [[companies/zed-industries|Zed Industries]] — Zed Industries 是推动 Agent Client Protocol（concepts/agentclientprotocol）标准化的公司之一（与 JetBrains 等共同推动）。raw 将其定位类比为 LSP 标准化语言服务器集成那样，为“编辑器 ↔ 编码智能体”这一组合提供统一交互标准。 官网：https://zed.dev 其产品 Ze

## concept

- [[concepts/adversarial-review|对抗式审查]] — Vibe Coding 中"管验证"的审查 Prompt 技巧：让 AI 站在"如果我是一个恶意用户……"的角度，以攻防方式对系统/代码找漏洞，用于开发完成后的测试/验证阶段。作者称自己现在做开发，最后的测试流程几乎必然是对抗式审查。
- [[concepts/agent|Agent（智能体）]] — Agent 是由模型与其外层执行系统共同构成、能够在上下文中规划并通过工具采取行动的运行系统。模型提供核心智能；concepts/harness 围绕模型编排执行，管理上下文与持久状态，连接工具和现实环境，并承担约束、验证与纠正。
- [[concepts/agent-client-protocol|Agent Client Protocol (ACP)]] — Agent Client Protocol（ACP）是一个标准化的通信协议，用于 AI 智能体（agent）与客户端应用（client，如代码编辑器/IDE）之间的通信。它由 Zed Industries、JetBrains 等推动，定位类似于 LSP（Language Server Protocol）标准化语言服务器集成那样，为“编辑器 ↔ 编码智能体”这
- [[concepts/agent-self-improvement|Agent Self-Improvement]] — Agent SelfImprovement（Agent 自我改进）是一个宽概念：concepts/agent 根据任务结果、评估或人类反馈，修改能被后续运行复用的组成部分，使后续行为有机会改善。可修改对象不只包括模型权重，也包括提示词、结构化上下文、Skill、工作流、Harness 代码，乃至负责改进这些对象的优化器代码。
- [[concepts/compiled-rag|编译式 RAG]] — 编译式 RAG（Compiled RAG）是一种检索增强生成范式，其核心主张是：先将原始资料一次性编译成结构化知识库（wiki），查询时直接读取编译产物，而非每次实时检索原始文档。
- [[concepts/context-compaction|Compaction（上下文压缩）]] — Compaction（上下文压缩）是一种在对话历史超过大语言模型上下文窗口限制时，把历史变成更小的表示、让当前会话得以继续的处理方式。本页依据 Pi 的 Compaction 实现介绍（基于 Earendil Engineering 官方博客文章《How Compaction Works in Pi》整理）编译。
- [[concepts/first-principles|第一性原理（Prompt 技巧）]] — Vibe Coding 中"管生成"的 Prompt 技巧：平时怎么说就怎么说，只需要在最后加一句"从第一性原理出发"。作者称加了这一句话后，Agent 写方案、找 BUG 的能力都进化了一大截。
- [[concepts/harness|Harness（模型外层执行系统）]] — Harness 是围绕基础模型的模型外层执行系统。它编排执行，并决定模型如何思考与规划、如何调用工具与行动、如何感知和管理上下文、如何保存工件，以及如何评价结果。
- [[concepts/loop-engineering|Loop Engineering（循环工程）]] — Loop Engineering（循环工程）是一个关于如何构建长时程（longhorizon）AI Agent 执行体系的概念：它强调工程化 Agent 外部的执行、验证、纠错与恢复闭环，而不是只优化单轮 Prompt。
- [[concepts/model-context-protocol|Model Context Protocol (MCP)]] — 本页只记录本次 raw 直接支持的事实——MCP 在 concepts/agentclientprotocol 生态中的角色与集成方式；不补写外部定义。
- [[concepts/multi-agent|Multi-Agent（多智能体）]] — MultiAgent（多智能体）是由多个 Agent 共同参与同一任务或系统的组织形式，不限定为固定工作流。它可以表现为主 Agent 调度专业子 Agent、多个 Agent 并发协作，或按角色划分的短生命周期执行单元。
- [[concepts/recursive-self-improvement|递归自我改进（Recursive Self-Improvement, RSI）]] — RSI 概念可追溯至 I. J. Good（1965）的“超智能机器”（ultraintelligent machine）：一个能在所有智力活动中超越人类、并能设计出更好的机器来改进自身的系统。 Yudkowsky（2008）用“recursive selfimprovement”指代一个特定反馈回路：AI 用当前智能来改进产生其智能的认知机制（cognit
- [[concepts/vibe-coding|Vibe Coding]] — 一种以 AI 为核心参与写代码的编码方式，非专业程序员也可依赖 AI 完成开发。

## engineering_practice

- [[practices/agent-context-compaction|Agent 上下文 Compact 工程实践]] — 面向长时运行 Agent 的上下文压缩（Compact）工程实践：结构化滚动摘要、完整历史与模型投影分离、最近上下文保留、事务式检查点、溢出恢复和工具协议边界。本实践适用于（appliesto）systems/opencode，OpenCode 是当前记录的最完整实现。
- [[practices/agent-search-minimal-context|Agent 搜索最小上下文实践]] — 编码 Agent 经常需要在大型 Workspace 中定位文件和符号。传统做法按次启动 rg 或 fd，把扫描顺序中的大量命中直接塞入模型 Context。高频关键词（如 TODO、FIXME）可能一次返回几十条甚至更多结果；若噪声文件排在前面，真正相关的文件会被挤到结果末尾或被输出上限截断。
- [[practices/agent-workload-productization-ladder|Agent 工作负载产品化阶梯：从 Raw Session 到 Specialized Agent]] — 开放式 Agent Session 适合探索，但用户需要自行提供上下文和 Steering，任务边界不固定，只能以 Session 粗略计价。随着使用量增长，这种形态难以稳定 Benchmark、路由模型、约束质量，也难把每一美元映射到实际完成的工作。
- [[practices/benchmark-driven-agent-model-routing|基于真实任务 Benchmark 的 Agent 模型路由]] — 模型排行榜或通用编码分数不能说明某个 Agent 在特定代码库、Harness 和任务边界中的真实完成率。默认使用最强模型会提高成本，默认使用最便宜模型则可能降低质量、增加超时和重试。
- [[practices/cross-harness-behavior-distillation|跨 Harness 行为蒸馏实践]] — 本实践从 papers/harnesszero 的实验流程提炼，目标是把领域专用 concepts/harness 引导出的可重现行为，转成固定部署 Harness 下可执行、可观察的训练示范。它描述的是论文在三个任务域验证过的一种做法，不是任意专用 Harness 都能被完整压缩进模型的保证。
- [[practices/durable-effect-ledger|EffectLedger：Agent 副作用账本]] — EffectLedger 是一种面向长运行 Agent 的工程实现：把每次可能产生外部副作用的操作，记录成可恢复、可去重、可审计的持久化状态机。它采用 Intent–Effect–Settlement（IES） 协议：先持久化执行意图，再执行副作用，最后持久化结果。
- [[practices/evidence-driven-bounded-harness-evolution|证据驱动的受约束 Harness 演化闭环]] — 这是一套把 SelfHarness 与 Agentic Harness Engineering（AHE）抽象成工程流程的方法：Agent 可以根据运行证据修改 concepts/harness，但修改必须发生在声明的可编辑面内，并经过独立验证、受控晋级和可逆部署。它 derivedfrom media/harnessengineeringforselfimp
- [[practices/handoff-briefing-compaction-summary|交接简报式 Compaction 摘要实践]] — 编码 Agent（如 Pi、Claude Code、Codex）的对话历史随工作推进持续增长。每次交互 Agent 都会把完整对话历史发给 LLM，一旦超过上下文窗口上限，下一次请求就会返回类似 Request exceeds the maximum size 的错误，无法继续。直接开启新会话会丢失先前的决策和未完成的工作。
- [[practices/harness-profile-tool-description-overrides|HarnessProfile 工具描述覆盖实践]] — 需要调整 frameworks/deepagents 内置工具向模型暴露的说明时，优先使用公共 API HarnessProfile.tooldescriptionoverrides 覆盖工具描述，而不是修改依赖源码；行为原则同时写入 Tool Guide，确保模型看到的工具 Schema 与系统指导语义一致。本实践 appliesto frameworks
- [[practices/harness-self-improvement-challenge-checklist|Harness 自我改进七项挑战检查框架]] — 这是一套面向 Agent 与自我改进 Harness 的工程评审方法：将 Lilian Weng 在《Harness Engineering for SelfImprovement》中列出的七项未来挑战，转译为可在方案设计、开发复盘、学习和面试中反复使用的检查框架。它 derivedfrom media/harnessengineeringforselfim
- [[practices/multi-agent-token-cost-optimization|Multi-Agent Token 成本优化实践]] — MultiAgent 系统把需求分析、编码、审查、测试和视觉验证分派给多个 Agent 后，系统提示词、工具 Schema、工具结果、代码和文档、长期记忆以及不断增长的历史消息会在多轮调用中重复进入模型。系统可以完成任务，但成本随 Agent 数量和轮次快速增长，而且若缺少按 Wave、Trace 或 Session 的度量，很难判断 Token 花在哪里。
- [[practices/outcome-based-agent-economics|面向结果的 Agent 单位经济性]] — 只看总支出、Token 单价或每 Session 成本，会把采用增长、交互深度、模型变化和真实产出混在一起。成本下降可能只是使用结构变化，成本上升也可能来自完成了更多高价值工作。
- [[practices/periodic-adversarial-review|定期全局对抗式审查]] — Vibe Coding 产出的代码漏洞多；问题如果不主动去找，就会一直潜伏，直到某天突然爆发。同时，表层修复往往治标不治本：例如 AIHOT 的 OpenAI 抓取故障，只把 OpenAI 的抓取单独修好，底层流量路由隐患仍在，未来其他信源还会出问题，"缝缝补补，最后堆成一座屎山"。
- [[practices/skill-based-agent-self-improvement-loop|基于 Skill 的 Agent 自我改进闭环]] — Agent 会反复产生无帮助的评论或低质量输出。人工逐次重写 prompt 难以扩展，而只存在于单次会话中的纠正无法被后续运行继承。

## media

- [[media/agent-client-protocol-official-tutorial|Agent Client Protocol 官方教程]] — 标题/署名：Agent Client Protocol 官网发布的入门教程（Get Started）。 发布平台：Agent Client Protocol 官网（agentclientprotocol.com）。 原文 URL：https://agentclientprotocol.com/（入门教程：/getstarted/introduction、/g
- [[media/cs329z-engineering-ai-agents|CS 329Z: Engineering AI Agents]] — CS 329Z 是 Stanford 2026 年秋季课程，主题是 Agentic Systems 工程。课程从简单 LLM pipeline、compound AI system 延伸到 autonomous agent，强调问题分解、组件选择、数据收集与整理、评测，以及实际系统设计中的权衡。
- [[media/deepseek-of-deepseek-harness|The deepseek of DeepSeek Harness: Overengineering or built for self-evolution?]] — 标题：The deepseek of DeepSeek Harness: Overengineering or built for selfevolution? 署名：Zhenjia 发布平台：Life Odyssey（zhenjia.dev），见 sources/lifeodyssey。 原文 URL：https://zhenjia.dev/posts/t
- [[media/dg-zhuya-pi-reading-notes|冬瓜 · 源码精读笔记（Pi Agent）]] — 生产级 Pi Agent 全景精读笔记，10 章完整精读、约 5 万字。
- [[media/harness-engineering-for-self-improvement|Harness Engineering for Self-Improvement]] — 这篇文章系统梳理了 Harness 如何从人工编写的模型外层执行系统，逐步变成可被搜索、评估和迭代的优化对象。其核心判断是：近期可落地的自我改进更可能先发生在 Prompt、上下文、工作流和 Harness 代码层，而不是由模型可靠地直接重写自身权重；但越深入地修改系统，越需要把评估器、权限、安全边界、审计和晋级控制放在自我修改循环之外。
- [[media/how-compaction-works-in-pi|How Compaction Works in Pi]] — 标题：How Compaction Works in Pi 署名/发布方：Earendil Engineering 官方博客 平台：Earendil Engineering 官方博客（earendil.com） 原文 URL：https://earendil.com/posts/compactioninpi/ 收录说明：本页依据 conversationsy
- [[media/how-warp-builds-self-improving-agents-on-claude|How Warp builds self-improving agents on Claude]] — 标题：How Warp builds selfimproving agents on Claude 原始署名：Michael Segner 发布平台：Claude（claude.com/blog），见 sources/claudeblog。 原文 URL：https://claude.com/blog/howwarpbuildsselfimprovingag
- [[media/multi-agent-token-cost-optimization|靠这10个优化点，我们把Multi-Agent工作流成本降了50%以上]] — 发布平台：微信公众平台。 发布渠道：sources/tencentprogrammer（sourcedfrom）。 Raw 元数据的 author 字段为“腾讯程序员”；正文署名为 lemonye。发布渠道、元数据作者字段和正文署名分别保留，不互换身份。 原文 URL：https://mp.weixin.qq.com/s/TIdXNlrcAOUZWVW1oW
- [[media/pi-agent-book|π-agent book（books.antinomie.org）]] — piagentcore 逐行源码导读——对象是 piagentcore 这个库本身（不是整个 pi）。进行中的书，每周只更一章，目前是预览版。
- [[media/pi-agent-runbook-tutorial|菜鸟教程 · Pi Agent 教程]] — 纯“使用手册”型教程，完全不碰源码，零门槛。
- [[media/pi-from-scratch|PI from Scratch]] — 基于生产级 pi（上万行）提炼的 nanopi（600 行） 教学版，用一篇文章跟着数据流把五个 TS 文件从头写出来。
- [[media/running-a-software-factory-efficiently-at-uber-scale|Running a Software Factory Efficiently at Uber Scale]] — 原始署名：Uday Kiran Medisetty，Distinguished Engineer 发布平台：Uber 发布日期：20260827 原文：https://www.uber.com/us/en/blog/efficientsoftwarefactory/
- [[media/vibe-coding-two-essential-prompts|分享2个Vibe Coding必备的超实用Prompt。]] — 标题：分享2个Vibe Coding必备的超实用Prompt。 发布平台：微信公众平台 账号：数字生命卡兹克 署名：卡兹克 原文 URL：https://mp.weixin.qq.com/s/umPqTDIubbhXIgiS47eQ 采集时间：20260804 文末投稿/爆料邮箱：wzglyay@virxact.com sourcedfrom：sources

## research_paper

- [[papers/harness-zero|Harness-Zero: Harness Distillation via Agent-as-Harness]] — 作者：Haoran Ye、Yuxing Lu、Haonan Dong、Zhaochen Su、Guojie Song。2026 年预印本，arXiv:2609.24974v1；[论文](https://arxiv.org/abs/2609.24974)，[代码](https://github.com/metaevoai/harnesszero)。以下结论限于
- [[papers/preregistered-critique-compiled-rag|预注册证伪实验：编译式 RAG 成本分析]] — 本实验采用预注册对照设计（preregistered controlled experiment），对编译式 RAG（concepts/compiledrag）与向量 RAG 的查询成本进行了系统性对比。
- [[papers/spatiotemporal-composability|A Programming Paradigm for Spatiotemporal Composability]] — 作者：Yifan Shi, Wei Zhang, Tianyi Cui 类型：Preprint，2026 标识：https://github.com/cordiverse/paper 收录说明：随 frameworks/deepseekharness 一起发布的论文，描述“时空可组合性”（spatiotemporal composability）编程范式；f

## software_framework

- [[frameworks/browser-use|Browser Use]] — Browser Use 是独立的开源浏览器自动化框架（Browser Use CLI 3.0）。它本身是一个 Python 库/CLI，可以单独用来驱动本地 Chrome 或 Browser Use 云浏览器，拥有自己的 GitHub 项目和文档，不依赖任何特定 Agent 环境。
- [[frameworks/cordis|Cordis]] — Cordis 是 frameworks/deepseekharness 的底层插件框架：DSH 的 .cordis.yml 官方约定名即源于它（文中“底层框架叫 Cordis”）。
- [[frameworks/deepagents|DeepAgents]] — DeepAgents 属于 LangChain 生态，但不等同于 LangChain。官方将其定义为一个独立的、开箱即用的 Agent Harness 库：它建立在 frameworks/langchain 的 Agent 核心构件之上（dependson），复用模型、工具和 Agent 循环等基础抽象；同时使用 frameworks/langgraph 作
- [[frameworks/deepseek-harness|DeepSeek Harness（DSH）]] — DeepSeek 于 2026 年 8 月发布的 Agent Harness 框架（文中基于 0.1.0 版本分析）。其设计口号是“一切皆插件”（Everything is Plugin）：整个 DeepSeek Harness 没有任何一个地方是核心，所有的地方都是插件。
- [[frameworks/fff|FFF]] — FFF 是 Rust 原生、SIMD 加速的文件查找与内容搜索库。面向编码 Agent 在大型 Workspace 中定位文件和符号的场景，在普通扫描之上提供预索引、排序与分页能力。
- [[frameworks/gbrain|GBrain]] — GBrain 是编译式 RAG（concepts/compiledrag）的开源生产级实现，将"先编译后查询"的理念落成了包含完整子系统的工程框架。
- [[frameworks/graphify|Graphify]] — Graphify 是文章所使用的代码图谱工具。它通过 AST 和语义为代码仓库建立文件索引与依赖关系，使 Agent 在读取代码前先查询图谱、缩小候选文件范围，再读取少量目标文件。
- [[frameworks/langchain|LangChain]] — LangChain 为 Agent 提供核心构件、模型与工具抽象。frameworks/deepagents 建立在 LangChain 的 Agent 核心构件之上（dependson），复用模型、工具和 Agent 循环等基础抽象。
- [[frameworks/langgraph|LangGraph]] — LangGraph 为 frameworks/deepagents 提供持久执行、状态、流式处理和 Humanintheloop 运行时；DeepAgents 使用 LangGraph 作为运行时（uses）。
- [[frameworks/long-horizon-harness|LongHorizon-Harness]] — LongHorizonHarness 是 AMAPML 组织下的 GitHub 仓库（[AMAPML/LongHorizonHarness](https://github.com/AMAPML/LongHorizonHarness)），含中文说明 README.zhCN.md。raw 将其作为「Loop Engineering」概念的文献来源之一收录。
- [[frameworks/pi-agent|Pi Agent]] — Pi Agent 是一个覆盖模型适配、Agent loop 与 codingagent 使用层的 Agent SDK 框架。本页同时区分它当前实际运行的 agent loop，以及仓库中仍处于实验性脚手架阶段的 Harness v2。
- [[frameworks/pi-fff|@ff-labs/pi-fff]] — @fflabs/pifff 是把 FFF 接入 Pi 的包：通过 Node binding 在进程内调用 FFF，不需要为每次搜索启动一个新的 rg 子进程，并在 Session 启动后为工作目录建立后台索引。上游包页面：https://pi.dev/packages/@fflabs/pifff 。
- [[frameworks/rtk|rtk]] — rtk 是一个开源 CLI 代理，在命令执行前进行拦截，把 git status、npm test、docker ps 等命令的原始输出重写为压缩版本，以减少进入 Agent Context 的噪声和重复 Token。

## source

- [[sources/agent-client-protocol-official|Agent Client Protocol 官网]] — 平台：Agent Client Protocol 官网（agentclientprotocol.com）。 说明：Agent Client Protocol（concepts/agentclientprotocol）的官方文档站。官网首页 https://agentclientprotocol.com/；入门教程路径 /getstarted/introduc
- [[sources/claude-blog|Claude Blog]] — 渠道名：Claude Blog。 平台：Claude（claude.com/blog）。 说明：首次建页仅记录本次 Raw 直接支持的信息；本次收录内容来自 Claude 的博客页面。
- [[sources/digital-life-kazik|数字生命卡兹克（微信公众平台）]] — 平台：微信公众平台 账号名：数字生命卡兹克 文章署名：卡兹克 投稿/爆料邮箱（文中提供）：wzglyay@virxact.com
- [[sources/earendil-engineering|Earendil Engineering 官方博客]] — 渠道名：Earendil Engineering 官方博客 平台：博客（earendil.com） 说明：首次建页，仅记录本次 raw 直接支持的信息——Earendil Engineering 官方博客是发布工程文章的平台，本次 raw 引用了其中的《How Compaction Works in Pi》（https://earendil.com/post
- [[sources/life-odyssey|Life Odyssey（zhenjia.dev）]] — 渠道名：Life Odyssey 平台：个人博客（zhenjia.dev） 说明：首次建页，仅记录本次 raw 直接支持的信息——Life Odyssey 是 zhenjia.dev 上的博客站点；本次 raw 收录的文章署名为 Zhenjia。 已收录内容： media/deepseekofdeepseekharness
- [[sources/lil-log|Lil'Log（Lilian Weng 的博客）]] — 频道名：Lil'Log（lilianweng.github.io）。 平台：博客（lilianweng.github.io）。 说明：首次建页，仅记录本次 raw 直接支持的信息——Lil'Log 是作者 Lilian Weng 的博客站点；本次 raw 收录的文章为《Harness Engineering for SelfImprovement》（http
- [[sources/runoob|菜鸟教程（runoob.com）]] — 稳定的中文教程平台。本页为首次建页，只记录 raw 直接支持的平台信息与已收录内容。
- [[sources/tencent-programmer|腾讯技术工程（微信公众号）]] — 渠道名：腾讯技术工程。 平台：微信公众平台。 身份边界：本次收录文章的 Raw 元数据 author 字段为“腾讯程序员”，正文署名为 lemonye；用户校正确认发布渠道是“腾讯技术工程”微信公众号。三者不作为同一自然人处理。
- [[sources/uber-blog|Uber Blog]] — Uber 的公开博客发布渠道。本页仅记录当前 Raw 直接支持的信息，不把平台、账号与自然人署名混为一谈。

## system

- [[systems/aihot|AIHOT]] — AIHOT 是微信公众号《分享2个Vibe Coding必备的超实用Prompt。》一文作者（署名卡兹克）以纯 Vibe Coding 方式构建的自研 Agent 产品。作者自称"纯粹的不懂代码的小白"，完全依靠 AI 进行开发（concepts/vibecoding，uses）。
- [[systems/hermes-agent|Hermes Agent]] — Hermes Agent 是 Nous Research 出品的 AI 智能体，内置完整的浏览器自动化工具集，支持多种后端模式。本页依据 Hermes Agent 浏览器自动化功能文档（https://hermesagent.nousresearch.com/docs/userguide/features/browser）的会话整理编译。
- [[systems/maka|Apache Maka]] — Apache Maka 是一个本地优先的 Agent 工作空间。它的关键差异不只是“支持工具调用”，而是把工具调用的执行边界纳入持久化 Runtime：先提交执行意图，再允许外部副作用发生，最后提交执行结果。
- [[systems/opencode|OpenCode]] — OpenCode 是一个 Agent 平台，其会话压缩（Compact）实现记录于本地源码 源码合集/opencode（证据 commit：c387fe190bbd22e9396d264effe242d157f866d2，分析日期 20260805）。本页只描述该 commit 的源码事实，不代表其他版本保持完全相同的实现。
- [[systems/pi-coding-agent|Pi（编码 Agent）]] — Pi 是一种编码 Agent，与 Claude Code、Codex 同类：每次请求都会把系统提示词、加载的文件（如 AGENTS.md）、工具定义以及持续增长的对话历史一起发送给大语言模型。本页依据 Pi 的 Compaction（上下文压缩）实现介绍编译，该介绍基于 Earendil Engineering 官方博客文章《How Compaction W
- [[systems/puddingclaw|PuddingClaw]] — PuddingClaw 通过 frameworks/deepagents 暴露的 Harness 定制接口（HarnessProfile、registerharnessprofile、tooldescriptionoverrides）覆盖工具描述（uses）。这些接口不是 LangChain 本身的同名 API。
- [[systems/puddingteams|PuddingTeams]] — PuddingTeams 将 FFF 作为受控 Harness 能力装配，而不是要求用户把 pifff 手工安装为 Pi 全局 Extension。受控装配用于保证配置、Workspace 和索引边界一致，并过滤可能绕过平台边界的全局 pifff 实例。
- [[systems/uber-software-factory|Uber Software Factory]] — Uber Software Factory 是 Uber 围绕软件开发生命周期组织 Agent 工具、Skill、通用 Agent 与专用托管 Agent 的工程体系。它覆盖 Code Gen、Validate、Deploy、Observe、Maintain，并用统一的成本、质量、模型和上下文治理支持本地及云端执行。

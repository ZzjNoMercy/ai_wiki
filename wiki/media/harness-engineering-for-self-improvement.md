---
title: Harness Engineering for Self-Improvement
type: media
sources:
  - read-later-4445b9b74d/later_abadf6c614f140da9b1d1fb5-0fce80db03-bcc916448a53.md
  - conversation-correction-codex-multi-agent-token-20260830-1c071d3fe3/multi-agent-token-wiki-scope-v2-c875e42ad4-adddccc75aa9.md
created: 2026-08-29
updated: 2026-09-27
schema_version: 0.4.0
---

# Harness Engineering for Self-Improvement

这篇文章系统梳理了 Harness 如何从人工编写的模型外层执行系统，逐步变成可被搜索、评估和迭代的优化对象。其核心判断是：近期可落地的自我改进更可能先发生在 Prompt、上下文、工作流和 Harness 代码层，而不是由模型可靠地直接重写自身权重；但越深入地修改系统，越需要把评估器、权限、安全边界、审计和晋级控制放在自我修改循环之外。

## 来源信息

- 标题/署名：Harness Engineering for Self-Improvement，作者 Lilian Weng。
- 发布平台：Lil'Log（lilianweng.github.io）。
- 原文 URL：https://lilianweng.github.io/posts/2026-07-04-harness/
- 发布月份：2026 年 7 月（原文引用条目标注 Jul 2026）。
- 收录说明：本页依据本次 Raw 全文编译。以下“解释”是对文章结构和公式的中文整理，不把文章引用的论文二次表述为已经由本 Wiki 独立核验的原始研究结论。

## 两个 Agent 公式回答不同问题

文章回顾的能力组成公式是：

```text
Agent = LLM + memory + tools + planning + action
```

它回答“一个 Agent 需要哪些能力”：LLM 提供生成与推理，memory 保存可复用信息，tools 扩展可执行能力，planning 组织步骤，action 让系统作用于外部环境。

从系统架构视角，[[concepts/agent|Agent（智能体）]] 还可以概括为：

```text
Agent = Model + Harness
```

它回答“这些能力由谁承载”：Model 提供核心智能；[[concepts/harness|Harness（模型外层执行系统）]] 负责组织 Prompt、上下文、记忆、工具、工作流、权限、持久状态和评价。两个公式不是竞争定义，而是能力清单与实现分层两种视角。

## Harness 的共同设计模式

文章归纳出几种反复出现的系统设计：

- **工作流自动化**：将计划、执行、观察或测试、改进、重新执行组成循环，而不是依赖一次模型调用完成长任务。
- **文件系统作为持久记忆**：把计划、代码、日志、实验结果和状态写成可检索工件，避免把全部历史塞入单个上下文窗口。
- **子 Agent 与后台任务**：并行处理、隔离上下文或执行耗时工作，但保持任务状态和结果可检查。
- **编码 Agent 工具面**：以 Read、Write、Edit、Bash、Glob、Grep、TodoWrite 等通用工具，让模型在可执行环境中读取、修改和验证系统。

这些模式说明 Harness 更接近运行时与软件系统设计，而不只是 Prompt 模板。

## 优化对象的递进

文章给出的主线可以整理为：

```text
instruction prompt
    → structured context
    → workflow
    → harness code
    → optimizer code
    → harness 与 model weights 联合更新
```

每向右一步，修改的不再只是“给模型说什么”，而是“如何构造模型输入”“如何组织多步执行”“什么代码控制 Agent”“谁来改进这个改进机制”。这也是 [[concepts/agent-self-improvement|Agent Self-Improvement]] 从浅层适配走向递归结构的路径。

一个重要但容易误读的结论是：许多外层算法仍是双层优化、树搜索、进化搜索等经典方法，新的部分主要是 LLM 能在自然语言、代码和 Agent 架构中提出有语义的候选。因此，本 Wiki 的阶段判断是：**LLM 把经典优化算法从结构化参数空间，扩展到了自然语言、代码和 Agent 架构等开放的语义空间。**这提升的首先是候选表示与设计空间，并不保证计算更便宜或评价更可靠。

## 方法地图：按“优化什么”分类

| 优化对象 | 代表方法 | 关键变化 |
| --- | --- | --- |
| 结构化上下文 | ACE | 从不断增长的 Prompt 转为可增量维护的上下文 playbook |
| 上下文生成方法 | MCE | 同时优化 Skill 与由 Skill 条件化的上下文函数 |
| Harness 代码 | Meta-Harness | 让编码 Agent 提议、评估并留档 Harness 候选 |
| Agent 工作流 | AI Scientist、ScientistOne、Autodata、ADAS、AFlow | 从专家手工流水线走向可执行工作流搜索 |
| 改进器与完整 Harness | STOP、Self-Harness、AHE | 改进“如何改进”，并加入证据、边界和非回归验证 |
| 候选种群 | Promptbreeder、GEPA、AlphaEvolve、ShinkaEvolve、DGM | 用 LLM 进行语义变异，以自动评估选择后代 |
| Harness 与权重 | SIA、Continual Harness | 尝试在同一循环中选择外层系统更新或参数更新 |

## 上下文优化：ACE、MCE 与 Meta-Harness

### ACE：维护结构化上下文

Agentic Context Engineering（ACE）把上下文视为持续演化的 playbook：Generator 产生任务轨迹，Reflector 从成功与失败中提炼经验，Curator 以带标识的条目增量更新上下文。它避免反复重写整段 Prompt 导致信息坍缩，但更新规则和总体工作流仍由人设计。

### MCE：同时优化“上下文”和“生成上下文的方法”

Meta Context Engineering（MCE）中的 Skill 不是某一份最终上下文，而是生成和管理上下文的方法。一个 Skill `s` 定义上下文函数：

```text
c_s = (ρ_s, F_s)
c = F_s(x; ρ_s)
```

- `ρ_s` 是静态组件，如 Prompt、知识库和代码库；
- `F_s` 是动态算子，如检索、选择、筛选和格式化；
- 输入 `x` 经过这些组件和算子后，才得到本次真正交给模型的上下文 `c`。

其双层优化可以通俗理解为：

```text
内层：固定一个 Skill s，在训练任务上找到它能构造出的最佳上下文 c_s*
外层：比较不同 Skill 产生的最佳上下文在验证集上的表现，选择最佳 Skill s*
```

外层不是把某个 Skill 的最佳上下文直接复制给所有其他任务，而是在验证数据上比较“哪一种上下文工程方法更能泛化”。随后，基础层上下文工程 Agent 会在新任务、当前 Skill、上一轮上下文和 rollout 反馈的共同约束下，继续生成或改进新的上下文函数。

实现上，上下文函数被实例化为专用目录中的一组文件：`skill.md` 保存较稳定的方法、规则和知识，context 与 data rollouts 保存动态工件。因此，`skill.md` 可以理解为“如何生成上下文的方法论”的核心载体，但它不等于整个上下文函数；完整函数还包括动态算子、数据、运行轨迹和生成出的上下文。

### Meta-Harness：把 Harness 代码本身变成候选

Meta-Harness 比 MCE 再向外一层。MCE 主要优化上下文函数；Meta-Harness 优化决定信息如何存储、检索、呈现和执行的 Harness 代码。可以把它理解为在现有 Harness 之外外挂一个专门修改 Harness 的编码 Agent：

1. 读取历史 Harness、代码、分数和轨迹；
2. 提出若干新的 Harness 候选；
3. 通过接口校验后执行评估；
4. 将代码、状态、轨迹和得分写回文件系统；
5. 重复循环，最终保留 Pareto frontier 上的候选。

提案 Agent 不必在运行中无感地直接覆写自己的优化器。文章展示的是显式的“提案—校验—评估—留档—选择”外循环；自动程度取决于实际系统是否设置人工审核和晋级门。所谓“用于优化 Harness 的 Harness”，指的是这套外层优化程序。

“一旦 Harness 设计成为可执行的搜索空间”意味着：原先人类工程师会调整的 Prompt、检索、工具定义、控制流、记忆和错误恢复，被表示为可运行、可修改、可评分的代码候选。强编码 Agent 因而可以在同一设计空间里提出修改；这不等于它拥有与人类相同的目标判断，也不保证评估器覆盖长期价值。

## 工作流设计与自动科研

### 人工设计的科研工作流

AI Scientist 将提出想法、写代码、运行实验、分析结果、写论文和同行评审串成工作流。ScientistOne 把可验证性放在中心，要求引用、数值、方法与结论能够追溯到证据，并用 Chain-of-Evidence 检查。这里的 `auto-research` 是“自动化研究”，不是“汽车研究”；论文写作只是自动科研闭环的一种实验载体。

文章同时强调：写出形式完整的论文不等于完成科学发现。系统仍可能捏造引用、在实现中偏离原始方法，或者用薄弱实验支撑过强结论。可追溯只是基础，工程上还要把每个 claim 与具体证据、实现、运行结果和审计结论绑定，并保留不能验证的部分。

### Autodata：寻找“强模型会、弱模型不会”的题

Autodata 用 Challenger 生成问题，由 weak solver、strong solver 和 verifier/judge 共同反馈，目标是合成“难度恰到好处”的训练与评估数据：强求解器成功、弱求解器失败。Challenger 的 Prompt 随反馈迭代；但文章指出，如果合成数据只用于微调弱求解器而没有继续改进强模型，这更像在生成的 Prompt 分布上做间接蒸馏，RSI 成分较弱。

### ADAS：让 Meta-Agent 编写 Agent 工作流

Automated Design of Agentic Systems（ADAS）的流程是：先用 CoT、self-refine 等简单 Agent 初始化工作流档案；Meta-Agent 参考档案先描述一个新工作流，再把它写成代码并进行两轮自我批评与改写；候选经过任务评估，成功者回到档案，成为下一轮设计素材。

例如，初始档案只有“直接 CoT”和“回答后自我反思”两种 Agent。Meta-Agent 可能生成一个新程序：先让两个角色分别求解，再由第三个角色比较分歧，最后调用 verifier 决定是否重试。这个程序在验证集上优于已有工作流后才进入档案；后续候选可以继续组合或修改它。这里真正被搜索的是可执行的 Agent 组织方式，而不只是一个 Prompt。

在这类实现中，`forward` 可以近似理解为“一个候选 Agent/Harness 对单次输入执行完整工作流的入口”：它定义信息如何流经模型调用、角色、工具与验证器。严格说，`forward` 是 Harness 的任务执行函数，不等于包含配置、状态、权限和发布机制在内的全部 Harness；名称借用了前向计算的直觉，但不是神经网络权重训练中的反向传播配对概念。

### AFlow：用 MCTS 搜索工作流图

AFlow 把工作流表示为图：节点是调用 LLM 的动作，边是代码实现的逻辑关系。它使用蒙特卡洛树搜索（MCTS）在候选工作流树中反复执行四类动作：在高分利用与未知分支探索之间选择父节点；让 LLM 修改并展开新工作流；实际执行和评分；把结果回传到树上，直到预算耗尽或得分趋于稳定。

MCTS 在这里不是训练模型参数，而是在有限评估预算下决定“下一次值得试哪个工作流变体”。它解决的是不能穷举巨大设计空间时，如何平衡继续深挖已知好方案与尝试新路线。

## 自我改进 Harness：STOP、Self-Harness 与 AHE

### STOP：改进“改进器”

Self-Taught Optimizer（STOP）的目标不是只改进某个解，而是让一个 improver 反复改写自身，使后续更会改进其他程序。文章列举的结果说明，它可能重新发现 beam search、遗传算法、模拟退火等经典策略。这正体现了“经典搜索框架 + LLM 语义提案”的组合：创新点不一定是发明新的优化数学，而是让模型能够编写、组合和修改优化程序。

STOP 也给出能力门槛：递归结构本身不会自动产生提升；较弱模型可能越改越差。Harness 可以更好地部署模型能力，但不能替代核心智能。

### Skill 更新能力不等于 Harness 使用能力

文章引用的实验观察到，从 Qwen3.5-9B 到 Claude Opus 4.6，不同规模模型都能表现出相近的 Harness/Skill 更新能力，9B 提案器也可能写出与 Opus 程序结构等价的 Skill。这说明“根据轨迹写出规则或程序”可能比想象中更容易，并非只有最强模型才能承担。

但写出好 Skill 不等于运行时一定获得同等收益。模型还必须在正确时机识别并调用 Skill 与工具、持续遵循长周期指令，并把 Skill 应用于真实状态。应区分：

- **更新能力**：能否提出或改写一份看起来正确的 Skill；
- **路由能力**：能否知道何时使用哪项 Skill 或工具；
- **执行能力**：能否在长任务中持续遵守规则、处理反馈并完成动作；
- **受益能力**：接入 Harness 后，端到端任务表现是否真实提高。

### Self-Harness：有边界的 propose–evaluate–accept

Self-Harness 更接近当前可工程落地的 Agent self-improvement：

1. **Weakness mining**：从评估轨迹中聚类由 verifier 支持的失败模式，区分表面结果相同但根因不同的失败；
2. **Harness proposal**：只在声明的可编辑面内，根据失败证据、需要保留的成功行为和历史尝试提出窄而不同的修改；
3. **Proposal validation**：同时在 held-in 数据上验证目标弱点是否修复，在 held-out 数据上检查未知回归；不满足非回归要求的候选只留档，不进入活动 Harness。

这不是系统任意重写自己，而是“证据驱动、编辑面受限、独立验证、合格后晋级”的闭环。操作系统、权限层和评估器若也能被同一个循环修改，就会破坏抽象边界并引入奖励黑客。

### AHE：让修改可归因、可证伪

Agentic Harness Engineering（AHE）把主要瓶颈归结为 observability，并分成三层：

- **组件可观察**：Prompt、工具描述、工具实现、中间件、Skill、子 Agent 配置和长期记忆等可编辑组件在文件系统中有明确表示；
- **经验可观察**：原始轨迹先形成逐任务分析，再聚合成 Benchmark 级失败模式，需要时仍能下钻到原始证据；
- **决策可观察**：每次编辑都附带失败证据、根因、目标修复和可能回归，成为下一轮可验证的预测。

Runs、tracer、verifier 和模型配置保持只读，候选只能修改 Harness workspace，从而尽量把收益归因于 Harness 编辑。把 Self-Harness 与 AHE 合在一起，可形成 [[practices/evidence-driven-bounded-harness-evolution|证据驱动的受约束 Harness 演化闭环]]：

```text
Evidence
  → Bounded Edit
  → Independent Validation
  → Controlled Promotion
  → Reversible Deployment
```

## 进化搜索：在可执行候选中选择

进化搜索维护一组候选，反复选择父代、变异、评估，并保留适应度较高的后代。它适合 Harness 的原因是：设计空间巨大且形状不规则，难以用梯度直接优化，但许多候选可以运行后评分。

在传统进化算法中，变异可能是随机改几个数；在 Harness 搜索中，LLM 可以读取父代代码、任务说明、轨迹和分数，再提出具有语义的代码 diff。Promptbreeder、GEPA、AlphaEvolve、ShinkaEvolve 等分别探索 Prompt、反思、程序池、采样与新颖性；Darwin Gödel Machine（DGM）则让固定基础模型驱动编码 Agent 修改自己的 Harness 代码库，再把表现足够好的新 Agent 加回候选池。

DGM 因而是“固定 Model，进化 Harness”，不是模型权重自我更新。进化搜索最适合 GPU kernel、算法竞赛、调度等“候选难设计但结果容易自动评分”的任务；面对研究价值、创新性、可维护性和可信度等慢、模糊或启发式目标时，适应度函数本身就成为瓶颈。

## 模型权重：可以更新，不等于可靠 RSI 已实现

文章把 Harness 与模型权重联合优化视为更深一层。技术上可以通过训练流水线改进或测试时持续学习更新权重；SIA 使用 Meta-Agent、Task-Specific Agent 和 Feedback-Agent，决定下一轮修改 Harness 还是模型参数；Continual Harness 则在长周期游戏环境中同时更新 Harness，并用强教师在低奖励轨迹上的标签蒸馏策略模型。

但文章明确把 SIA 的证据视为早期且暂定：不同角色模型能力不对称、基线较弱，训练稳定性与 Goodhart 效应仍未解决。因此应区分：

1. 用轨迹离线微调模型在工程上可行；
2. 由 Agent 触发或选择权重更新仍偏实验性；
3. Harness 与权重联合闭环已有早期尝试；
4. 开放式、长期稳定且能可靠验证的自主权重 RSI 尚未由文章证明。

近期实际竞争点仍更可能集中在可编辑、可验证和可回滚的 Harness 层。

## 自动科研实验揭示的六类失败

文章引用 Trehan 与 Chopra 的实验：系统使用很少的脚手架和基础文件/搜索工具，尝试从研究想法走到论文；三个领域各有 45–50 篇高质量种子文档，只有四个想法被人类专家选入完整流程，最终只有一个完整执行成论文。文章归纳出六类反复出现的失败：

1. **偏向训练数据默认值**：使用旧库、过期命令、常见格式或没有被当前仓库与数据支持的假设；
2. **实现漂移**：技术难度升高时，系统退回更常见、更简单但已经偏离原始提案的方法；
3. **记忆与上下文退化**：长项目丢失关键细节，除非把日志与状态写成持久工件；
4. **过度乐观**：在实验失败或信号仍是噪声时宣布成功；
5. **领域智能不足**：难以估计实现复杂度、判断结果是否合理或选择关键基线；
6. **科学品味薄弱**：实验可以运行，却没有回答真正重要的问题。

这组实验不是为了证明 Agent 已能自动做出科学发现，而是在测试最小脚手架下端到端科研的真实断点。它说明“生成论文文本”“完成实验流程”“获得可信科学结论”是三个不同的验收层级。

## 七项未来挑战与人的位置

文章最终提出七项长期挑战：弱且模糊的评估器、上下文与记忆生命周期、负结果、多样性坍缩、奖励黑客、长期成功，以及人类角色。本 Wiki 已将它们连同每项具体例子、验证方式、适用范围和失效条件整理为 [[practices/harness-self-improvement-challenge-checklist|Harness 自我改进七项挑战检查框架]]，本页不重复维护两份清单。

其中最关键的分界是“容易自动验证”与“重要但难量化”：单元测试、数学答案、GPU kernel 速度和游戏得分可以提供清晰的选择信号；研究价值、真正创新、长期可维护性、结论可信度以及哪个失败值得继续投入，则不能可靠压缩成一次 Benchmark 或一个 Judge Model 分数。

文章所说的人类 `move up the stack`，不是退出闭环，而是从逐步操作员转向：问题和目标定义者、模糊价值判断者、评估与边界设计者、领域导师、异常处理者，以及高影响修改的晋级与责任承担者。自动验证越清晰、风险越低，执行越可自动化；目标越模糊、影响越长期或权限越高，人类越需要在更高抽象层保留最终判断。

## 阶段性结论

这篇文章呈现出的近期路线不是“Agent 已能无限自我进化”，而是把原本由人手工完成的 Harness 设计逐步编译成可执行搜索空间，并形成有边界的优化闭环。对 Agent 工程更直接的启示是：

- Harness 的核心从“多接工具、多写 Prompt”转向状态、权限、副作用、恢复、验证与晋级协议；
- 自我改进的可信度主要来自证据、受限编辑面、独立评估、held-out 非回归和可回滚发布，而不是“模型修改了自己”这一叙事；
- 模型能力与 Harness 相互促进，但可内化的主要是部分推理和补偿性启发式；任务目标、现实状态、权限和完成证据仍需要外部系统承载；
- 越靠近真实世界、开放研究和长期软件维护，评价函数越不能替代人类价值判断与责任。

## 阅读边界

- 本文是综述，汇集了多篇研究；若要建立某一方法的独立论文页或作强结论，应先把对应原论文作为 Raw 摄取并核验。
- Benchmark 提升不能自动外推为开放式 RSI、生产可靠性或跨领域泛化。
- “可生成优秀候选”与“能正确路由、长期执行并真实受益”是不同能力。
- 只要评估器、权限或预算可以被候选 Harness 同时修改，分数提升就不能直接归因于 Harness 能力改善。

## 关系

- discusses → [[concepts/agent|Agent（智能体）]]
- discusses → [[concepts/harness|Harness（模型外层执行系统）]]
- discusses → [[concepts/agent-self-improvement|Agent Self-Improvement]]
- relates_to → [[concepts/recursive-self-improvement|递归自我改进（RSI）]]
- relates_to → [[concepts/loop-engineering|Loop Engineering]]
- discusses → [[practices/evidence-driven-bounded-harness-evolution|证据驱动的受约束 Harness 演化闭环]]
- discusses → [[practices/harness-self-improvement-challenge-checklist|Harness 自我改进七项挑战检查框架]]

## 引用

原文提供的引用格式：

> Weng, Lilian. "Harness Engineering for Self-Improvement". Lil'Log (Jul 2026). https://lilianweng.github.io/posts/2026-07-04-harness/

## 来源

- 本文 sourced_from：[[sources/lil-log|Lil'Log]]

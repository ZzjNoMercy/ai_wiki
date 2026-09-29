# Wiki Ingest Log
## [2026-07-30] ingest | 首次 Ingest：从三份 raw 编译出编译式 RAG 概念页、GBrain 框架页、预注册证伪实验论文页，建立 introduces / implements / challenges 关系链。

- job_id: wiki-a9569c26f52a49e0
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: f34e57252514288944414d441eb97bff1432e0e83aea8f25c6eaed82691af1d4
- model: claude-sonnet-4-20250514
- raw: manual-upload-785681c302/gbrain-implementation.md-1785069962628-3f55e791d8-7bfc6d0a9aa3.md, manual-upload-785681c302/karpathy-llm-wiki.md-1785069962627-3a2cdec832-f3ca050e6cc2.md, manual-upload-785681c302/preregistered-critique.md-1785069962628-ca95cdaeee-c0c80fd07f6b.md
- added_or_updated: [[wiki/concepts/compiled-rag]], [[frameworks/gbrain]], [[papers/preregistered-critique-compiled-rag]]
## [2026-07-30] migrate | remove duplicate wiki root prefix

- job_id: wiki-migrate-d3466dcbe6ce4c11
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: 9d98a025584d2f56bf66bc5f6b0efc38df8a3a2e45cc6089d3b15682ad8884c6
- moved: `wiki/concepts/compiled-rag` -> [[concepts/compiled-rag]]
- links_updated_in: [[concepts/compiled-rag]], [[frameworks/gbrain]], [[papers/preregistered-critique-compiled-rag]]
## [2026-07-31] ingest | Compile HarnessProfile tool_description_overrides concept page: documents the DeepAgents pattern of overriding built-in tool descriptions via public API rather than modifying source, with Tool Guide alignment principles and GBrain validation.

- job_id: wiki-270648116225449f
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: 9d98a025584d2f56bf66bc5f6b0efc38df8a3a2e45cc6089d3b15682ad8884c6
- model: deepseek-v4-pro
- raw: knowledge-file-4be80edb57/knowledge-curated-concepts-agent-engineering-harness-profile-tool-description-overrides.-6db80f2034-ebc1de42802f.md
- added_or_updated: [[concepts/harness-profile-tool-description-overrides]]
## [2026-07-31] ingest | Ingest raw：编译 5 个页面——engineering_practice（HarnessProfile 工具描述覆盖实践）、software_framework（DeepAgents、LangChain、LangGraph）、system（PuddingClaw），关系仅采用 Raw 明确支持的 applies_to/depends_on/uses/implements。

- job_id: wiki-284620ca2996483b
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: 2b98d5cc8d48dabfcb47007ce3bdf3b9463bac13743296cc2552fb9f56ef12ef
- model: deepseek-v4-flash
- raw: knowledge-file-4be80edb57/knowledge-imported-20260722-harness-profile-tool-description-overrides.md-9fe983cc93-cfbd16066b26.md
- added_or_updated: [[practices/harness-profile-tool-description-overrides]], [[frameworks/deepagents]], [[frameworks/langchain]], [[frameworks/langgraph]], [[systems/puddingclaw]]
## [2026-08-01] retire | 退役 concepts 页面并替换为 practices 页面（仅 Wiki）

- job_id: wiki-retire-8cfee90a418e45a5
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: 2b98d5cc8d48dabfcb47007ce3bdf3b9463bac13743296cc2552fb9f56ef12ef
- retired: [[concepts/harness-profile-tool-description-overrides]] -> [[practices/harness-profile-tool-description-overrides]]
- links_updated_in: none
## [2026-08-04] ingest | 将微信公众平台文章《分享2个Vibe Coding必备的超实用Prompt。》编译为 6 个原子页面：media 文章页、source 渠道页（数字生命卡兹克）、concept 概念页（Vibe Coding、第一性原理、对抗式审查）与 system 页面（AIHOT），关系均以 Raw 直接支持为准。

- job_id: wiki-ff6616134e4d4e0e
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: 3772bd1a3c715668cf3ccc1967ffb667f7d5fc1af4c725f5a1e13feb0e5cf5bf
- model: deepseek-v4-flash
- raw: read-later-4445b9b74d/later_809e9deef2b14e7d97a162b4-b9f587c39f-042741626d7c.md
- added_or_updated: [[media/vibe-coding-two-essential-prompts]], [[sources/digital-life-kazik]], [[concepts/vibe-coding]], [[concepts/first-principles]], [[concepts/adversarial-review]], [[systems/aihot]]
## [2026-08-04] ingest | 将《分享2个Vibe Coding必备的超实用Prompt。》编译为 media 文章页、source 来源页（数字生命卡兹克，含已收录内容反向链接）、system 页（AIHOT）、三个 concept 页（第一性原理、对抗式审查、Vibe Coding）及新增 engineering_practice 页（定期全局对抗式审查）；sourced_from/discusses/uses/relates_to/applies_to 关系均由 Raw 直接支持。

- job_id: wiki-e45b9bd67e92485e
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: f81f75b1a77ac3496ab92332eb3611492cf0fb483a9f57d520eb54a04bca2b47
- model: deepseek-v4-flash
- raw: read-later-4445b9b74d/later_809e9deef2b14e7d97a162b4-b9f587c39f-042741626d7c.md
- added_or_updated: [[media/vibe-coding-two-essential-prompts]], [[sources/digital-life-kazik]], [[systems/aihot]], [[concepts/first-principles]], [[concepts/adversarial-review]], [[concepts/vibe-coding]], [[practices/periodic-adversarial-review]]
## [2026-08-06] ingest | 将 OpenCode Agent Compact 工程实践 Raw 编译为两个原子页面：systems/opencode（OpenCode 会话压缩实现）与 practices/agent-context-compaction（跨平台 Compact 工程实践），双向 establishes implements/applies_to 关系，保留全部源码路径、常量与事件名。

- job_id: wiki-9dc9895247c847e9
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: f81f75b1a77ac3496ab92332eb3611492cf0fb483a9f57d520eb54a04bca2b47
- model: deepseek-v4-flash
- raw: manual-upload-785681c302/opencode-agent-compact-engineering-practice.md-1785921032328-20d84d7559-8130ff6bba3a.md
- added_or_updated: [[systems/opencode]], [[practices/agent-context-compaction]]
## [2026-08-12] ingest | 编译 Hermes Agent 浏览器自动化文档会话：新建 systems/hermes-agent（系统，含浏览器后端、browser_* 工具集、混合路由、与后端产品独立性）与 frameworks/browser-use（开源浏览器自动化框架，作为 Hermes 默认后端），以 uses 关系互链。

- job_id: wiki-a6005f149d194ff5
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: f81f75b1a77ac3496ab92332eb3611492cf0fb483a9f57d520eb54a04bca2b47
- model: deepseek-v4-flash
- raw: conversation-session-c0cc35d048c0-58aa7931a2/query-dcaa72953343-1a913f89cf-5218339fe6af.md
- added_or_updated: [[systems/hermes-agent]], [[frameworks/browser-use]]
## [2026-08-13] ingest | 编译会话 Raw：新增 Pi Agent（software_framework）及其四个学习资源（media）、菜鸟教程 source、Pi 学习资源评估 analysis；并补齐开源 Computer Use 调研（7 个桌面级 CUA system、浏览器专精与基础设施组件 framework 页 + 调研汇总 analysis）。所有事实与关系均直接来自本次 Raw。

- job_id: wiki-fa712fcbdc9e4a44
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: f81f75b1a77ac3496ab92332eb3611492cf0fb483a9f57d520eb54a04bca2b47
- model: deepseek-v4-flash
- raw: conversation-session-6afa50dc1ea8-6901fa8582/query-18f0f64be0c2-625953db84-28f865fb4246.md
- added_or_updated: [[frameworks/pi-agent]], [[media/pi-agent-runbook-tutorial]], [[media/pi-from-scratch]], [[media/pi-agent-book]], [[media/dg-zhuya-pi-reading-notes]], [[sources/runoob]], [[analysis/pi-agent-learning-resources]], [[systems/ui-tars-desktop]], [[systems/agent-s]], [[systems/open-interpreter]], [[systems/ufo]], [[systems/turix-cua]], [[systems/bytebot]], [[systems/pc-agent]], [[frameworks/stagehand]], [[systems/skyvern]], [[frameworks/midscene-js]], [[frameworks/cua-trycua]], [[frameworks/omniparser-v2]], [[frameworks/computer-rl]], [[analysis/computer-use-open-source-survey]]
## [2026-08-13] ingest | 纠正混合会话 Raw：Pi 页面仅保留 Pi 学习资源与 framework 定位

- job_id: wiki-7ea8a0fcc8a3455f
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: f81f75b1a77ac3496ab92332eb3611492cf0fb483a9f57d520eb54a04bca2b47
- model: deterministic-rollback
- raw: conversation-correction-session-6afa50dc1ea8-10d3fb8d76/query-18f0f64be0c2-pi-only-7374e46112-8cbd01cdcc8e.md
- added_or_updated: [[analysis/pi-agent-learning-resources]], [[frameworks/pi-agent]], [[media/dg-zhuya-pi-reading-notes]], [[media/pi-agent-book]], [[media/pi-agent-runbook-tutorial]], [[media/pi-from-scratch]], [[sources/runoob]]
## [2026-08-13] correction | 回退混合会话 Raw 的越界发布

- job_id: wiki-withdraw-d10722015ada4358
- corrected_publish: wiki-7ea8a0fcc8a3455f
- original_raw_retained: conversation-session-6afa50dc1ea8-6901fa8582/query-18f0f64be0c2-625953db84-28f865fb4246.md
- clean_pi_raw: conversation-correction-session-6afa50dc1ea8-10d3fb8d76/query-18f0f64be0c2-pi-only-7374e46112-8cbd01cdcc8e.md
- pi_pages_republished: [[analysis/pi-agent-learning-resources]], [[frameworks/pi-agent]], [[media/dg-zhuya-pi-reading-notes]], [[media/pi-agent-book]], [[media/pi-agent-runbook-tutorial]], [[media/pi-from-scratch]], [[sources/runoob]]
- withdrawn: [[analysis/computer-use-open-source-survey]], [[frameworks/computer-rl]], [[frameworks/cua-trycua]], [[frameworks/midscene-js]], [[frameworks/omniparser-v2]], [[frameworks/stagehand]], [[systems/agent-s]], [[systems/bytebot]], [[systems/open-interpreter]], [[systems/pc-agent]], [[systems/skyvern]], [[systems/turix-cua]], [[systems/ufo]], [[systems/ui-tars-desktop]]
- reason: source=conversation incorrectly authorized unrelated earlier Computer Use exchanges
## [2026-08-14] ingest | 基于 Pi 的 Compaction 实现介绍 raw，编译 5 个原子页面：Pi（编码 Agent）system、Compaction 概念、交接简报式摘要工程实践、原始博客文章 media、Earendil Engineering 官方博客 source 渠道，并建立 raw 直接支持的 implements/applies_to/derived_from/discusses/sourced_from 关系。

- job_id: wiki-57c195ca929f415d
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: f81f75b1a77ac3496ab92332eb3611492cf0fb483a9f57d520eb54a04bca2b47
- model: deepseek-v4-flash
- raw: conversation-synthesis-session-bc0c821a04d9-9bf943cf4e/query-cd31e5bdfb00-72cbb0465f-baa1ba11a8b2.md
- added_or_updated: [[systems/pi-coding-agent]], [[concepts/context-compaction]], [[practices/handoff-briefing-compaction-summary]], [[media/how-compaction-works-in-pi]], [[sources/earendil-engineering]]
## [2026-08-19] ingest | 将 ACP 概念与官方教程编译为 concepts/agent-client-protocol（协议定义、架构哲学、连接方式、MCP 集成、兼容智能体清单、客户端生态、Registry、官方语言库），并新建 concepts/model-context-protocol 记录 MCP 在 ACP 中的角色；仅以 Raw 证据关联既有 OpenCode、Hermes Agent、Pi、LangChain、LangGraph 页面。

- job_id: wiki-1c6fa435dd9042dc
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: f81f75b1a77ac3496ab92332eb3611492cf0fb483a9f57d520eb54a04bca2b47
- model: deepseek-v4-flash
- raw: conversation-synthesis-session-0943218c7a57-e3f54c6100/query-c43444565333-6a8c270165-ae8669e4b3cb.md
- added_or_updated: [[concepts/agent-client-protocol]], [[concepts/model-context-protocol]]
## [2026-08-19] ingest | 编译 Agent Client Protocol（ACP）官网地址、概念、动机、适用场景、架构哲学、连接方式、MCP 集成、兼容智能体、客户端生态与 ACP Registry，更新 concepts/agent-client-protocol 页面。

- job_id: wiki-363743d7f2bf483c
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: f81f75b1a77ac3496ab92332eb3611492cf0fb483a9f57d520eb54a04bca2b47
- model: deepseek-v4-flash
- raw: conversation-synthesis-session-0943218c7a57-e3f54c6100/query-42841b61c609-69e89ebf93-dd681b17ec20.md
- added_or_updated: [[concepts/agent-client-protocol]]
## [2026-08-20] ingest | 编译 ACP 会话：更新 concepts/agent-client-protocol 与 concepts/model-context-protocol，新建 companies/zed-industries、companies/jetbrains、sources/agent-client-protocol-official、media/agent-client-protocol-official-tutorial，覆盖 ACP 概念、架构、MCP 集成、智能体/客户端生态与 Registry。

- job_id: wiki-35dd46bcd3984c61
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: f81f75b1a77ac3496ab92332eb3611492cf0fb483a9f57d520eb54a04bca2b47
- model: deepseek-v4-flash
- raw: conversation-synthesis-session-0943218c7a57-e3f54c6100/query-188aaa0b1421-5c71d45755-602786d6ca0c.md
- added_or_updated: [[concepts/agent-client-protocol]], [[concepts/model-context-protocol]], [[companies/zed-industries]], [[companies/jetbrains]], [[sources/agent-client-protocol-official]], [[media/agent-client-protocol-official-tutorial]]
## [2026-08-23] ingest | 将 conversation-synthesis 会话中关于 Loop Engineering 概念的摘要编译为两个页面：concepts/loop-engineering（循环工程概念定义、执行闭环要点与 LongHorizon-Harness 文献定义）与 frameworks/long-horizon-harness（AMAP-ML GitHub 项目的定位与定义来源），并建立 implements/implemented_by 双向关系。

- job_id: wiki-39f0d103ce4d49ae
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: f81f75b1a77ac3496ab92332eb3611492cf0fb483a9f57d520eb54a04bca2b47
- model: deepseek-v4-flash
- raw: conversation-synthesis-session-76a1c4236a85-bb4891cbfe/query-152b4c99cc6b-0791a30d01-b37848b8a83a.md
- added_or_updated: [[concepts/loop-engineering]], [[frameworks/long-horizon-harness]]
## [2026-08-25] ingest | Compile Maka, Pi Harness v2, and EffectLedger durable agent runtime notes

- job_id: wiki-b0201712862a4cfa
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: f81f75b1a77ac3496ab92332eb3611492cf0fb483a9f57d520eb54a04bca2b47
- model: codex:gpt-5
- raw: conversation-synthesis-codex-local-source-review-e6e0a886b3/effectledger-pi-maka-durable-agent-interpreter-20260825-6fb80d55eb-c745a08e4e5a.md
- added_or_updated: [[systems/maka]], [[frameworks/pi-agent]], [[practices/durable-effect-ledger]]
## [2026-08-28] ingest | Ingest conversation-synthesis-fff-puddingteams-20260828-9bafcf50d8：编译 FFF 搜索库、@ff-labs/pi-fff、PuddingTeams 系统及四项搜索相关工程实践，共 7 个原子页面。

- job_id: wiki-8fbcf72579b54b0e
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: f81f75b1a77ac3496ab92332eb3611492cf0fb483a9f57d520eb54a04bca2b47
- model: deepseek-v4-flash
- raw: conversation-synthesis-fff-puddingteams-20260828-9bafcf50d8/fff-agent-code-search-practice-b33c55560c-3388215c0fa6.md
- added_or_updated: [[frameworks/fff]], [[frameworks/pi-fff]], [[systems/puddingteams]], [[practices/agent-search-minimal-context]], [[practices/search-session-state-isolation]], [[practices/workspace-search-security-boundary]], [[practices/controlled-search-verification]]
## [2026-08-29] ingest | 将 Lilian Weng《Harness Engineering for Self-Improvement》编译为 22 个原子页面：文章 media 页与 Lil'Log source 页（双向 sourced_from 链接）；RSI、Harness、Harness 设计模式及 ACE/MCE/Meta-Harness/STOP/Self-Harness/AHE/ADAS/AFlow/AlphaEvolve/DGM/进化式程序搜索/SIA/Continual Harness/ScientistOne/Autodata/自主科研失败模式等概念页；AI Scientist 系统页。所有事实均出自本次 raw。

- job_id: wiki-00d15abf070e46c3
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: f81f75b1a77ac3496ab92332eb3611492cf0fb483a9f57d520eb54a04bca2b47
- model: deepseek-v4-flash
- raw: read-later-4445b9b74d/later_abadf6c614f140da9b1d1fb5-0fce80db03-bcc916448a53.md
- added_or_updated: [[media/harness-engineering-for-self-improvement]], [[sources/lil-log]], [[concepts/recursive-self-improvement]], [[concepts/harness]], [[concepts/harness-design-patterns]], [[concepts/agentic-context-engineering]], [[concepts/meta-context-engineering]], [[concepts/meta-harness]], [[concepts/self-taught-optimizer]], [[concepts/self-harness]], [[concepts/agentic-harness-engineering]], [[systems/ai-scientist]], [[concepts/scientist-one]], [[concepts/autodata]], [[concepts/automated-design-of-agentic-systems]], [[concepts/aflow]], [[concepts/alpha-evolve]], [[concepts/darwin-godel-machine]], [[concepts/evolutionary-program-search]], [[concepts/sia]], [[concepts/continual-harness]], [[concepts/autonomous-research-failure-modes]]
## [2026-08-29] ingest | 编译 Life Odyssey 文章《The deepseek of DeepSeek Harness》，建立 DeepSeek Harness、Cordis、时空可组合性论文、Pydantic AI、Claude Agent SDK、Eve、Prime Agent 及 agent 组成/agent loop/开发流派/自我改进五级/离线在线学习等概念页面，并建立 Life Odyssey 渠道与文章媒体页。

- job_id: wiki-d329a1b33ba745ed
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: f81f75b1a77ac3496ab92332eb3611492cf0fb483a9f57d520eb54a04bca2b47
- model: deepseek-v4-flash
- raw: read-later-4445b9b74d/later_84091bcce34b4bab91a7ad57-f00ab3bff8-26d6d03086bf.md
- added_or_updated: [[media/deepseek-of-deepseek-harness]], [[sources/life-odyssey]], [[frameworks/deepseek-harness]], [[frameworks/cordis]], [[papers/spatiotemporal-composability]], [[frameworks/pydantic-ai]], [[frameworks/claude-agent-sdk]], [[products/eve]], [[systems/prime-agent]], [[concepts/agent-composition]], [[concepts/agent-loop]], [[concepts/agent-development-schools]], [[concepts/self-improvement-levels]], [[concepts/offline-online-nearline-learning]]
## [2026-08-30] retire | 纠正 2026-08-29 两篇 Harness 文章的过度原子化：仅保留用户确认的八个核心页面

- job_id: wiki-retire-3dfdd7e00ad84c63
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: f81f75b1a77ac3496ab92332eb3611492cf0fb483a9f57d520eb54a04bca2b47
- retired: `concepts/aflow` -> [[media/harness-engineering-for-self-improvement]], `concepts/agent-composition` -> [[media/deepseek-of-deepseek-harness]], `concepts/agent-development-schools` -> [[media/deepseek-of-deepseek-harness]], `concepts/agent-loop` -> [[media/deepseek-of-deepseek-harness]], `concepts/agentic-context-engineering` -> [[media/harness-engineering-for-self-improvement]], `concepts/agentic-harness-engineering` -> [[media/harness-engineering-for-self-improvement]], `concepts/alpha-evolve` -> [[media/harness-engineering-for-self-improvement]], `concepts/autodata` -> [[media/harness-engineering-for-self-improvement]], `concepts/automated-design-of-agentic-systems` -> [[media/harness-engineering-for-self-improvement]], `concepts/autonomous-research-failure-modes` -> [[media/harness-engineering-for-self-improvement]], `concepts/continual-harness` -> [[media/harness-engineering-for-self-improvement]], `concepts/darwin-godel-machine` -> [[media/harness-engineering-for-self-improvement]], `concepts/evolutionary-program-search` -> [[media/harness-engineering-for-self-improvement]], `concepts/harness` -> [[media/harness-engineering-for-self-improvement]], `concepts/harness-design-patterns` -> [[media/harness-engineering-for-self-improvement]], `concepts/meta-context-engineering` -> [[media/harness-engineering-for-self-improvement]], `concepts/meta-harness` -> [[media/harness-engineering-for-self-improvement]], `concepts/offline-online-nearline-learning` -> [[media/deepseek-of-deepseek-harness]], `concepts/scientist-one` -> [[media/harness-engineering-for-self-improvement]], `concepts/self-harness` -> [[media/harness-engineering-for-self-improvement]], `concepts/self-improvement-levels` -> [[media/deepseek-of-deepseek-harness]], `concepts/self-taught-optimizer` -> [[media/harness-engineering-for-self-improvement]], `concepts/sia` -> [[media/harness-engineering-for-self-improvement]], `frameworks/claude-agent-sdk` -> [[media/deepseek-of-deepseek-harness]], `frameworks/pydantic-ai` -> [[media/deepseek-of-deepseek-harness]], `products/eve` -> [[media/deepseek-of-deepseek-harness]], `systems/ai-scientist` -> [[media/harness-engineering-for-self-improvement]], `systems/prime-agent` -> [[media/deepseek-of-deepseek-harness]]
- links_updated_in: [[concepts/recursive-self-improvement]], [[frameworks/deepseek-harness]], [[media/deepseek-of-deepseek-harness]], [[media/harness-engineering-for-self-improvement]]
## [2026-08-30] ingest | 收录 Multi-Agent Token 成本优化文章、概念、实践、Graphify 与 rtk，并关联现有明确提及

- job_id: wiki-062eaf68f045477d
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: f81f75b1a77ac3496ab92332eb3611492cf0fb483a9f57d520eb54a04bca2b47
- model: gpt-5.6-sol
- raw: knowledge-file-4be80edb57/knowledge-imported-20260825-10-multi-agent-50-.md-c353f4a9b1-941251b63b09.md, conversation-correction-codex-multi-agent-token-20260830-1c071d3fe3/multi-agent-token-wiki-scope-v2-c875e42ad4-adddccc75aa9.md
- added_or_updated: [[media/multi-agent-token-cost-optimization]], [[sources/tencent-programmer]], [[concepts/multi-agent]], [[practices/multi-agent-token-cost-optimization]], [[frameworks/graphify]], [[frameworks/rtk]], [[media/harness-engineering-for-self-improvement]], [[practices/periodic-adversarial-review]], [[media/vibe-coding-two-essential-prompts]], [[concepts/adversarial-review]], [[frameworks/deepagents]]
## [2026-08-30] ingest | 完成 Multi-Agent 编译来源收口并将反向提及关系规范为 derived_from

- job_id: wiki-06f5935d76b04dd6
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: f81f75b1a77ac3496ab92332eb3611492cf0fb483a9f57d520eb54a04bca2b47
- model: gpt-5.6-sol
- raw: conversation-correction-codex-multi-agent-token-20260830-1c071d3fe3/multi-agent-token-wiki-scope-cecfa617a3-ed1742b2e57f.md
- added_or_updated: [[sources/tencent-programmer]], [[frameworks/graphify]], [[frameworks/rtk]]
## [2026-08-30] ingest | 按用户确认恢复 Harness 长期概念页，记录稳定定义、核心职责和从功能堆叠转向状态、权限、副作用、恢复与验证稳定协议的阶段性趋势，并关联现有 Harness 相关 Wiki 页面。

- job_id: wiki-78ce1eee45a04f72
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: f81f75b1a77ac3496ab92332eb3611492cf0fb483a9f57d520eb54a04bca2b47
- model: codex:gpt-5
- raw: read-later-4445b9b74d/later_abadf6c614f140da9b1d1fb5-0fce80db03-bcc916448a53.md, read-later-4445b9b74d/later_84091bcce34b4bab91a7ad57-f00ab3bff8-26d6d03086bf.md, conversation-synthesis-session-76a1c4236a85-bb4891cbfe/query-152b4c99cc6b-0791a30d01-b37848b8a83a.md, conversation-synthesis-codex-local-source-review-e6e0a886b3/effectledger-pi-maka-durable-agent-interpreter-20260825-6fb80d55eb-c745a08e4e5a.md, manual-upload-785681c302/opencode-agent-compact-engineering-practice.md-1785921032328-20d84d7559-8130ff6bba3a.md, knowledge-file-4be80edb57/knowledge-imported-20260722-harness-profile-tool-description-overrides.md-9fe983cc93-cfbd16066b26.md, conversation-synthesis-fff-puddingteams-20260828-9bafcf50d8/fff-agent-code-search-practice-b33c55560c-3388215c0fa6.md
- added_or_updated: [[concepts/harness]]
## [2026-08-30] ingest | 补齐 Harness 概念页与现有定义来源、长任务闭环、运行时机制、具体框架和系统之间的双向导航关系，消除新页面孤立状态。

- job_id: wiki-6722f10579dd43e9
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: f81f75b1a77ac3496ab92332eb3611492cf0fb483a9f57d520eb54a04bca2b47
- model: codex:gpt-5
- raw: conversation-correction-codex-multi-agent-token-20260830-1c071d3fe3/multi-agent-token-wiki-scope-v2-c875e42ad4-adddccc75aa9.md, conversation-correction-session-6afa50dc1ea8-10d3fb8d76/query-18f0f64be0c2-pi-only-7374e46112-8cbd01cdcc8e.md, conversation-synthesis-codex-local-source-review-e6e0a886b3/effectledger-pi-maka-durable-agent-interpreter-20260825-6fb80d55eb-c745a08e4e5a.md, conversation-synthesis-fff-puddingteams-20260828-9bafcf50d8/fff-agent-code-search-practice-b33c55560c-3388215c0fa6.md, conversation-synthesis-session-76a1c4236a85-bb4891cbfe/query-152b4c99cc6b-0791a30d01-b37848b8a83a.md, knowledge-file-4be80edb57/knowledge-imported-20260722-harness-profile-tool-description-overrides.md-9fe983cc93-cfbd16066b26.md, manual-upload-785681c302/opencode-agent-compact-engineering-practice.md-1785921032328-20d84d7559-8130ff6bba3a.md, read-later-4445b9b74d/later_84091bcce34b4bab91a7ad57-f00ab3bff8-26d6d03086bf.md, read-later-4445b9b74d/later_abadf6c614f140da9b1d1fb5-0fce80db03-bcc916448a53.md
- added_or_updated: [[media/harness-engineering-for-self-improvement]], [[media/deepseek-of-deepseek-harness]], [[concepts/recursive-self-improvement]], [[concepts/loop-engineering]], [[frameworks/deepagents]], [[frameworks/deepseek-harness]], [[frameworks/long-horizon-harness]], [[frameworks/pi-agent]], [[practices/agent-context-compaction]], [[practices/durable-effect-ledger]], [[practices/harness-profile-tool-description-overrides]], [[systems/maka]], [[systems/opencode]], [[systems/puddingclaw]], [[systems/puddingteams]]
## [2026-08-31] ingest | 收录 Warp 基于 Skill 的 Agent 自我改进闭环，并建立 Agent Self-Improvement 父概念及相关页关联

- job_id: wiki-2f1cf47b343c4d8f
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: f81f75b1a77ac3496ab92332eb3611492cf0fb483a9f57d520eb54a04bca2b47
- model: gpt-5.6-sol
- raw: knowledge-file-4be80edb57/knowledge-imported-20260830-how-warp-builds-self-improving-agents-on-claude---claude-by--d0eb9dd10a-d24d9bd248b7.md, read-later-4445b9b74d/later_abadf6c614f140da9b1d1fb5-0fce80db03-bcc916448a53.md, read-later-4445b9b74d/later_84091bcce34b4bab91a7ad57-f00ab3bff8-26d6d03086bf.md
- added_or_updated: [[media/how-warp-builds-self-improving-agents-on-claude]], [[sources/claude-blog]], [[concepts/agent-self-improvement]], [[practices/skill-based-agent-self-improvement-loop]], [[concepts/recursive-self-improvement]], [[media/harness-engineering-for-self-improvement]], [[media/deepseek-of-deepseek-harness]], [[frameworks/deepseek-harness]]
## [2026-09-02] ingest | 编译 Uber Software Factory：四层执行形态、产品化阶梯、结果经济性与 Benchmark 路由

- job_id: wiki-62f7d06fafd64de1
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: 39bb17e0625aa3fd82c82d809a5a6426460e04a1f178d1d043f2c1611503ed36
- model: codex-gpt-5.6
- raw: read-later-4445b9b74d/later_81b795f2b793454db6ae5691-a15e695237-16d194673310.md
- added_or_updated: [[media/running-a-software-factory-efficiently-at-uber-scale]], [[sources/uber-blog]], [[systems/uber-software-factory]], [[practices/agent-workload-productization-ladder]], [[practices/outcome-based-agent-economics]], [[practices/benchmark-driven-agent-model-routing]], [[concepts/harness]], [[practices/multi-agent-token-cost-optimization]]
## [2026-09-10] ingest | 建立 Agent 基础概念页，以能力组成与系统架构两种公式统一定义 Agent，并关联 Harness、Loop Engineering、Multi-Agent、Agent Self-Improvement 及两篇来源文章。

- job_id: wiki-4645246b6e3c4661
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: f23af1076aa1a7f0eaebf528e48d277aea0cf8fa19f87a89b5d05b2b7dad4d71
- model: codex:gpt-5
- raw: read-later-4445b9b74d/later_abadf6c614f140da9b1d1fb5-0fce80db03-bcc916448a53.md, read-later-4445b9b74d/later_84091bcce34b4bab91a7ad57-f00ab3bff8-26d6d03086bf.md
- added_or_updated: [[concepts/agent]], [[concepts/harness]], [[media/harness-engineering-for-self-improvement]], [[media/deepseek-of-deepseek-harness]], [[concepts/agent-self-improvement]]
## [2026-09-12] ingest | 补充 Agent 两种组成公式的正文内 Media 来源链接，使公式可在出现位置直接追溯原文。

- job_id: wiki-5831931f01ed4135
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: f23af1076aa1a7f0eaebf528e48d277aea0cf8fa19f87a89b5d05b2b7dad4d71
- model: codex:gpt-5
- raw: read-later-4445b9b74d/later_abadf6c614f140da9b1d1fb5-0fce80db03-bcc916448a53.md, read-later-4445b9b74d/later_84091bcce34b4bab91a7ad57-f00ab3bff8-26d6d03086bf.md
- added_or_updated: [[concepts/agent]]
## [2026-09-13] ingest | 收录 Stanford CS 329Z Agent 工程课程主页，并在 Agent 概念正文中增加相关课程入口。

- job_id: wiki-58a1f2fc8a544a4f
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: f23af1076aa1a7f0eaebf528e48d277aea0cf8fa19f87a89b5d05b2b7dad4d71
- model: codex:gpt-5
- raw: web-capture-cs329z-stanford-20c64e618d/cs329z-engineering-ai-agents-course-homepage-20260914-ddc1edf11e-50d0e0774ed8.md
- added_or_updated: [[media/cs329z-engineering-ai-agents]], [[concepts/agent]]
## [2026-09-13] ingest | 补充 Harness 的近期趋势、能力内化与外部职责边界：记录元方法论演进、Harness 与模型的双向促进，并解释 manual prompt tricks 及外部任务契约为何仍需保留。

- job_id: wiki-25e2fcd0be884aab
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: f23af1076aa1a7f0eaebf528e48d277aea0cf8fa19f87a89b5d05b2b7dad4d71
- model: codex:gpt-5
- raw: read-later-4445b9b74d/later_abadf6c614f140da9b1d1fb5-0fce80db03-bcc916448a53.md
- added_or_updated: [[concepts/harness]]
## [2026-09-13] ingest | 重组 Harness 演进趋势章节：在统一标题下先区分 Lilian Weng 的方向预测，再呈现 Wiki 的 2026-08 工程阶段判断。

- job_id: wiki-cda73fc493534485
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: f23af1076aa1a7f0eaebf528e48d277aea0cf8fa19f87a89b5d05b2b7dad4d71
- model: codex:gpt-5
- raw: read-later-4445b9b74d/later_abadf6c614f140da9b1d1fb5-0fce80db03-bcc916448a53.md, read-later-4445b9b74d/later_84091bcce34b4bab91a7ad57-f00ab3bff8-26d6d03086bf.md, conversation-synthesis-session-76a1c4236a85-bb4891cbfe/query-152b4c99cc6b-0791a30d01-b37848b8a83a.md, conversation-synthesis-codex-local-source-review-e6e0a886b3/effectledger-pi-maka-durable-agent-interpreter-20260825-6fb80d55eb-c745a08e4e5a.md, manual-upload-785681c302/opencode-agent-compact-engineering-practice.md-1785921032328-20d84d7559-8130ff6bba3a.md, knowledge-file-4be80edb57/knowledge-imported-20260722-harness-profile-tool-description-overrides.md-9fe983cc93-cfbd16066b26.md, conversation-synthesis-fff-puddingteams-20260828-9bafcf50d8/fff-agent-code-search-practice-b33c55560c-3388215c0fa6.md, read-later-4445b9b74d/later_81b795f2b793454db6ae5691-a15e695237-16d194673310.md
- added_or_updated: [[concepts/harness]]
## [2026-09-13] ingest | 修复 Harness 核心职责的 Markdown 表格分栏问题：改为响应式列表，保留原有职责说明和带显示名的 Wiki 关系。

- job_id: wiki-e6f9e05308d141ca
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: f23af1076aa1a7f0eaebf528e48d277aea0cf8fa19f87a89b5d05b2b7dad4d71
- model: codex:gpt-5
- raw: read-later-4445b9b74d/later_abadf6c614f140da9b1d1fb5-0fce80db03-bcc916448a53.md, read-later-4445b9b74d/later_84091bcce34b4bab91a7ad57-f00ab3bff8-26d6d03086bf.md, conversation-synthesis-session-76a1c4236a85-bb4891cbfe/query-152b4c99cc6b-0791a30d01-b37848b8a83a.md, conversation-synthesis-codex-local-source-review-e6e0a886b3/effectledger-pi-maka-durable-agent-interpreter-20260825-6fb80d55eb-c745a08e4e5a.md, manual-upload-785681c302/opencode-agent-compact-engineering-practice.md-1785921032328-20d84d7559-8130ff6bba3a.md, knowledge-file-4be80edb57/knowledge-imported-20260722-harness-profile-tool-description-overrides.md-9fe983cc93-cfbd16066b26.md, conversation-synthesis-fff-puddingteams-20260828-9bafcf50d8/fff-agent-code-search-practice-b33c55560c-3388215c0fa6.md, read-later-4445b9b74d/later_81b795f2b793454db6ae5691-a15e695237-16d194673310.md
- added_or_updated: [[concepts/harness]]
## [2026-09-22] ingest | 补充 Agent Self-Improvement 的阶段性算法判断：经典优化外循环借助 LLM 的语义提案能力，把搜索对象扩展到自然语言、代码与 Agent 架构，并说明其效率与验证边界。

- job_id: wiki-d126cf0f6572441f
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: f23af1076aa1a7f0eaebf528e48d277aea0cf8fa19f87a89b5d05b2b7dad4d71
- model: codex:gpt-5
- raw: read-later-4445b9b74d/later_abadf6c614f140da9b1d1fb5-0fce80db03-bcc916448a53.md
- added_or_updated: [[concepts/agent-self-improvement]]
## [2026-09-27] ingest | 将 Lilian Weng 提出的七项未来挑战整理为 Harness 自我改进工程检查框架，并从 Harness 概念页和来源 Media 建立入口。

- job_id: wiki-a0456a6aa1724a70
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: f23af1076aa1a7f0eaebf528e48d277aea0cf8fa19f87a89b5d05b2b7dad4d71
- model: codex:gpt-5
- raw: read-later-4445b9b74d/later_abadf6c614f140da9b1d1fb5-0fce80db03-bcc916448a53.md
- added_or_updated: [[concepts/harness]], [[media/harness-engineering-for-self-improvement]], [[practices/harness-self-improvement-challenge-checklist]]
## [2026-09-27] ingest | 补充 SI 易自动验证与难量化目标的例子，并明确人类在未来 Harness 中上移后的职责。

- job_id: wiki-a4b7fd906ef94fec
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: f23af1076aa1a7f0eaebf528e48d277aea0cf8fa19f87a89b5d05b2b7dad4d71
- model: codex:gpt-5
- raw: read-later-4445b9b74d/later_abadf6c614f140da9b1d1fb5-0fce80db03-bcc916448a53.md
- added_or_updated: [[practices/harness-self-improvement-challenge-checklist]]
## [2026-09-27] ingest | 扩充 Harness Engineering for Self-Improvement 阅读页，新增证据驱动的受约束 Harness 演化工程实践，并从 Harness、Agent Self-Improvement 与七项挑战检查框架建立双向导航。

- job_id: wiki-f547c235a3cc493c
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: f23af1076aa1a7f0eaebf528e48d277aea0cf8fa19f87a89b5d05b2b7dad4d71
- model: codex:gpt-5
- raw: read-later-4445b9b74d/later_abadf6c614f140da9b1d1fb5-0fce80db03-bcc916448a53.md
- added_or_updated: [[media/harness-engineering-for-self-improvement]], [[practices/evidence-driven-bounded-harness-evolution]], [[concepts/harness]], [[concepts/agent-self-improvement]], [[practices/harness-self-improvement-challenge-checklist]]
## [2026-09-29] ingest | 收录 Harness-Zero 论文与跨 Harness 行为蒸馏工程实践

- job_id: wiki-fd8d76f12eec42b6
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: 39bb17e0625aa3fd82c82d809a5a6426460e04a1f178d1d043f2c1611503ed36
- model: gpt-6-sol
- raw: manual-upload-harness-zero-paper-a648d690f9/arxiv-2609-24974-v1-20ecc4840e-0cbb660ab65d.md
- added_or_updated: [[papers/harness-zero]], [[practices/cross-harness-behavior-distillation]]
## [2026-09-29] retire | 退役三篇不再需要的 PuddingTeams 搜索实践页；保留系统页作为入口

- job_id: wiki-retire-46eb74859ffc4ab5
- schema: puddingclaw-wiki@0.4.0
- bundle_hash: 39bb17e0625aa3fd82c82d809a5a6426460e04a1f178d1d043f2c1611503ed36
- retired: `practices/controlled-search-verification` -> [[systems/puddingteams]], `practices/search-session-state-isolation` -> [[systems/puddingteams]], `practices/workspace-search-security-boundary` -> [[systems/puddingteams]]
- links_updated_in: none

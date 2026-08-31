# LLM Wiki Agent 操作契约

> schema: puddingclaw-wiki@0.4.0

## 所有权与操作边界

- `raw/` 只读，严禁修改、重命名或删除其中的内容。
- Ingest 只能在 `.puddingclaw/staging/` 下写入补丁；由发布流程更新 `wiki/`。
- Query 和 Lint 均为只读操作。
- `wiki/log.md` 只允许追加，禁止改写已有记录。

## Schema 约束

- 允许的 page types：system, debate, research_paper, software_framework, ai_model, programming_language, engineering_practice, person, company, media, tweet, social-digest, analysis, atom, concept, source, deal, email, slack, writing, project, note, event, diary。
- 允许的 link types：supports, challenges, introduces, implements, uses, depends_on, evaluates, applies_to, partner_of, relates_to, mentions, discusses, founded, works_at, invested_in, sourced_from, derived_from, supersedes, redirects_to, attended, authored, attributed_to。
- 必填 frontmatter：title, type, sources, created, updated, schema_version。
- Wiki 页面按 Page Type 的 `path_prefixes` 分目录存放；文件名 slug 使用小写连字符格式。
- `system`：`systems/<slug>.md`。
- `debate`：`debates/<slug>.md`。
- `research_paper`：`papers/<slug>.md`。
- `software_framework`：`frameworks/<slug>.md`。
- `ai_model`：`models/<slug>.md`。
- `programming_language`：`languages/<slug>.md`。
- `engineering_practice`：`practices/<slug>.md`。
- `person`：`people/<slug>.md`、`person/<slug>.md`。
- `company`：`companies/<slug>.md`、`company/<slug>.md`、`products/<slug>.md`、`orgs/<slug>.md`。
- `media`：`media/<slug>.md`、`videos/<slug>.md`、`articles/<slug>.md`、`essays/<slug>.md`、`books/<slug>.md`、`podcasts/<slug>.md`、`blog/<slug>.md`、`posts/<slug>.md`。
- `tweet`：`tweets/<slug>.md`、`twitter/<slug>.md`。
- `social-digest`：`digests/social/<slug>.md`。
- `analysis`：`analysis/<slug>.md`。
- `atom`：`atoms/<slug>.md`。
- `concept`：`concepts/<slug>.md`、`concept/<slug>.md`。
- `source`：`sources/<slug>.md`、`source/<slug>.md`。
- `deal`：`deals/<slug>.md`、`deal/<slug>.md`。
- `email`：`emails/<slug>.md`、`email/<slug>.md`。
- `slack`：`slack/<slug>.md`。
- `writing`：`writing/<slug>.md`。
- `project`：`projects/<slug>.md`、`project/<slug>.md`。
- `note`：`notes/<slug>.md`、`note/<slug>.md`。
- `event`：`life/events/<slug>.md`。
- `diary`：`life/diary/<slug>.md`。
- 所有 wikilink 必须写出与 gbrain 页面 slug 完全一致的目录前缀：使用 `[[<type-directory>/<slug>]]` 或 `[[<type-directory>/<slug>|显示文本]]`；禁止裸写 `[[<slug>]]`，否则 gbrain 无法抽取关系边。

## AI Agent Wiki 分类准则

- `research_paper`：论文、预印本和技术报告；保留作者、年份、URL/DOI/arXiv 标识及核心结论。
- `software_framework`：LangGraph、AutoGen 等具体软件框架；抽象方法论仍使用 `concept`。
- `ai_model`：具体模型或模型系列；模型背后的技术方法使用 `concept`。
- `programming_language`：Python、TypeScript、Rust 等具体编程语言。
- `system`：具体 Agent 系统、产品或可运行实现；项目过程使用 `project`。
- `engineering_practice`：从开发记录中提炼的可复用工程经验，必须说明问题、根因、方案、验证、适用范围和失效条件。
- 普通文章、博客、视频等来源优先使用内置 `media` 或 `source`，不要重复增加 `article`。
- `media` 页面必须保留 Raw 明确给出的原始署名与发布平台；不得把发布渠道、账号名和自然人身份互换。
- 对微信公众号、Newsletter、博客、播客频道、视频频道等具有稳定名称且会重复收录内容的发布渠道，应建立或复用一个 `source` 页面；先从 Index 解析已有 slug，再使用 `sourced_from` 将每个 `media` 页面链接到该来源，禁止为同一平台账号重复建页。首次建页时只记录 Raw 直接支持的账号名、平台及已收录内容，不得补写外部事实。
- `source` 页的“已收录内容”必须使用完整路径 wikilink 指向对应 `media` 页面：`[[media/<slug>|文章标题]]`。禁止使用“详见 media 页面”等纯文本代替链接。对于同一收录关系，`media` 必须以 `sourced_from` 指向 `source`，`source` 也必须在“已收录内容”中反向链接 `media`，确保双向可导航；不得重复收录同一页面。
- 账号名或频道名本身不等于 `person`。只有 Raw 明确支持具体自然人身份时才建立 `person` 页面和 `authored` 关系；仅有署名时保留原始署名，不创建空洞人物页。
- Raw 自带的 `id`、`type`、`subtype`、`related` 等 frontmatter 是来源元数据和分类线索，不是目标 Wiki 的分类结论；必须根据正文主题和活动 Schema 重新判断 page type 与关系。
- 编写页面前先在内部完成“长期实体—稳定主题—关系”清单。对 Raw 明确出现且证据充分的具体框架、系统、模型、语言和工程实践分别建页；一份 raw 可以编译出多个 Wiki 页面，一个页面只描述一个长期稳定主题，不按 raw 文件机械地一对一建页。
- 具体框架与作用于该框架的工程实践应分别建为 `software_framework` 和 `engineering_practice`；若框架只有名称而缺少足以形成稳定页面的事实，则保留为正文文本并报告知识缺口，不得用模型常识补全。
- 本次选中的 Raw 正文是事实依据。`source_refs`、文件路径、URL 和其他引用字段只表示来源线索；除非对应内容也作为本次 Raw 被授权读取，否则不得递归读取或据此引入事实。
- 现有 `index.md` 只用于发现和解析已有页面 slug，不是事实证据；不得根据 Index 摘要推断归属、依赖、实现方或其他关系。
- 每项事实、实体归属和关系都必须由本次 Raw 直接支持。严格保留专有名词及主客体，不得把不同框架、系统、公司或项目互换；模型既有知识不能作为补充证据。
- 只在 Raw 明确支持关系时创建 wikilink。不得为了避免孤立页面或满足“互链”而添加关系；暂时没有可信关系的页面可以保持孤立。若关系两端均有充分证据且页面尚不存在，应在同一次 publish 中创建缺失页面。
- 优先使用 `introduces`、`implements`、`uses`、`depends_on`、`evaluates`、`applies_to`、`supports`、`challenges` 以及内置关系；关系两端只使用相对 `wiki/` 根目录的完整页面 slug，例如 `[[concepts/compiled-rag]]`，不得再次添加 `wiki/`。

## Ingest（摄取）

开始前读取 `AGENTS.md`、当前 Schema Bundle、本次选中的 raw 文件和 Wiki Index。先完成实体、主题、页面类型和有证据关系的规划，再生成页面；发布前逐项核对页面中的事实归属和关系是否可由本次 Raw 直接支持。生成 staging patch、更新索引覆盖情况，并追加一条 Ingest 日志。每个新增或更新的页面必须引用至少一个本次选中的 raw 文件；sources 只能填写 `raw/manifest.jsonl` 中不可变的精确 `snapshot_path`；直接复制 context 返回的路径，例如 `manual-upload-.../document.md`，不要添加 `raw/` 前缀。

## Query（查询）

先读取 `wiki/index.md`，再按需读取相关 Wiki 页面；Query 期间不得读取 raw。回答时引用 Wiki slug 和 sources；若 Wiki 信息不足，明确报告知识缺口。

## Lint（检查）

报告无效 frontmatter、未知类型、断链、孤立页面、索引遗漏、日志改写和过期 raw hash。Lint 不得修改任何文件。

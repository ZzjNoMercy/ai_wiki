---
id: kb:concept:harness-profile-tool-description-overrides
title: HarnessProfile 工具描述覆盖实践
type: concept
subtype: pattern
domain: agent-engineering
status: active
created: 2026-07-22
updated: 2026-07-22
aliases:
  - DeepAgents 工具描述覆盖
  - tool_description_overrides
  - Tool Schema 与 Tool Guide 双层配置
tags:
  - DeepAgents
  - HarnessProfile
  - Tool Schema
  - Tool Guide
source_refs:
  - /Users/pet/Code/AI/Agent/2026全年班_大模型Agent智能体开发实战/【专题课】Harness Engineering驾驭工程实战/Part 3. Harness Engineering 驾驭工程 · DeepAgents 框架实战/HarnessEngineering_第三节_deepAgents实战.ipynb
  - /Users/pet/Code/AI/Agent/PuddingClaw/backend/graph/deepagents_manager.py
  - /Users/pet/Code/AI/Agent/PuddingClaw/backend/prompts/deepagents/TOOL_GUIDES.md
related:
  - kb:analysis:gbrain-informed-puddingclaw-knowledge-architecture
---

# HarnessProfile 工具描述覆盖实践

## 当前结论

需要调整 DeepAgents 内置工具向模型暴露的说明时，优先使用公共 API
`HarnessProfile.tool_description_overrides`，不要修改依赖源码；行为原则同时写入
Tool Guide，确保模型看到的工具 Schema 与系统指导语义一致。

已知精确路径时，Agent 应直接使用对应文件工具。`ls` 只用于发现未知目录项，
`glob` 只用于精确路径或文件名未知时的模式发现，禁止仪式性探索。

## 问题背景

DeepAgents 内置 `ls` 工具的默认说明倾向于让模型在读取或编辑文件前先列目录。
当用户、系统上下文、工具结果或持久化产物已经提供精确路径时，这会造成模型重复
调用 `ls`、`glob`、读取 README 或清单文件，只为确认已经确定的路径和项目身份。

单独在 System Prompt 或 Tool Guide 中禁止这种行为并不稳，因为模型仍会在工具
Schema 中看到相反说明。正确做法是同时统一两层指令。

## HarnessProfile 的角色

`HarnessProfile` 是 DeepAgents 在 `create_deep_agent()` 组装 Agent 时读取的运行
适配配置。它不是 Middleware，也不是新的 LangGraph 节点。

它适合承载：

- `tool_description_overrides`：按工具名替换模型看到的工具描述；
- `base_system_prompt` / `system_prompt_suffix`：调整 Prompt；
- `excluded_tools`：控制工具可见性；
- `extra_middleware`：注入运行时 Middleware；
- 通用子代理等 Harness 差异化设置。

## 推荐实现

```python
from deepagents import HarnessProfile, register_harness_profile


register_harness_profile(
    "modelclientchatmodel",
    HarnessProfile(
        tool_description_overrides={
            "ls": (
                "Only list a directory when unknown children genuinely need "
                "discovery. If an exact path is already known, operate on it "
                "directly instead of calling ls first."
            ),
            "glob": (
                "Use pattern discovery only when the exact path or file name "
                "is unknown. Do not confirm or rediscover a known path."
            ),
        },
    ),
)
```

框架会在 Agent 组装阶段复制并替换受支持工具的 description，不修改 DeepAgents
源码，也不修改调用方持有的原工具对象。

## Tool Guide 配套原则

1. 用户、系统上下文、工具结果或产物引用给出的精确路径视为权威事实；
2. 已知路径时直接调用 `read_file`、`grep`、`inspect_file_version`、
   `patch_file` 或对应写入工具；
3. `ls` 仅用于从已知目录发现未知子项；
4. `glob` 仅用于文件路径或文件名未知、确实需要模式发现时；
5. 找到目标路径后停止发现，不重复确认父目录或项目身份。

```text
Tool Guide             定义跨工具的总体行为原则
       ↓ 保持一致
Tool Schema Override   修正具体工具的局部诱导
       ↓
模型决策               已知路径直接操作，未知路径才探索
```

## 边界

- 不修改虚拟环境或 `site-packages` 中的 DeepAgents 源码；
- 覆盖项按工具名称匹配，工具改名后旧键会静默失效，应通过测试确认；
- 覆盖内容保持短而明确，避免继续扩大 System Prompt；
- Run 状态、Project identity、Goal、Todo、权限和产物引用应通过结构化 Run
  Context 注入，不应塞进工具描述。

---

## Evidence / Timeline

- **2026-07-22**：在 PuddingClaw 的 `modelclientchatmodel` HarnessProfile 中覆盖
  `ls` 和 `glob` 描述，同时补充 `TOOL_GUIDES.md`，专项测试通过。
- **课程依据**：Harness Engineering 第三节 Notebook 的第 2.4 节讲解了
  Harness Profile 与 `tool_description_overrides`；Notebook 前半部分的
  `_HarnessProfile` 是旧版内部 API，当前应使用公共 API。

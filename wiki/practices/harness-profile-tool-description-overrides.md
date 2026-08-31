---
title: HarnessProfile 工具描述覆盖实践
type: engineering_practice
sources:
  - knowledge-file-4be80edb57/knowledge-imported-20260722-harness-profile-tool-description-overrides.md-9fe983cc93-cfbd16066b26.md
created: 2026-07-22
updated: 2026-08-31
schema_version: 0.4.0
---

# HarnessProfile 工具描述覆盖实践

需要调整 [[frameworks/deepagents|DeepAgents]] 内置工具向模型暴露的说明时，优先使用公共 API `HarnessProfile.tool_description_overrides` 覆盖工具描述，而不是修改依赖源码；行为原则同时写入 Tool Guide，确保模型看到的工具 Schema 与系统指导语义一致。本实践 applies_to [[frameworks/deepagents|DeepAgents]]。

## 问题

DeepAgents 内置 `ls` 工具的默认说明倾向于让模型在读取或编辑文件前先列目录。当用户、系统上下文、工具结果或持久化产物已经提供精确路径时，这会造成模型重复调用 `ls`、`glob`，或反复读取 README 与清单文件，只为确认已经确定的路径和项目身份。

## 根因

单独在 System Prompt 或 Tool Guide 中禁止这种探索行为并不稳定：模型仍会在工具 Schema 中看到相反说明（`ls` 的默认描述诱导先列目录）。指令冲突来自单层配置——行为原则只写入提示层，工具 Schema 层仍保留诱导。

## 方案：统一两层指令

正确做法是同时统一两层指令：

1. **Tool Schema 层**：用公共 API `HarnessProfile.tool_description_overrides` 按工具名替换模型看到的工具描述；
2. **Tool Guide 层**：把跨工具的总体行为原则写入 Tool Guide，保持两层语义一致。

已知精确路径时，Agent 应直接使用对应文件工具（如 `read_file`、`grep`、`inspect_file_version`、`patch_file`）；`ls` 只用于发现未知目录项，`glob` 只用于精确路径或文件名未知时的模式发现，禁止仪式性探索。

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

框架会在 Agent 组装阶段复制并替换受支持工具的 description，不修改 DeepAgents 源码，也不修改调用方持有的原工具对象。

## Tool Guide 配套原则

1. 用户、系统上下文、工具结果或产物引用给出的精确路径视为权威事实；
2. 已知路径时直接调用 `read_file`、`grep`、`inspect_file_version`、`patch_file` 或对应写入工具；
3. `ls` 仅用于从已知目录发现未知子项；
4. `glob` 仅用于文件路径或文件名未知、确实需要模式发现时；
5. 找到目标路径后停止发现，不重复确认父目录或项目身份。

## 验证

2026-07-22 在 [[systems/puddingclaw|PuddingClaw]] 的 `modelclientchatmodel` HarnessProfile 中覆盖 `ls` 和 `glob` 描述，同时补充 `TOOL_GUIDES.md`，专项测试通过。课程依据：Harness Engineering 第三节 Notebook 第 2.4 节讲解了 Harness Profile 与 `tool_description_overrides`；Notebook 前半部分的 `_HarnessProfile` 是旧版内部 API，当前应使用公共 API。

## 适用范围

适用于 DeepAgents Harness 生态内需要调整内置工具对模型暴露说明的场景，尤其是存在“已知精确路径”上下文、需要消除仪式性探索的 Agent 工程。本实践 applies_to [[frameworks/deepagents|DeepAgents]]，并由 [[systems/puddingclaw|PuddingClaw]] 实现。

本实践 relates_to [[concepts/harness|Harness]] 的工具暴露与策略配置职责。

## 失效条件与边界

- 覆盖项按工具名称匹配：工具改名后旧键会静默失效，应通过测试确认；
- 不修改虚拟环境或 `site-packages` 中的 DeepAgents 源码；
- 覆盖内容保持短而明确，避免继续扩大 System Prompt；
- Run 状态、Project identity、Goal、Todo、权限和产物引用应通过结构化 Run Context 注入，不应塞进工具描述。

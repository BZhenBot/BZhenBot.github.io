---
layout: single
title: "AI 狼人杀：LLM 多智能体博弈、记忆与治理实验平台"
permalink: /projects/llm-werewolf-agent-platform/
author_profile: true
---

{% include base_path %}

[返回 Projects](/projects/)

围绕字节跳动 AI 全栈挑战赛 Agent 智能体方向课题 **“AI 狼人杀 — 多智能体协作与博弈的 Agent Team 实战”**，我实现了一个以狼人杀为实验场景的 LLM 多智能体实验平台。这个项目关注的不是把一局游戏简单跑通，而是在不完全信息、角色对抗和语言交互并存的规则环境里，让 Agent 具备 **可运行、可追踪、可复盘、可评测、可治理** 的完整闭环。

- 课题来源：[字节跳动 AI 全栈挑战赛](https://bytedance.aiforce.cloud/app/app_4k10u9vy4dz50/ai-challenge)
- 方向标签：LLM Agent / Multi-Agent / Memory / Governance / Evaluation
- 相关材料：[项目报告 PDF](/files/llm-werewolf-agent-platform-report.pdf)

![狼人杀作为 Multi-Agent Arena 的场景价值图](/images/projects/werewolf-agent/arena-value.png)

项目要解决的问题
======

狼人杀天然具备多角色、隐藏身份、信息不对称、公共讨论和投票决策等机制，非常适合作为多智能体博弈实验场景。这个项目主要围绕六个问题展开：

- 如何用统一框架承载不同角色，而不是为每个角色分别硬编码 Agent。
- 如何保证每个 Agent 只能看到合法可见的信息，避免越权推理。
- 如何让 Agent 在长流程对局中维护本局记忆，并积累跨局经验。
- 如何治理 LLM 输出中的格式错误、非法动作和私有信息泄露。
- 如何把每一步发言、投票、行动和裁决都记录下来，支持回放与审计。
- 如何把单局演示提升为可统计、可比较的批量评测平台。

核心方法
======

**1. 统一 Agent Harness**

系统没有为狼人、预言家、女巫、守卫、猎人和平民分别写独立的执行主流程，而是统一走 `PlayerAgent + AgentHarness` 这条链路。角色差异被拆解到三类边界中：

- 角色策略：不同身份通过策略工厂生成各自的行动建议与约束。
- 信息视角：Observation 构造阶段只注入该角色合法可见的私有状态。
- 规则治理：所有 LLM 输出都先解析、校验、修复，再由游戏引擎裁决。

![统一 Agent Harness 总体架构图](/images/projects/werewolf-agent/harness-architecture.png)

**2. 八阶段决策流水线**

为了让每次 Agent 行动都可追踪，系统把一次行动拆成标准化八阶段：Observation Construction、Memory Update、Reasoning Pipeline、Role Policy Planning、LLM Call、Parse + Validate、Decision Trace、Memory Result Update。这样模型输出不会直接写入游戏状态，而是只能提交一个候选动作，必须经过规则层确认后才会生效。

![AgentHarness 八阶段决策流水线图](/images/projects/werewolf-agent/harness-pipeline.png)

**3. 行为治理而不是直接相信模型输出**

系统对空输出、JSON 解析失败、字段错误、非法目标、无效发言和私有信息泄露都设计了显式处理路径。治理链路遵循：

- 先解析模型输出为结构化动作。
- 再做本地合法性校验。
- 可以确定修复的问题走 deterministic repair。
- 无法修复时生成最安全的 fallback 动作。
- 把 `fallback`、`repair_reason`、原始输出和最终动作全部写入决策证据。

这让“模型出错”从隐性问题变成可统计、可审计的工程信号。

系统闭环与前端证据
======

项目不只是一套对局引擎，还把运行态、复盘态、记忆态和评测态统一成完整闭环：

- `Live`：展示当前阶段、玩家状态、发言、投票和 LLM 调用状态，证明系统能实时运行。
- `History`：按时间线回放事件，并支持 public / debug 视角切换，证明过程可追踪、可复盘。
- `Memory`：查看 Agent 记忆内容、更新时间和使用痕迹，证明长期积累不是口头设定。
- `Reports`：聚合多局结果，统计胜率、fallback 率、token 消耗和治理指标，证明平台可评测。

![前端四视图证据链图](/images/projects/werewolf-agent/frontend-evidence-chain.png)

关键能力
======

| 能力 | 说明 |
| --- | --- |
| 可运行 | 支持完整昼夜流程、角色行动、公共讨论、投票与结算 |
| 可追踪 | 每次行动都能回溯 Observation、LLM 输出、解析结果与最终裁决 |
| 可复盘 | 历史对局支持事件时间线与不同可见性视角检查 |
| 可评测 | 多局实验可生成胜率、行动数、fallback 率、token 等统计指标 |
| 可治理 | 非法动作、输出异常、信息泄露风险和记忆使用情况都有显式记录 |

界面展示
======

**Live 视图**

![Live 总览界面](/images/projects/werewolf-agent/live-overview.png)

**Reports 视图**

![Reports 总览界面](/images/projects/werewolf-agent/reports-overview.png)

项目价值
======

这个项目对我来说，不是一次单点功能实现，而是一套更接近研究型工程系统的实践：

- 让我把 LLM Agent 从单轮问答推进到多角色、不完全信息和长流程交互环境。
- 把记忆、治理、审计和评测这些平时容易被演示掩盖的问题，落成可运行的系统部件。
- 验证了统一 Harness 设计在多角色场景下的可扩展性，也为后续迁移到其他多智能体任务保留了抽象边界。

如果后续继续扩展，我会优先补强多模型对照实验、记忆有效性的 A/B 验证，以及更系统的治理指标分析。

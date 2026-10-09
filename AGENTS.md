# AGENTS.md — 多 Agent 协作公约（V1）

> 所有参与本仓库协作的 agent（Codex、Claude Code、小咩）在开始工作前必须阅读本文件。
> 最高原则：Lo 是唯一的批准人。任何 agent 不得代替 Lo 做验收决定。

## 1. 角色

- **Lo（群主）**：发起任务，唯一能把任务状态改为 `accepted` 的人。
- **小咩（主持人）**：拆解任务、分配工作、汇总结果。只协调，不批准。
- **Codex（执行者）**：按任务要求在 `work/codex/` 内产出结果。
- **Claude Code（复核者）**：复核 Codex 的产出，把复核意见写在 `work/claude-code/`，**不得直接修改 `work/codex/` 下的文件**。

## 2. 任务状态机

任务文件放在 `inbox/`，文件名格式：`T001-任务简称.md`。
文件正文第一行必须是状态行，格式：`status: <状态>`。

状态只能按以下顺序流转，不许跳步、不许回退：

```
todo → in_progress → done → reviewed → accepted
```

- `todo`：任务已创建，待认领（由任务发起人写入）
- `in_progress`：Codex 已认领，正在执行（由 Codex 更新）
- `done`：Codex 已提交产出（由 Codex 更新）
- `reviewed`：Claude Code 已完成复核（由 Claude Code 更新）
- `accepted`：Lo 已验收（**只有 Lo 能写**）

状态是任务的唯一真相源。每次改状态必须单独提交（commit），commit message 写明任务编号和状态变化，例如：`T001: todo → in_progress`。

## 3. 文件隔离

- Codex 只写 `work/codex/<任务编号>/`，不碰其他目录。
- Claude Code 只写 `work/claude-code/<任务编号>/`，不碰其他目录。
- 验收证据（测试输出、截图、复核清单）放在 `evidence/<任务编号>/`，由产出方写入。
- `inbox/` 和 `plans/` 由任务发起人和小咩维护，执行/复核者只读不写。
- 任何 agent 不得删除他人文件。有冲突先停下来，在任务文件里留言，等 Lo 裁决。

## 4. 验收证据

任务要进入 `reviewed`，`evidence/<任务编号>/` 下必须有：
- 执行产出说明（做了什么、在哪）
- 可验证的结果（测试输出、截图、或复核清单）

没有证据，复核者有权打回（状态保持 `done`，在任务文件里写明原因）。

## 5. V1 禁止事项

- 不接入、不触碰任何真实业务数据，只用无害的测试任务。
- 不设定时巡逻，不做实时群聊。
- 小咩不做最终批准，只汇总呈现，等 Lo 验收。

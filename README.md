# agent-hq-test

多 Agent 协作 V1 验证：私有测试仓库。

## 流程

创建任务 → Codex 执行 → Claude Code 复核 → 小咩汇总 → 用户验收

## 目录结构

- `inbox/` — 任务池：新任务以 Markdown 文件放入这里
- `plans/` — 方案区：小咩写的任务拆解与分工方案
- `work/codex/` — Codex 的执行工作区
- `work/claude-code/` — Claude Code 的复核工作区
- `evidence/` — 验收证据：每个任务一个子目录
- `AGENTS.md` — 协作公约，所有参与的 agent 必读

## 角色

| 角色 | 人 | 职责 |
|------|-----|------|
| 群主 | Lo | 唯一批准人，发起任务、最终验收 |
| 主持人 | 小咩 | 协调分工、汇总结果，**无最终批准权** |
| 执行者 | Codex | 在 `work/codex/` 内执行任务 |
| 复核者 | Claude Code | 在 `work/claude-code/` 内复核，不直接改执行产出 |

# T001：agent-hq-test 测试报告

## 仓库用途

`agent-hq-test` 用无害的测试任务验证多 Agent 协作流程，不接入真实业务数据。任务从创建、执行、复核、汇总到用户验收，各阶段的文件和状态都保存在仓库中，便于检查。

## 角色与流程

| 角色 | 职责 |
| --- | --- |
| Lo | 发起任务，并独自决定是否最终验收。 |
| 小咩 | 整理需求、协调传话、汇总进度；不替 Lo 验收。 |
| Codex | 认领任务，在 `work/codex/<任务编号>/` 提交执行产出，并提供验收证据。 |
| Claude Code | 在 `work/claude-code/<任务编号>/` 提交复核意见，不直接修改 Codex 的产出。 |

任务文件放在 `inbox/`。正常状态依次为 `todo → in_progress → done → reviewed → accepted`；只有 Lo 能把状态改为 `accepted`。如复核未通过，公约允许 `done → in_progress`，并要求保留打回及解决记录。每次状态变化须单独提交。

`plans/` 存放小咩维护的方案，`evidence/<任务编号>/` 存放产出说明和可验证结果。各 Agent 按根目录 `AGENTS.md` 的文件隔离规则操作。

## T001 验证内容

本任务提供这份 Markdown 报告和 `hello.py`。脚本运行时输出 `Hello, world!`；实际运行结果记录在 `evidence/T001/run-output.txt`，产出位置记录在 `evidence/T001/README.md`。


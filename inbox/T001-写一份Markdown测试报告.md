status: in_progress

# T001 写一份 Markdown 测试报告

## 背景
这是多 Agent 协作 V1 验证的第一个测试任务，用于跑通「创建任务 → Codex 执行 → Claude Code 复核 → 小咩汇总 → 用户验收」完整流程。只用无害的测试内容，不触碰真实业务数据。

## 要求
1. 在 `work/codex/T001/` 下写一份 Markdown 测试报告，介绍 `agent-hq-test` 这个仓库是干嘛的（参考根目录 README 和 AGENTS.md）。
2. 附一段能实际运行的 Python hello world 代码（`work/codex/T001/hello.py`），运行截图或运行输出文本作为可验证产出。
3. 把验收证据放入 `evidence/T001/`：产出说明 + 运行输出。

## 验收标准
- 报告内容准确，无错别字，格式规范。
- `hello.py` 可运行，输出符合预期。
- `evidence/T001/` 下有产出说明和运行输出。

## 备注
- 遵守 AGENTS.md：只写 `work/codex/T001/` 和 `evidence/T001/`，不碰其他目录。
- 状态流转：认领时 `todo → in_progress`，提交产出后 `in_progress → done`，每次改状态单独提交。

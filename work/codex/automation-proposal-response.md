# Codex 对自动化提案七项议题的答复

| 项目 | 内容 |
| --- | --- |
| 日期 | 2026-10-09 |
| 状态 | 讨论稿；不是 `inbox/` 任务，也不启用任何定时程序 |
| 对应提案 | [proposal-agent-automation.md](../claude-code/proposal-agent-automation.md) 第五节 |

T001 已完成手动闭环。以下建议把自动化限定为「发现任务状态、触发复核、报告异常」，最终验收仍由 Lo 决定。设计同时吸收 [v1.4 修订建议](../claude-code/v1.4-suggestions.md) 中实际暴露的提交身份、证据可复现和规则单源问题。

## 1. 谁来当 watcher？

建议在 Lo 的 Windows 电脑上用任务计划程序每 5 分钟运行一次短脚本，而不是让每个 agent 自行轮询或保持常驻窗口。脚本使用独立克隆先比较远端 `main` 的提交；有更新才读取 `inbox/`。只对状态新变为 `done`、且产出与证据齐全的任务触发一次复核；以「任务编号 + `done` 提交 SHA」记录已处理项。任务设置为已有实例运行时忽略新实例。

GitHub Actions 可响应 `push`，但要运行本机 Claude Code 还需额外部署本机运行环境。GitHub Webhook 延迟更低，却需要可达的接收端、签名校验和漏送补偿。当前测试规模先用本机短时轮询，达到明显延迟或规模瓶颈后再比较这两种方案。

## 2. 通知渠道用什么？

任务转为 `reviewed`、自动复核失败或 watcher 熔断时，向 Lo 显示 Windows 通知，并在本机保留可查看的事件记录，包含任务编号、结果、提交 SHA 和仓库链接。任务状态仍以 `inbox/` 文件为准。不要为通知提交 `NOTIFY` 文件，以免额外提交反过来触发 watcher。若测试发现 Windows 通知容易漏看，再评估邮件备选。

## 3. 怎么防止错误被自动放大？

- 同一任务、同一 `done` 提交最多正常触发一次；处理前再次核对远端状态与证据。
- 复核程序设置运行时间、模型轮次和每日调用上限；脚本异常、证据缺失、状态冲突均停止该任务。
- 临时网络故障最多重试一次；连续两次失败就冻结该任务，记录原因并通知 Lo，不能无限重试。
- `accepted` 永远不由 watcher 或复核 agent 写入；打回只走 `AGENTS.md` 定义的状态流转。

Claude CLI 的 `--max-turns` 可限制轮次；`--max-budget-usd` 是客户端估算值，可能超过阈值，不能独自充当精确的费用熔断。外层脚本还要执行时间与调用次数限制。

## 4. 无头模式的权限边界是什么？

Claude Code 在独立临时目录读取任务和证据、生成复核意见；它不直接持有 Git 推送凭据。固定脚本检查变更，只接受 `work/claude-code/<任务编号>/`、公约允许的 `evidence/<任务编号>/`，以及对应任务文件的授权状态或 `review:` 行。通过校验后，脚本在 Claude Code 自己的克隆中按其本地提交身份提交和推送；失败则不提交，并通知 Lo。

无头会话可采用 `dontAsk` 拒绝未预批准操作，但 `--allowedTools` 只是预批准工具，并不构成文件路径隔离。必须由临时目录、操作系统权限和提交前的路径白名单共同约束；不使用跳过权限检查的模式。正式启用前还要在实际运行的 Windows 会话验证 `claude -p` 的可执行路径、登录状态和权限行为。

## 5. 成本怎么比较？

先用 10 个无害任务试跑，逐次记录：人工转达分钟数、从 `done` 到 `reviewed` 的时间、无头复核次数、模型用量、失败及误触发次数。无新提交时只检查 Git，不启动模型。由 Lo 预先确定每日可接受调用上限，超过就停止自动触发。试跑后按「节省的人工时间 / 新增调用和维护成本」作决定，不凭假定单价推算。

## 6. 代码放哪个仓库？

建议自动化代码和测试独立存放，`agent-hq-test` 继续只承载无害任务、产出与证据。本机运行状态、日志和凭据不提交到 Git。先在本机做手动测试；决定长期使用后再建立独立的自动化仓库，以免工具实现和任务协议混在一起。

## 7. 是否违反 V1 的「不设定时巡逻」？

是。V1 下只手动执行单次检测与复核演练，不注册定时任务。若 Lo 决定启用，应先批准 V2 公约，明确监测范围、触发条件、失败熔断、写入权限、调用上限和人工验收边界；保留 V1 作为已经验证的手动流程。

## 建议实施顺序

手动检测状态 → 只通知试跑 → 无头复核试跑 → Lo 批准 V2 → 启用定时触发。每一步通过后再进入下一步。

## 参考资料

- [Claude Code CLI reference](https://code.claude.com/docs/en/cli-reference)
- [Claude Code permissions](https://code.claude.com/docs/en/permissions)
- [Windows Task Scheduler repetition](https://learn.microsoft.com/en-us/windows/win32/taskschd/repeating-a-task) 与 [multiple instances policy](https://learn.microsoft.com/en-us/windows/win32/taskschd/taskschedulerschema-multipleinstancespolicy-settingstype-element)
- [GitHub Actions workflow syntax](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax)
- [GitHub Webhook best practices](https://docs.github.com/en/webhooks/using-webhooks/best-practices-for-using-webhooks)

# AGENTS.md — 多 Agent 协作公约（V1）

> 所有参与本仓库协作的 agent（Codex、Claude Code、小咩）在开始工作前必须阅读本文件。
> 最高原则：Lo 是唯一的批准人。任何 agent 不得代替 Lo 做验收决定。

## 1. 角色

- **Lo（群主）**：发起任务，唯一能把任务状态改为 `accepted` 的人。
- **小咩（秘书）**：Lo 和 agent 们之间的秘书，只对 Lo 负责。职责三件套——传话（把 Lo 的大白话需求写成规范任务，把 agent 的产出翻译成人话汇报）、过滤（执行细节不打扰 Lo，只递上需要拍板的事）、记账（任务状态、进度、卡点随问随答）。只协调，不批准；无最终批准权。
- **Codex（执行者）**：按任务要求在 `work/codex/` 内产出结果。
- **Claude Code（复核者）**：复核 Codex 的产出，把复核意见写在 `work/claude-code/`，**不得直接修改 `work/codex/` 下的文件**。

## 2. 任务状态机

任务文件放在 `inbox/`，文件名格式：`T001-任务简称.md`。
文件正文第一行必须是状态行，格式：`status: <状态>`。

状态按以下顺序流转，不许跳步；回退只允许一种情况（见打回规则）：

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

- Codex 的**执行产出**只写在 `work/codex/<任务编号>/`；除本公约明确授权的 `evidence/<任务编号>/` 和任务文件的 status 行外，不碰其他目录。
- Claude Code 的**复核意见**只写在 `work/claude-code/<任务编号>/`；除 `evidence/<任务编号>/`、任务文件的 status 行、以及打回时追加的 `review:` 行外，不碰其他目录。
- 验收证据（测试输出、截图、复核清单）放在 `evidence/<任务编号>/`，由产出方写入。
- `inbox/` 内任务正文由发起人（和小咩代笔）维护；**第一行的 status 行由流程中对应的 agent 更新**（Codex 写 `in_progress`/`done`，Claude Code 写 `reviewed`，Lo 写 `accepted`）。
- `plans/` 由小咩维护，其他人只读不写。
- 任何 agent 不得删除他人文件。有冲突先停下来，在任务文件里留言，等 Lo 裁决。

## 4. 验收证据与打回

任务要进入 `reviewed`，`evidence/<任务编号>/` 下必须有：
- 执行产出说明（做了什么、在哪）
- 可验证的结果（测试输出、截图、或复核清单）

验收证据必须可独立复现，须包含：
1. 工具的**完整路径**（如 `where python` 的输出），而不只是版本号
2. 可复现的**命令原文**
3. **执行环境**（哪台机器 / 哪个克隆目录）

没有证据或复核不通过，复核者有权打回，打回动作如下：
1. 在任务正文追加一行：`review: rejected（原因）`
2. 把状态从 `done` 回退为 `in_progress`，单独提交，commit message 注明返工，例如：`T001: done → in_progress (rework)`
3. Codex 返工后重新提交，状态再次变为 `done`，等待复核
4. 返工通过后，在 `review: rejected` 行下方追加 `review: resolved`，保留审计痕迹（不要删除 rejected 行）

## 5. V1 禁止事项

- 不接入、不触碰任何真实业务数据，只用无害的测试任务。
- 不设定时巡逻，不做实时群聊。
- 小咩不做最终批准，只汇总呈现，等 Lo 验收。

## 6. 提交身份

每个 agent 在自己的本地 clone 里设置独立的提交身份（只对本仓库生效，不影响他人）：

| Agent | user.name | user.email |
|---|---|---|
| Lo | Lo | loro970728@gmail.com |
| Codex | Codex | loro970728@gmail.com |
| Claude Code | Claude Code | loro970728@gmail.com |

设置命令：

```
git config --local user.name "<你的名字>"
git config --local user.email "loro970728@gmail.com"
```

设置后**必须验证**：

```
git config --local user.name          # 应输出你的名字
```

首次提交后再核对：

```
git log -1 --format='%an <%ae>'       # 应显示 <你的名字> <loro970728@gmail.com>
```

名字独立便于在 `git log` 里区分 agent；邮箱共用 Lo 已关联的邮箱，GitHub 上仍关联 Lo 的账号（有头像、计入贡献）。

小咩经 GitHub App 直接写入远程仓库，不经过本地 clone，提交作者显示为仓库所有者，commit message 中注明代笔/批准情况。

## 7. 单源真相

规则只在 `AGENTS.md` 定义一次。

各子目录的 `README.md` 只说明"**本目录放什么**"，涉及"**规则是什么**"一律写"见 `AGENTS.md`"，不得复述。

理由：复述必过期。历史上已因复述造成三次规则不同步（v1.1 漏改 status 行相邻行、v1.2 漏改两处 README、v1.3 漏改 codex README）。

## 修订记录

- v1.4（2026-10-09）：采纳 Claude Code《V1.4 修订建议》全部四节——① 提交身份：名字独立 + 共用已关联邮箱，强制验证步骤（Lo 已定稿）；② 验收证据须可独立复现（完整路径 / 命令原文 / 执行环境）；③ 单源真相：规则只在 AGENTS.md 定义一次，子目录 README 不得复述。P5（work/codex/README.md）Codex 已自行修复，无需改动。
- v1.3（2026-10-09）：Claude Code 审查报告的 3 处遗留问题——① §3 明确授权例外：执行产出目录与 evidence/、status 行、review: 行的授权分开写（P1）；② 打回通过后追加 `review: resolved` 保留审计痕迹（P3）；③ README.md / plans/README.md 中"主持人"同步为"秘书"（P2，见对应文件）。
- v1.2（2026-10-09）：小咩身份更新为"秘书"（Lo 钦定）：只对 Lo 负责，职责为传话、过滤、记账；无最终批准权。
- v1.1（2026-10-09）：修复两处矛盾，均由 Claude Code 在 Codex 进场前审查发现——① inbox/ 写入权限：status 行由流程中对应的 agent 更新（原 §2 与 §3 打架）；② 打回后状态卡死：允许打回时 `done → in_progress` 回退并注明原因（原"不许回退"与"打回保持 done"语义冲突）。

# Issue 跟踪器：GitHub

本仓库的需求和规格均以 GitHub Issue 形式管理。所有操作使用 `gh` CLI。

## 约定

- **创建 Issue**：`gh issue create --title "..." --body "..."`。多行正文使用 heredoc。
- **读取 Issue**：`gh issue view <number> --comments`；同时获取标签，并可用 `jq` 过滤评论。
- **列出 Issue**：`gh issue list --state open --json number,title,body,labels,comments --jq '[.[] | {number, title, body, labels: [.labels[].name], comments: [.comments[].body]}]'`，按需追加 `--label` 和 `--state` 筛选。
- **评论 Issue**：`gh issue comment <number> --body "..."`
- **添加或移除标签**：`gh issue edit <number> --add-label "..."` / `--remove-label "..."`
- **关闭 Issue**：`gh issue close <number> --comment "..."`

从 `git remote -v` 推断仓库；在该克隆目录内运行时，`gh` 会自动完成此项。

## 将 PR 作为 Triage 来源

**PR 是否作为需求入口：否。** 若本仓库把外部 PR 也作为功能请求，请改为 `yes`；`/triage` 会读取此标记。

设为 `yes` 后，PR 与 Issue 使用同一套标签和状态，但改用相应的 `gh pr` 命令：

- **读取 PR**：`gh pr view <number> --comments`；需要查看改动时使用 `gh pr diff <number>`。
- **列出待 Triage 的外部 PR**：`gh pr list --state open --json number,title,body,labels,author,authorAssociation,comments`，只保留 `authorAssociation` 为 `CONTRIBUTOR`、`FIRST_TIME_CONTRIBUTOR` 或 `NONE` 的项。
- **评论、打标签、关闭**：分别使用 `gh pr comment`、`gh pr edit --add-label` / `--remove-label`、`gh pr close`。

GitHub 的 Issue 和 PR 共用编号空间。遇到裸编号如 `#42` 时，先用 `gh pr view 42` 解析；失败后再用 `gh issue view 42`。

## 技能要求“发布到 Issue 跟踪器”时

创建一个 GitHub Issue。

## 技能要求“获取相关工单”时

运行 `gh issue view <number> --comments`。

## Wayfinder 操作

供 `/wayfinder` 使用。**地图**是一个单独的 Issue，**子工单**为其子 Issue。

- **地图**：创建带 `wayfinder:map` 标签的 Issue，正文包含 Notes / Decisions-so-far / Fog。命令：`gh issue create --label wayfinder:map`。
- **子工单**：通过 GitHub 子 Issue API 将 Issue 关联到地图。若未启用子 Issue，则在地图正文添加任务清单，并在子工单开头写入 `Part of #<map>`。标签为 `wayfinder:<type>`，其中 `<type>` 可为 `research`、`prototype`、`grilling` 或 `task`。认领后，分配给当前执行者。
- **阻塞关系**：优先使用 GitHub 原生 Issue dependency。通过 `gh api --method POST repos/<owner>/<repo>/issues/<child>/dependencies/blocked_by -F issue_id=<blocker-db-id>` 添加边；`<blocker-db-id>` 是阻塞 Issue 的数据库 ID，可由 `gh api repos/<owner>/<repo>/issues/<n> --jq .id` 获取，不是 `#编号` 或 `node_id`。若依赖功能不可用，在子工单开头写 `Blocked by: #<n>, #<n>`。所有阻塞 Issue 关闭后，子工单解除阻塞。
- **前沿查询**：列出地图的未关闭子工单，排除仍有未关闭阻塞项或已有 assignee 的工单；地图顺序中最靠前者优先。
- **认领**：使用 `gh issue edit <n> --add-assignee @me`；这是该会话的首次写操作。
- **完成**：先 `gh issue comment <n> --body "<answer>"`，再 `gh issue close <n>`，最后将上下文指针（gist 与链接）补入地图的 Decisions-so-far。

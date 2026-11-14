---
title: "练习 5：迭代改进 GitHub Copilot 的工作"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 上一课：管理智能体][previous-lesson] |
|:--|

## 审查工作

在本研讨会中，你使用 GitHub Copilot 执行了多项任务，以改善用户体验并添加功能：为代码添加文档、构建相关游戏功能供设计团队迭代，以及实现高对比度和浅色模式等无障碍功能。现在检查代码更改；必要时向 Copilot 提供反馈，改进其工作。

### 场景

多次强调过，加入生成式 AI 不会改变软件设计和 DevOps 的基本原则。仍需审查生成的代码并遵循正常的 DevOps 流程。接下来审查 Copilot 为文档、相关游戏功能和无障碍功能提出的更改，然后再允许团队其他成员审查。

## 安全和 GitHub Copilot 云智能体

Copilot 云智能体会异步且无人监督地执行任务，因此设有安全限制：

- Copilot 只有存储库读取权限，并且仅对用于编写代码的分支具有写入权限。
- 云智能体在 GitHub Actions 中运行，并在独立的临时环境中工作。
- 运行任何 GitHub Actions 工作流前，都需要人工批准。
- 默认情况下，[对外部资源的访问受到限制](https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall)，包括 MCP 服务器。

## 审查生成的文档

先通过 GitHub.com 上的标准 PR 界面，检查 Copilot 云智能体创建的第一个 PR（为代码添加文档）。

> [!NOTE]
> PR 中可能显示 GitHub Copilot 被防火墙阻止的警告，这是预期行为。默认情况下，Copilot 访问外部资源（包括外部 MCP 服务器）受到限制。如有需要，可以[自定义或禁用云智能体防火墙][agent-firewall]。

1. 返回 github.com 上的存储库，选择 **Pull Requests** 选项卡。
2. 打开标题类似 **Add missing documentation** 的 PR。

> [!NOTE]
> 如果 Copilot 仍在工作，PR 标题会带有 **[WIP]** 标记。请等待 Copilot 完成，这可能需要几分钟。

3. PR 准备好后，选择 **Files changed** 选项卡并审查更改。

   ![Files changed 选项卡](../../../_images/shared-pr-files-changed.png)

4. 检查新增的 TSDoc 文档注释及其他文档。具体更改会有所不同。留意你在练习 2 开始时查看的自定义说明所要求的 TSDoc 注释、TypeScript 约定和文件头注释。
5. 确认更改无误后，返回 **Conversation** 选项卡并向下滚动。
6. 如果看到有工作流等待批准，选择 **Approve and run workflows**。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

7. PR 的检查区域会显示已排队的工作流。如果一切正常，单个 Astro 应用的项目检查应通过；这可能需要几分钟。

## 请求 GitHub Copilot 更改

在 PR 中与 Copilot 协作并非单向操作。你可以像 @提及团队成员一样，在 PR 评论或代码行内评论中提及 Copilot。它会看到评论并启动新会话处理更改。由于结果具有非确定性，无法指定固定文本，但可要求 Copilot：

- 在代码文件开头添加简短说明文件用途的注释。
- 为 TypeScript 和 Astro 文件添加 TSDoc 文档注释。
- 创建 README，介绍 Astro 应用结构。

1. 在评论中使用 **@copilot** 请求更改；可以采用上述建议，或提出其他文档改进要求。
2. 选择 **View Session** 观察 Copilot 如何启动新会话并执行更新。
3. 选择 **Back to pull request** 返回 PR。
4. Copilot 完成后，PR 中应出现新提交。选择 **Files changed** 审查更改。

可以继续迭代，直到满意为止。完成后，可将 PR 从草稿转换为准备审查状态，再合并到主分支。

![将 PR 转为准备审查状态](../../../_images/ex7-ready-for-review.png)

## 审查相关游戏功能

返回 Copilot 为“在游戏详情页显示相关游戏”议题创建的 PR：

1. 打开 github.com 上的存储库，选择 **Pull Requests** 选项卡。
2. 选择标题类似 **Show related games on the game details page** 的 PR。
3. 选择 **Files changed** 审查代码。
4. 确认更改无误后，返回 **Conversation** 选项卡并向下滚动。
5. 如果有工作流等待批准，选择 **Approve and run workflows**。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

6. 检查区域会显示已排队的工作流。单个 Astro 应用的项目检查通过可能需要几分钟。
7. **可选：**在 Codespace 中手动测试相关游戏功能。打开终端，将 `<branch-name>` 替换为 Copilot 创建的分支名（例如 **copilot/fix-8**），然后运行：

   ```bash
   git fetch origin
   git checkout <branch-name>
   ```

Copilot 已构建相关游戏功能。你可以继续通过 PR 评论请求调整，例如更改相关游戏的数量，或补充文件头和 TSDoc 注释。注意，该任务是在更新自定义说明之前分配的。与前面相同，在 **Conversation** 选项卡添加评论后，Copilot 会启动新会话。

## 审查无障碍功能

最后，审查通过无障碍自定义智能体实现的功能。PR 应包含练习 3 中分配的高对比度模式，以及练习 4 中在任务控制中心追加的浅色模式。

1. 返回 github.com 上的存储库并打开 **Pull Requests** 选项卡。
2. 选择标题类似 **Add high contrast mode to website** 的 PR。

> [!NOTE]
> 如果 Copilot 仍在处理任务，PR 标题会带有 **[WIP]** 标记。等待 Copilot 完成，这可能需要几分钟。

3. 选择 **Files changed** 审查代码，重点查看模式切换 UI、使用本地存储保存用户偏好、高对比度和浅色模式的 CSS/样式、ARIA 属性和键盘导航，以及管理模式切换的 JavaScript/TypeScript 代码。
4. 更改无误后返回 **Conversation** 选项卡并向下滚动。如果有工作流等待批准，选择 **Approve and run workflows**。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

5. 等待检查区域中的工作流完成。单个 Astro 应用的项目检查通过可能需要几分钟。
6. **可选：**在 Codespace 中手动测试无障碍功能。打开终端，将 `<branch-name>` 替换为 Copilot 创建的分支名，然后运行：

   ```bash
   git fetch origin
   git checkout <branch-name>
   ```

   启动应用，并在浏览器中测试高对比度和浅色模式切换，确认功能正常且偏好在重新加载页面后仍然保留。

无障碍自定义智能体帮助 Copilot 按照无障碍最佳实践实现功能。如果发现问题或改进点，可像处理之前的 PR 一样在评论中提及 **@copilot** 请求更新。

## 可选练习 — 继续委派

云智能体适合处理待办事项，让你专注于其他工作。可在存储库中创建更多议题并分配给 Copilot，例如：

- 在游戏详情页创建支持者兴趣表单。
- 为游戏列表页添加分页。
- 为数据访问辅助函数添加输入验证和错误处理。

## 总结

你已完成云智能体研讨会。本系列中，你：

- **检查了存储库提供的自定义说明文件**，以便之后观察它们对云智能体输出的影响。
- **将议题分配给 Copilot 云智能体**，观察它设置环境、制定计划并异步执行。
- **创建并使用自定义智能体**实现无障碍功能，添加高对比度和浅色模式切换。
- **使用 Copilot 智能体页面作为任务控制中心**，监控并引导无障碍会话。
- **审查并迭代改进云智能体的 PR**，通过提及 `@copilot` 请求更改并批准工作流。

## 回顾和后续步骤

云智能体研讨会已完成。其他研讨会可以帮助你继续探索：

- 🖥️ **[VS Code 研讨会](../../vscode/)** — 在 IDE 中探索 Copilot Chat 智能体模式和 MCP 集成。
- 💻 **[CLI 研讨会](../../cli/)** — 在终端中交付两项经过审查的更改，练习 Plan 和 Autopilot 模式、智能体技能、自定义 QA 智能体、Playwright MCP、Agent Merge，以及可选的 `/delegate` 云智能体桥接。

在自己的存储库中，还可以尝试将重构或测试覆盖率议题分配给云智能体、为其他领域创建自定义智能体，或为不同技术栈设置 `copilot-setup-steps.yml`。

## 资源

- [GitHub Copilot][github-copilot]
- [关于 Copilot 智能体][copilot-agents]
- [将 GitHub 议题分配给 Copilot][assign-issue]
- [Copilot 云智能体设置工作流最佳实践][cloud-agent-best-practices]
- [配置 Copilot 云智能体防火墙][agent-firewall]

---

| [← 上一课：管理智能体][previous-lesson] |
|:--|

[github-copilot]: https://github.com/features/copilot
[cloud-agent-overview]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[agent-firewall]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall
[previous-lesson]: ../4-managing-agents/

---
title: "第 1 课 - 安装 GitHub Copilot app"
description: "安装 GitHub Copilot app，连接通过模板创建的存储库，熟悉工作区并尝试快速聊天。"
authors:
  - geektrainer
lastUpdated: 2026-10-22
---

**[GitHub Copilot app][about-copilot-app]** 是一款用于智能体驱动开发的桌面应用。它基于 GitHub Copilot CLI 构建，并与 GitHub 原生集成，因此存储库、分支和 CI 管道均可直接使用。它适用于同时指挥多个智能体的工作流：每个智能体都在隔离的工作区中运行，让你无需亲自完成所有工作，并可自动执行重复性任务。安装 Node.js 并准备好项目副本后，下一步是安装应用并连接该存储库。

本课将带你：

- 安装 GitHub Copilot app 并登录。
- 从 GitHub 存储库将项目添加到应用。
- 熟悉工作区，包括模板创建的待办事项。
- 通过快速聊天了解应用本身。

## 场景

团队正采用 AI 智能体处理不断增加的待办事项。Copilot app 提供统一的工作位置，用于领取议题、运行智能体、审查更改并合并拉取请求。本课将帮助你完成安装和连接，并熟悉如何围绕项目发起对话。

> [!NOTE]
> 需要符合条件的 Copilot 计划：Copilot Free、Copilot Student 或任一付费计划（Pro、Pro+、Business 或 Enterprise）。如果使用 Copilot Business 或 Copilot Enterprise，管理员必须启用 **GitHub Copilot app** 策略。

## 安装并配置 GitHub Copilot app

GitHub Copilot app 支持 Windows、macOS 和 Linux。接下来安装应用、完成身份验证，并将 Tailspin Toys 存储库添加到应用。

1. 在浏览器中打开 [GitHub Copilot app 产品页面][download-app]。
2. 下载适用于当前平台的应用，并按产品页面上的说明安装。
3. 安装完成后打开应用。
4. 选择 **Sign in to GitHub**，并按提示使用 GitHub.com 或 GHE.com 账户登录。如果使用支持数据驻留的 GitHub Enterprise Cloud，选择 **GitHub Enterprise Cloud**，并在提示时输入 `*.ghe.com` 主机名。

   如果使用 GitHub Enterprise Server，先使用云账户完成初始设置。然后打开 **Settings**，依次选择 **Accounts**、**Add account** 和 **GitHub Enterprise Server**。按照 [Enterprise Server 登录指南][ghes-sign-in]中的提示，在浏览器中授权应用。

5. 如果初始设置流程提供了存储库选择，请选择通过模板创建的 Tailspin Toys 存储库。也可以跳过存储库选择，完成初始设置后再添加项目。
6. 按提示选择主题，然后完成初始设置。
7. 在侧边栏的 **Sessions** 下查找存储库。如果已经列出，无需再次添加。
8. 如果尚未连接存储库，选择 **Sessions** 旁的 **+** 创建新会话，然后选择 **Add GitHub repository**。输入先决条件课程中记录的所有者和存储库名称。
9. 确认存储库出现在侧边栏中。此布局会在 **Sessions** 下按存储库分组（例如 `tailspin-toys`），每个存储库下方再列出各个会话。

## 熟悉工作区

连接项目后，花些时间熟悉工作区。应用在侧边栏中将功能组织为几个区域：

- **New**：启动与 Copilot 的新聊天会话。
- **Pull requests**：通过应用与 GitHub 的原生集成显示拉取请求。无需离开应用即可浏览和筛选 PR、检查 CI 状态并审查 PR。
- **Issues**：以同样的方式显示议题。可以浏览和筛选议题，也可以从议题启动会话。
- **Automations**：可按计划或按需运行的已保存智能体任务，适合管理待办事项、定期项目维护和其他重复工作。总结课程会将其作为后续学习方向，而不是另一项研讨会练习。
- **Customize**：通过 MCP 服务器、插件、技能和其他组件为 Copilot app 添加功能。你将使用它配置 Playwright MCP。
- **Chats**：用于提问和集思广益的轻量对话，无需单独创建分支或工作区。启动第一次聊天后，此区域才会显示。如果未显示，请选择 **Sessions** 旁的 **+** 开始第一次聊天。
- **Sessions**：按存储库文件夹分组显示智能体会话。选择存储库名称（例如 `tailspin-toys`）可打开存储库视图；选择存储库下的会话可返回该对话。每个会话都在独立工作区中运行，因此可以同时运行多个会话，而更改不会冲突。下一课将启动第一个会话。

随着研讨会继续进行，你会逐步了解工作区。

> [!TIP]
> 不确定如何操作或某项功能是否可用时，可以直接询问 Copilot，它会提供帮助。

### 查找模板创建的待办事项

几乎每个项目都有待办事项，Tailspin Toys 也不例外。现在查看创建模板时生成的待办事项：

1. 在侧边栏的 **Sessions** 下选择 Tailspin Toys 存储库文件夹，而不是其下的某个会话。然后在存储库顶部导航中选择 **Issues**。
2. 按标题查找以下议题，不要假设它们的议题编号：

   - Allow users to filter games by category and publisher
   - Update our repository coding standards

3. 选择议题阅读详情。每个议题也可以作为启动智能体会话的入口。完成快速的首次更改后，你将从筛选议题开始工作。

> [!NOTE]
> **Issues** 和 **Pull requests** 列表会自动筛选，只显示已添加到 Copilot app 的存储库中的项目。若要查看其他存储库的工作项，请将相应存储库添加到应用。

## 尝试快速聊天

熟悉应用的一种好方法是用它了解*应用本身*。**快速聊天**无需创建分支或工作树即可提问或集思广益，因此适合一次性问题，无需创建会话。

1. 在侧边栏中选择 **Chats** 旁的 **+** 以打开新聊天。如果尚未显示 **Chats**，选择 **Sessions** 旁的 **+** 开始第一次聊天；之后 **Chats** 区域就会出现。
2. 询问应用自身的会话工作方式：

   ```plaintext
   How does the GitHub Copilot app use worktrees?
   ```

3. 在对话视图中阅读回复。每个会话都在独立的 Git 工作树中运行，因此可以并行运行多个智能体，而更改不会冲突。可以随时继续对话或开始新聊天。

## 总结和后续步骤

你已安装 GitHub Copilot app、连接项目并探索工作区。你了解了如何：

- 安装应用并登录 GitHub。
- 从 GitHub 存储库添加项目。
- 熟悉工作区并在 **Issues** 中查找模板创建的待办事项。
- 使用快速聊天提出一次性问题。

接下来，[启动第一个智能体会话][next-lesson]，并用它在游戏卡片上显示星级评分。

## 资源

- [关于 GitHub Copilot app][about-copilot-app]
- [GitHub Copilot app 入门][getting-started]
- [在 GitHub Copilot app 中使用智能体会话][agent-sessions]

[next-lesson]: ../2-add-star-rating/
[about-copilot-app]: https://docs.github.com/copilot/concepts/agents/github-copilot-app
[getting-started]: https://docs.github.com/copilot/how-tos/github-copilot-app/getting-started
[agent-sessions]: https://docs.github.com/copilot/how-tos/github-copilot-app/agent-sessions
[download-app]: https://gh.io/app
[ghes-sign-in]: https://docs.github.com/enterprise-server@3.22/copilot/copilot-on-ghes/sign-in-on-copilot-app

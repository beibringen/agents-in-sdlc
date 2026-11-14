---
title: "练习 2 - GitHub Copilot 云智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 上一课：自定义说明][previous-lesson] |
|:--|

几乎所有组织都要应对技术债务，例如尚未解决的安全问题、需要更新的旧代码，或因没有时间实现而长期积压的功能请求。GitHub Copilot 云智能体可以自主执行更新代码和添加功能等任务。完成后，它会创建草稿 PR，供开发人员审查。这样可以将繁琐任务交给智能体，加快开发进程，让开发人员专注于更重要的工作。

本练习将带你了解：

- 如何自定义代码生成环境。
- 如何确保操作安全。
- 明确界定议题范围的重要性。
- 如何将议题分配给 Copilot。

## 场景

Tailspin Toys 有一些技术债务需要处理。最初受雇开发网站的承包商没有留下文档，因此第一项任务是为应用中所有导出的函数添加 TSDoc 文档注释。

此外，设计团队希望改进游戏发现体验：在每款游戏的详情页显示同类别的其他游戏，方便用户继续浏览。他们暂时不需要完善的实现，只需要可用于验收测试用户体验的版本。此需求目前受阻，但优先级低于其他议题。

这些都是容易被延后的任务，适合交给 Copilot 云智能体异步处理。开发人员可以继续处理其他工作，之后再回来审查 Copilot 的成果。

## 了解 GitHub Copilot 云智能体

[GitHub Copilot 云智能体](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent)可以像开发人员一样在后台执行任务。可以通过多种方式启动它，例如[将 GitHub 议题分配给 Copilot](https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions)。分配任务后，Copilot 会创建草稿拉取请求来跟踪进度、准备环境并开始工作。你可以在任务进行中或完成后查看会话。解决方案准备好供审查时，它会在拉取请求中提及你。

## 明确界定说明范围的重要性

GitHub Copilot 并非魔法，使用它并不能省去思考。即使看似简单的操作，也可能包含复杂细节。因此，务必[谨慎确定交给 Copilot 云智能体的任务范围][cloud-agent-best-practices]。将它视作 AI 结对程序员，分阶段工作、学习、试验并调整。引入生成式 AI 不会改变软件开发的基本原则。

## 本存储库中的自定义说明

之前的练习介绍了自定义说明及其如何引导 Copilot。如果已完成自定义说明练习，你就见过 `.github/copilot-instructions.md` 的效果；否则，现在可以快速查看。分配任务前，以只读方式检查存储库中的说明文件，之后更容易发现它们的影响：

- `.github/copilot-instructions.md`：查看 **Code standards**，尤其是 TypeScript 约定和 TSDoc 文档注释要求。
- `.github/instructions/unit-tests.instructions.md`：注意 `applyTo` frontmatter，它将说明限定于 `**/*.test.ts` 文件。

在下一节将 `Code lacks documentation` 议题分配给云智能体后，观察其 PR 中的 TSDoc 注释、TypeScript 约定和文件头注释。这些要求来自上述说明文件；后续练习会再次检查它们。

## 设置 Copilot 云智能体的开发环境

开发通常需要特定环境和设置脚本，以确保运行状态正常。向 Copilot 分配任务时也一样，因为它的工作方式类似软件工程师。

云智能体使用 [GitHub Actions][github-actions] 环境。可在 `.github/workflows/copilot-setup-steps.yml` 中创建[专用设置工作流][setup-workflow]，让它在开始工作前安装所需工具和依赖项。实验室已预先配置此工作流，以安装 Node.js、项目依赖项、端到端测试所需的浏览器，以及供单个 Astro 应用使用的迁移并填充数据的 SQLite 数据库：

```yaml
name: "Copilot Setup Steps"

# 允许你从存储库的 "Actions" 选项卡测试设置步骤
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # 将权限限制为步骤所需的最低级别。Copilot 会使用自己的令牌执行操作。
    permissions:
      # 如果要在设置步骤中克隆存储库（例如安装依赖项），需要 `contents: read` 权限。如果未在设置步骤中克隆，步骤完成后 Copilot 会自动为你克隆。
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # 前端/应用设置 - Node.js（整个应用现在使用 Astro + Drizzle/libSQL）
      - name: Set up Node.js
        uses: actions/setup-node@v6
        with:
          node-version: "lts/*"
          cache: "npm"
          cache-dependency-path: "./package-lock.json"

      - name: Install JavaScript dependencies
        run: npm ci

      - name: Install Playwright browsers
        run: npx playwright install --with-deps chromium

      # 迁移并填充本地 SQLite 数据库，以便构建和测试有可用数据。
      - name: Set up the database
        run: npm run db:setup
```

这看起来与其他 GitHub 工作流相似，但有几点值得注意：

- 其中只有一个名为 `copilot-setup-steps` 的作业。Copilot 开始处理 PR 前，GitHub Actions 会运行此作业。
- `workflow_dispatch` 触发器允许你在存储库的 **Actions** 选项卡中手动运行工作流，以便先验证它能否正常运行，而不必等待 Copilot 启动。

## 添加文档

大多数项目都存在文档过时或缺失的问题。这类技术债务会降低工作效率，也增加维护代码库或引入新团队成员的难度。Copilot 擅长生成文档，因此很适合处理此议题。它会在后台生成文档，后续练习中再审查结果。

1. 在新浏览器选项卡中打开 github.com 上的存储库。
2. 选择 **Issues** 选项卡，再选择 **New issue**。
3. 选择 **Blank issue**。
4. **Title** 输入 `Code lacks documentation`。
5. **Description** 输入以下内容：

   ```plaintext
   Our organization has a requirement that functions and methods include TSDoc doc comments where helpful. Unfortunately, recent updates haven't followed this standard. We need to update the existing code to ensure doc comments are included where they clarify behavior.
   ```

6. 选择 **Create** 创建议题。
7. 在右侧选择 **Assign to Copilot**，打开分配对话框。

   ![将 Copilot 分配给议题](../../../_images/shared-assign-copilot.png)

8. 选择 **Assign**。
9. 选择 **Pull Requests** 选项卡，并打开刚创建的 PR。标题可能类似于 `[WIP]: Code lacks documentation`。如果列表中尚未出现新 PR，稍等片刻并刷新浏览器。
10. 几分钟后，Copilot 应会创建待办列表。

> [!NOTE]
> Copilot 可能需要几分钟才会在 PR 中显示待办列表。它正在创建环境（运行前面介绍的工作流）、分析项目并确定处理任务的方案。

11. 检查任务列表和计划执行的工作。
12. 向下滚动 PR 时间线，查看 Copilot 已开始处理议题的更新。
13. 选择 **View session** 按钮。

   ![Copilot 会话视图](../../../_images/ex4-view-session.png)

> [!CAUTION]
> 可能需要刷新窗口才能看到更新后的状态。

14. 查看实时会话，观察 Copilot 如何探索代码、了解状态、暂停思考并选择方案，然后编写代码。

这可能需要几分钟。云智能体的一个主要优点是可以异步工作，让你去处理其他任务。我们将利用这一点，再分配一个任务给云智能体，然后转而为应用编写功能代码。

## 在游戏详情页添加相关游戏

GitHub Copilot 云智能体的一个优点是可以分配不同的工作，让你处理一项任务时由它负责另一项任务。为设计团队添加相关游戏可能不会花太久，但这段时间仍可用于其他工作。现在将任务分配给云智能体：

1. 返回 github.com 上的存储库，选择 **Issues** 选项卡，再选择 **New issue** 和 **Blank issue**。
2. **Title** 输入 `Show related games on the game details page`。
3. **Description** 输入以下内容：

   ```markdown
   We want to help people discover more games by showing related games on each game's details page. The design team wants to explore the UX and do some acceptance testing. Our requirements are:

   - Add a data-access helper in `src/lib/` that returns other games in the same category as a given game, excluding that game
   - Show a "Related games" section on the game details page that uses the helper
   - Handle the case where a game has no related games
   - There should be unit tests created for the new helper
   - Before creating the PR, ensure all tests pass
   ```

4. 选择 **Create** 创建议题。
5. 在右侧选择 **Assign to Copilot**，然后选择 **Assign**。

   ![将 Copilot 分配给议题](../../../_images/shared-assign-copilot.png)

稍后你会在议题的第一条评论旁看到一组 👀，表示 Copilot 已开始处理。

![Copilot 使用眼睛表情符号表示正在处理议题](../../../_images/ex4-issue-eyes-emoji.png)

Copilot 正在处理第二项请求。它的工作方式类似软件工程师，你无需一直监控，可以等任务完成后再审查。接下来将转而创建和使用自定义智能体。

## 总结和后续步骤

本课程介绍了 [GitHub Copilot 云智能体][copilot-agents]。你可以将议题分配给云智能体，让它异步处理技术债务、实现新功能，或协助将代码迁移到其他框架。

本课程中，你了解了如何自定义代码生成环境、确保操作安全、明确界定议题范围并将议题分配给 Copilot。

云智能体在后台工作时，可以转而创建和使用自定义智能体。[云智能体也可以使用 MCP 服务器][cloud-agent-mcp]，并可使用之前模块中介绍的自定义说明。

## 资源

- [关于 Copilot 云智能体][copilot-agents]
- [将 GitHub 议题分配给 Copilot][assign-issue]
- [Copilot 云智能体设置工作流最佳实践][cloud-agent-best-practices]

| [下一课：自定义智能体 →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions

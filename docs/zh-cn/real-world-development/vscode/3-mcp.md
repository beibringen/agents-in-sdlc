---
title: "练习 3 - 使用 Playwright MCP 服务器测试功能"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 上一课：智能体模式][previous-lesson] · [下一课：自定义智能体 →][next-lesson]

你刚用智能体模式构建了筛选功能。创建拉取请求前，应先在浏览器中确认功能正常。与其手动操作应用，不如连接 **Playwright MCP 服务器**，让 Copilot 驱动真实浏览器测试功能，然后发布分支并创建 PR。

本练习将带你了解：

- 什么是 Model Context Protocol (MCP)，以及 MCP 服务器如何使用新工具扩展 Copilot。
- 如何向工作区添加 Playwright MCP 服务器。
- 如何让 Copilot 在浏览器中手动测试筛选功能。
- 如何发布分支并为筛选工作创建拉取请求。

## 什么是 Model Context Protocol (MCP)？

智能体模式可以访问编辑器以外的工具，从而增强能力。Model Context Protocol (MCP) 是 Copilot 用来访问这些工具的标准方式，可让智能体与外部工具和服务通信。

![智能体模式内部工作原理示意图，显示它如何与上下文、LLM 和工具交互，包括 MCP 服务器及 VS Code 扩展提供的工具](../../../_images/ex1-mcp-diagram.png)

[Model Context Protocol (MCP)](https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/) 为 AI 智能体提供与外部工具和服务通信的方式。智能体可以通过资源获取最新信息，并通过工具代表你执行操作。

MCP 服务器充当智能体与外部工具和服务之间的桥梁，负责管理智能体与现有 API 或本地工具（如 NPM 包）的通信。每个服务器提供一组可供智能体访问的工具和资源。常用服务器包括：

- **[GitHub MCP Server](https://github.com/github/github-mcp-server)**：提供管理 GitHub 存储库、议题和拉取请求的 API。
- **[Playwright MCP Server](https://github.com/microsoft/playwright-mcp)**：使用 Playwright 提供浏览器自动化能力，可导航网页、填写表单和选择按钮。

还有许多提供不同工具和资源的 MCP 服务器。GitHub 提供 [MCP 注册表](https://github.com/mcp)，帮助发现并为生态系统做出贡献。

> [!CAUTION]
> 将 MCP 服务器视作项目中的其他依赖项。使用前，请仔细审查其源代码、验证发布者并考虑安全影响。只使用信任的 MCP 服务器，谨慎授予其访问敏感资源或执行敏感操作的权限。

## 查看 MCP 配置

`.vscode/mcp.json` 文件配置此 VS Code 工作区可使用的 MCP 服务器。

1. 在 Codespace 中打开 `.vscode/mcp.json`。
2. 你会看到已配置的 `github` 服务器：

   ```json
   {
     "servers": {
       "github": {
         "type": "http",
         "url": "https://api.githubcopilot.com/mcp/"
       }
     }
   }
   ```

项目模板包含此 `github` 配置，这也是 Copilot 能在上一练习中读取议题列表的原因。它使用[远程 GitHub MCP 服务器][remote-github-mcp-server]，无需本地安装；VS Code 通过 HTTP 连接，Copilot 首次使用其工具时需要使用 GitHub 身份验证。

## 添加 Playwright MCP 服务器

接下来添加第二个服务器。[Playwright MCP 服务器][playwright-mcp-server]为 Copilot 提供可控的浏览器，用于测试功能。

1. 在 `.vscode/mcp.json` 中，在 `github` 旁添加 `playwright` 配置：

   ```json
   {
     "servers": {
       "github": {
         "type": "http",
         "url": "https://api.githubcopilot.com/mcp/"
       },
       "playwright": {
         "command": "npx",
         "args": ["@playwright/mcp@latest", "--headless"]
       }
     }
   }
   ```

2. 保存文件。

与远程 `github` 服务器不同，`playwright` 是本地服务器：VS Code 会在你的计算机上运行 `npx @playwright/mcp@latest` 来启动它。`--headless` 标记让 Playwright 在没有可见窗口的情况下运行浏览器，这适用于没有桌面环境可显示窗口的 Codespace。

> [!NOTE]
> Tailspin Toys 项目已使用 Playwright 运行端到端测试，因此通常已安装浏览器。如果 Copilot 报告缺少浏览器，请让它运行 `npx playwright install chromium` 后重试。

## 启动并信任服务器

VS Code 会按需启动 MCP 服务器：Copilot 首次需要服务器工具时，VS Code 会启动它并询问你是否信任。

1. `.vscode/mcp.json` 中 `playwright` 配置上方会出现 **Start** 操作。选择它以立即启动服务器并确认配置正确。
2. 如果 VS Code 询问是否信任服务器，请先审查配置，然后选择信任以启动服务器。

手动启动是可选的；若跳过，VS Code 会在下一步 Copilot 首次使用服务器工具时启动它并询问是否信任。

## 测试筛选功能

Playwright MCP 服务器为 Copilot 提供可操作的真实浏览器。智能体可以打开页面、导航、应用筛选条件、读取结果并汇报，无需你离开对话手动浏览网站。

Playwright MCP 服务器使用页面的[无障碍树][playwright-mcp-server]，而不是截图。因此，智能体会通过结构化且有标签的元素（按钮、链接和列表项）进行推理，与辅助技术的工作方式相同；快速功能检查也能顺便进行基础的无障碍检查。

服务器已连接且应用正在运行时，使用以下提示测试筛选功能：

```text
Using the Playwright MCP server, open a browser to the running app at http://localhost:4321 and verify the new game filtering feature:

1. Go to the games page and note how many games are listed.
2. Apply a category filter and confirm the list updates to only show games in that category.
3. Clear it, then apply a publisher filter and confirm the list updates to that publisher.
4. Combine a category and a publisher filter and confirm the results respect both.

Report what you observe at each step, and call out anything that does not behave as expected.
```

Copilot 会通过 Playwright MCP 启动浏览器并执行各步骤，然后报告结果。根据议题中的验收标准审查报告；如发现问题，可追问或让 Copilot 修复代码后再创建 PR。

> [!NOTE]
> 此测试要求应用在 `http://localhost:4321` 运行。如果已停止开发服务器，请重新启动后再发送提示。Copilot 首次使用 Playwright MCP 时可能需要下载浏览器；如果报告缺少浏览器，请让它运行 `npx playwright install chromium` 并重试。

[playwright-mcp-server]: https://github.com/microsoft/playwright-mcp

## 发布分支并创建拉取请求

确认功能正常后，发布 `filtering-vscode` 分支，以便团队审查：

1. 打开 Codespace 的 **Source Control** 面板并审查 Copilot 所做更改。
2. 选择 **+** 图标暂存更改。
3. 使用 **Sparkle** 按钮生成提交消息。

   ![显示 Copilot 更改的 Source Control 面板](../../../_images/ex3-source-control-changes.png)

4. 选择 **Publish** 将分支推送到存储库。

### 创建拉取请求

可以通过 github.com 或 GitHub 命令行接口创建 PR。这里让 Copilot 使用 GitHub 工具查找相关议题并创建关联的 PR：

1. 打开 Copilot Chat 面板，选择 **New Chat** 开始新会话。
2. 在智能体下拉菜单中选择 **Agent**，以便 Copilot 使用 GitHub 工具。
3. 使用以下提示：

   ```plaintext
   Find the issue in the repo related to filtering by category and publisher. Create a new pull request for the current branch, and associate it with the correct issue.
   ```

4. 根据需要选择 **Continue**，允许 Copilot 执行检索信息和操作所需的任务。首次使用 GitHub 工具时，可能会提示你登录 GitHub；按提示完成身份验证。
5. 查看 Copilot 如何搜索议题、找到正确议题并创建 PR。
6. 选择 Copilot 生成的链接审查 PR，但暂时**不要合并**。

## 总结和后续步骤

本练习中，你了解了 MCP 如何使用新工具扩展 Copilot，向工作区添加 Playwright MCP 服务器，在浏览器中手动测试筛选功能，并发布分支、创建筛选 PR。

MCP 只是扩展 Copilot 的一种方式。接下来，[创建自定义智能体][next-lesson]，简化无障碍审查等专注任务。

## 资源

- [MCP 是什么，为什么大家都在讨论它？][mcp-blog-post]
- [Microsoft Playwright MCP Server][playwright-mcp-server]
- [GitHub MCP Server][github-mcp-server]
- [GitHub MCP 注册表][mcp-registry]
- [VS Code 中的 MCP 服务器][vscode-mcp-config]

[previous-lesson]: ../2-agent-mode/
[next-lesson]: ../4-custom-agents/
[mcp-blog-post]: https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/
[github-mcp-server]: https://github.com/github/github-mcp-server
[mcp-registry]: https://github.com/mcp
[remote-github-mcp-server]: https://github.blog/changelog/2025-06-12-remote-github-mcp-server-is-now-available-in-public-preview/
[vscode-mcp-config]: https://code.visualstudio.com/docs/agents/reference/mcp-configuration

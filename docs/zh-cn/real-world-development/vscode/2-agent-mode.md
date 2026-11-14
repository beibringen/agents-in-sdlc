---
title: "练习 2 - 使用 Copilot 智能体模式添加功能"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

即使是简单的应用更新，通常也需要修改多个文件并运行测试。开发人员需要找到所有相关文件、完成更改、运行测试、调试、查找遗漏文件并再次修改，流程可能不断重复。

Copilot 智能体模式可以帮助处理这些工作。它能够在 IDE 中更自主地工作：先探索项目结构，再协调更新、运行测试，并自动修复发现的问题。接下来使用智能体模式为网站添加新功能。

> [!NOTE]
> 智能体模式和云智能体虽然名称相似，但适用于不同场景。智能体模式在 IDE 中执行任务，适合快速反馈和交互；云智能体则像团队成员一样异步工作，通过议题和拉取请求与你协作。

本练习将带你了解：

- Copilot 智能体模式如何探索项目、找到相关文件并协调更改。
- 如何使用智能体模式跨 UI 和数据层实现功能。
- 合并前如何审查 Copilot 生成的更改和测试。

## 场景

游戏列表不断扩大，用户需要按类别和发行商筛选。上一练习已添加发行商辅助函数；现在用 Copilot 智能体模式完成剩余的数据层、UI 和测试工作。

## 运行 Tailspin Toys 网站

更改前先探索网站，了解现有功能。Tailspin Toys 是以开发者为主题的桌游众筹平台，可浏览游戏列表和详情。网站是单个 Astro 应用，构建时将页面呈现为静态 HTML。页面通过 frontmatter 使用 Drizzle ORM 直接查询本地 SQLite 数据库，没有单独的后端 API 或客户端 UI 框架。可复用的数据访问辅助函数放在 `src/lib/`，交互功能由使用标准 DOM API 的精简 Astro `<script>` 实现。

### 启动网站

项目提供 `npm run dev` 任务，启动唯一的 Astro 开发服务器：

1. 返回上一练习创建的 Codespace。当前应处于 `main` 分支。
2. 按 <kbd>Control</kbd>+<kbd>`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>`</kbd>（Windows/Linux）打开终端。
3. 更新 `main` 并创建筛选功能分支：

   ```bash
   git checkout main
   git pull
   git checkout -b filtering-vscode
   ```

4. 启动网站：

   ```bash
   npm run dev
   ```

   Astro 开发服务器就绪后，会显示类似以下 URL 的横幅：

   ```bash
   🚀 Tailspin Toys is ready!
      Astro server: http://localhost:4321
      Press Ctrl-C to stop.
   ```

> [!NOTE]
> 如果弹出提示，询问是否打开 `http://localhost:4321` 的浏览器窗口，请选择 **x** 关闭。

5. 按住 <kbd>Command</kbd>（Mac）或 <kbd>Ctrl</kbd>（Windows/Linux），然后在终端中选择 Astro 服务器地址 `http://localhost:4321`。

> [!NOTE]
> 在 Codespace 中选择终端的 localhost 链接，会自动重定向到 `https://<your-codespace-name>-4321.app.github.dev/`。这是连接到 Codespace 网站的专用隧道。

### 浏览网站

网站启动后，可以浏览其功能：

- **主页**：显示桌游列表，包括标题、图片和描述。
- **游戏详情页**：选择游戏后会打开详情页，显示标题、描述、发行商和类别等信息。

## 使用 Copilot 探索待办事项

> [!TIP]
> **打开 Copilot Chat**
>
> 开始练习前，返回 Codespace，打开 Copilot Chat 面板并选择 **New Chat** 开始干净的新对话。模式和模型要求会在相关步骤中说明。

模板会自动创建一组待办议题。让 GitHub Copilot 查看这些项目并帮助确定优先级：

1. 在 Chat 视图的智能体下拉菜单中选择 **Agent**。该智能体可以自主规划并跨文件实现更改、运行终端命令和调用工具。

   ![Chat 视图中的智能体选择器](../../../_images/shared-chat-mode-selector.png)

2. 从可用模型列表中选择 **Claude Sonnet 4.5**。

> [!CAUTION]
> 本研讨会作者并未偏好某个模型。编写研讨会时使用的是 Claude Sonnet 4.5，因此将它列入步骤以保持体验一致。LLM 具有概率性，收到的建议可能与示例不同，这是正常现象。

> [!NOTE]
> 由于 LLM 具有概率性，Copilot 可能会使用不同的 MCP 命令，但仍应能完成任务。

3. 发送以下提示，查看 GitHub 存储库中的待办事项：

   ```plaintext
   Please show me the backlog of items from my GitHub repository. Help me prioritize them based on those which will be most useful to the user.
   ```

4. 选择 **Continue** 以运行列出所有议题的命令。
5. 审查生成的议题列表。

Copilot 还会根据它认为对用户最有用的程度为项目排序。

## 审查说明文件

让智能体生成代码前，先查看将用于提供上下文的说明文件。项目的 [UI 说明](https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md)介绍了如何为网站添加功能。

1. 在 Codespace 中打开 `.github/instructions/ui.instructions.md`。
2. 查看总体指导，包括架构概述、组件设计原则、可测试性和无障碍访问，以及指向 Astro、Tailwind CSS 和 Drizzle 说明的链接。

> [!TIP]
> 说明文件可以引用其他说明文件和项目文件。路径相对于说明文件所在位置，可用于复用内容、拆分复杂指导，并提供示例和模板。

## 实现筛选功能

完成筛选至少需要对应用进行三类更改：

- 在数据层 (`src/lib/`) 添加或完善筛选逻辑。
- 添加或更新筛选行为测试。
- 更新游戏列表页并加入筛选 UI。

合并前还必须运行并通过测试。Copilot 智能体模式可以为你完成这些任务：

1. 继续当前 Copilot 对话，或选择 **New Chat** 开始新对话。
2. 选择 **Add Context**、**Instructions** 和 **ui** 说明文件。

   ![选择 UI 说明文件](../../../_images/ex3-select-instructions-file.png)

3. 确认 Chat 视图的智能体下拉菜单仍选择 **Agent**。
4. 确认模型仍为 **Claude Sonnet 4.5**。
5. 使用与待办议题相关的以下提示请求实现功能：

   ```plaintext
   Please update the site to include filtering by publisher and category based on the requirements from the related GitHub issue in the backlog. A publishers helper already exists from the previous exercise, so preserve and refine that work as needed while completing the remaining data-layer, UI, and tests. Ensure all tests are passing before completion. The server is already running, so you do not need to start it up.
   ```

6. 观察 Copilot 如何探索项目并找到相关的数据层辅助函数、UI 和测试，然后修改文件并运行测试。

   ![Copilot 探索项目文件](../../../_images/ex3-agent-mode-explores.png)

> [!NOTE]
> Copilot 会探索项目、修改文件并运行测试。根据任务和代码库的复杂度，这可能需要几分钟。过程中编辑器可能会出现 **Keep** 和 **Undo** 按钮；工作进行时无需选择。完成后，每项更改都会有相应的 **Keep** 或 **Undo** 选项。

7. Copilot 提示运行测试时，选择 **Continue**。

   ![Copilot Chat 提示用户确认运行测试](../../../_images/ex3-agent-mode-run-tests.png)

8. 过程中可能会暂停，也可能有测试失败。这没有关系；Copilot 会在生成代码和运行测试之间迭代，直到完成任务且未检测到错误。
9. 检查生成的代码是否存在问题。

   ![已完成的 Copilot 智能体模式会话](../../../_images/ex3-agent-mode-proposed-changes.png)

> [!CAUTION]
> 始终审查 Copilot 或其他 AI 工具生成的代码。

10. 返回运行网站的浏览器并体验新功能。
11. 确认功能有效并审查代码后，在 Copilot Chat 窗口中选择 **Keep**。

## 总结和后续步骤

恭喜！你使用 GitHub Copilot 智能体模式为 Tailspin Toys 网站添加了功能。你了解了如何使用它跨 UI 和数据层实现功能、探索项目并协调更改，以及在合并前审查生成的更改和测试。

接下来，使用 [Playwright MCP 服务器测试功能][next-lesson]并为其创建拉取请求。

### 额外探索练习 - 实现分页

游戏列表增长后需要分页。使用本练习学到的技能，让 Copilot 更新网站以实现分页。考虑遵循现有最佳实践和说明文件、决定分页方式（允许用户选择页面大小或使用固定大小），并在提示中提供所需指导。你可能需要多次迭代、请求更改并补充上下文；这正是与 Copilot 协作的常见方式。

## 资源

- [Copilot ask、edit 和 agent 模式：功能及适用场景][choose-mode]
- [VS Code 中的智能体模式][vs-code-agent-mode]

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-mcp/
[choose-mode]: https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/
[vs-code-agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode

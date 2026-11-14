---
title: "练习 4 - 监控和管理智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

前两项练习中，你让 Copilot 云智能体执行了三项任务，以改善用户体验并添加功能。云智能体会异步自主工作，但监控任务仍然很重要。

你可以使用多种工具管理分配给云智能体的任务，包括 GitHub.com 上的[智能体页面][agents-page]。在此任务控制中心，可以查看所有有未关闭 PR 的智能体任务，检查执行情况，也可以在会话进行中引导智能体。

本课程将带你：

- 浏览智能体页面，监控云智能体任务。
- 在会话进行中引导智能体，要求添加其他功能。

## 场景

分配高对比度模式任务后，团队意识到也应添加浅色模式。由于智能体已经在更新网站样式并添加切换功能，将其纳入同一任务很合适。你将引导智能体同时添加浅色和高对比度模式。

## 查看 Copilot 云智能体任务

查看当前分配给 Copilot 云智能体的所有任务：

1. 打开 [Copilot 智能体页面](https://github.com/copilot/agents)。
2. 查看主区域和左侧区域中的任务列表。应能看到你分配给 Copilot 的任务，包括更新代码文档、生成游戏管理 API，以及为网站添加高对比度模式。
3. 选择一个正在运行的任务，查看 Copilot 执行的操作，例如检出存储库代码、创建工作环境、设置 MCP 服务器，以及执行任务所需的其他步骤。

> [!NOTE]
> 显示的具体步骤会因 Copilot 的工作状态和执行方案而异。

4. 另请查看右侧的拉取请求面板，其中显示 PR 和更改的文件，以便进一步监控。

## 引导云智能体

现在让 Copilot 在实现高对比度模式的同时，也添加浅色模式：

1. 选择创建高对比度模式的会话。确切标题可能因 Copilot 使用的名称和当前工作状态而异。

   ![任务控制中心中的无障碍会话](../../../_images/ex6-accessibility-session.png)

2. 观察会话几分钟，直到设置完成并开始工作。
3. 在 **Steer active session while Copilot is working** 对话框中输入以下提示：

   ```plaintext
   While we are working on a high contrast mode, let's also add a light mode. There should be a switch for this mode as well where users can select their desired display mode.
   ```

   ![智能体页面中的云智能体任务，突出显示 Steer active session while Copilot is working 对话框](../../../_images/ex6-steer-cloud-agent-task.png)

4. 按 <kbd>Enter</kbd> 发送提示。
5. 注意 Copilot 会确认收到提示，并将其纳入工作流程。

## 让 Copilot 继续工作

Copilot 会处理更新后的任务。收到消息时，它会先完成当前步骤，再将新请求加入工作流程。

这同样需要几分钟，可以借此回顾目前学到和探索的内容。

## 总结和后续步骤

本课程介绍了 Copilot 智能体页面，这是监控和引导 GitHub Copilot 云智能体任务的中心位置。你可以跟踪正在进行和已完成的任务、审查工作，也可以在任务进行中更改范围或补充指导。

你探索了如何使用任务控制中心和智能体页面监控任务，并如何重定向会话以请求添加功能。Copilot 正在完成无障碍功能，接下来将[迭代改进它创建的拉取请求][next-lesson]。

## 资源

- [Copilot 智能体页面][agents-page]
- [关于自定义智能体][custom-agents]

---

| [← 上一课：自定义智能体][previous-lesson] | [下一课：迭代改进 Copilot 的工作 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents

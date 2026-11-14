---
title: "练习 1 - 自定义说明（VS Code）"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 上一课：先决条件][previous-lesson] · [下一课：智能体模式 →][next-lesson]

使用生成式 AI 时，上下文至关重要。若任务需要以特定方式完成，或 Copilot 需要了解背景信息，就要确保它能访问相关上下文。[说明文件][instruction-files]是提供代码生成指导的主要方式。

本练习将带你：

- 了解项目上下文、编码指南和文档标准如何通过存储库自定义说明及按路径应用的说明文件提供给 Copilot。
- 在当前说明文件的约束下，使用 Copilot 生成筛选功能的第一个数据切片（发行商辅助函数）。
- 向 `.github/copilot-instructions.md` 添加存储库级标准。
- 再次运行相同提示，观察生成的代码如何应用新标准。
- 将说明更新和筛选功能切片提交到 `main`，让 Copilot 在下一练习中使用更新后的指导。

> [!CAUTION]
> 生成的代码可能不符合所有自定义标准，因为 Copilot 具有非确定性。本练习旨在观察更新说明后行为的整体变化趋势，而不是要求输出与示例逐字符相同。

## 说明文件

### 场景

Tailspin Toys 有一套开发实践指南和要求，包括：

- 数据层始终需要单元测试。
- UI 应采用暗色模式并具有现代外观。
- 使用 TSDoc 文档注释为代码添加文档。
- 在每个文件开头添加说明文件用途的注释块。

通过说明文件确保 Copilot 获得正确上下文，按这些实践完成任务。

### 自定义说明

自定义说明可为 Copilot 提供上下文和偏好，帮助它了解编码风格和要求。可以指定编码约定、库以及注释风格，从而引导它生成更合适的建议和代码片段。说明可以覆盖整个存储库，也可以针对特定任务或文件类型。

- `.github/copilot-instructions.md`：每次针对该存储库的请求都会发送此文件，适合存放技术栈、项目概述、最佳实践等项目级信息。
- `.github/instructions/*.instructions.md`：为特定任务或文件类型提供指导，例如 TypeScript、Astro、UI 组件和单元测试。

> [!NOTE]
> 在 IDE 中，说明文件仅用于 Copilot Chat 代码生成，不用于代码补全或下一步编辑建议。Copilot Chat、Copilot CLI 和 Copilot 云智能体在生成代码时会使用存储库级说明和带有 `applyTo` frontmatter 的 `*.instructions.md` 文件。Copilot 还支持 AGENTS.md 和 CLAUDE.md 等其他规范。

### 管理说明文件的最佳实践

- 让 `copilot-instructions.md` 专注于项目级指导，如项目简介、结构和全局编码标准。
- 使用 `*.instructions.md` 提供特定文件类型（单元测试、Astro 组件、数据层）或任务的指导。
- 使用清晰的自然语言，并提供代码的正反示例。

说明文件的编写方式不止一种，应通过实践找出适合项目的方法。

> [!TIP]
> GitHub Copilot 项目应有完善的说明文件集合。项目中已有适用于 [UI 更新][ui-instructions]和 [Astro][astro-instructions] 等任务的说明。Copilot 也可以帮助生成说明文件：VS Code 中可使用 **Configure Chat → Generate Agent Instructions**，Copilot CLI 中可使用 `/init`。如需模板和起点，请浏览 [awesome-copilot][awesome-copilot]。

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[astro-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/astro.instructions.md
[awesome-copilot]: https://github.com/github/awesome-copilot
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support

## 探索项目中的自定义说明文件

先查看存储库中的说明文件：核心 `copilot-instructions.md` 和一组适用于不同任务的 `*.instructions.md` 文件。

1. 打开 `.github/copilot-instructions.md`，查看项目简介以及 **Agent notes**、**Code standards**、**Scripts** 和 **Repository Structure** 等部分。**Code standards** 下还有 **GitHub Actions Workflows** 指导。
2. 打开 `.github/instructions` 文件夹，查看 Astro、Drizzle 数据层和测试等说明。
3. 打开 `.github/instructions/unit-tests.instructions.md`。顶部的 `applyTo` 设置相对于存储库根目录的 glob；例如匹配 `**/*.test.ts` 的 TypeScript 测试文件。
4. 查看本项目单元测试的具体说明。
5. 打开 `.github/instructions/drizzle.instructions.md` 并查看底部链接。它链接到其他说明文件（如 `unit-tests.instructions.md`）和项目现有文件；路径相对于说明文件，而不是存储库根目录。

> [!NOTE]
> `copilot-instructions.md` 中的 **Code formatting requirements** 部分尚未要求代码内文档。接下来将添加 TSDoc 文档注释和文件头注释规则。

## 为更改创建分支

1. 返回上一练习使用的 Codespace。
2. 按 <kbd>Control</kbd>+<kbd>`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>`</kbd>（Windows/Linux）打开终端。
3. 创建并切换到新分支：

   ```bash
   git checkout -b custom-instructions
   ```

## 更新说明前使用 Copilot Chat

> [!TIP]
> **打开 Copilot Chat**
>
> 开始练习前，返回 Codespace，打开 Copilot Chat 面板并选择 **New Chat** 开始干净的新对话。模式和模型会因练习而异；每个相关步骤都会说明选择要求。

先在当前说明下发送提示，观察 Copilot 的输出；更新说明后再发送相同提示进行比较。

1. 关闭之前练习留下的编辑器选项卡，避免 Copilot 获取不相关上下文。
2. 打开 `src/lib/publishers.ts`，让 Copilot 知道辅助函数应放在哪里。
3. 在 Chat 视图的智能体下拉菜单中选择 **Agent**，以便 Copilot 修改文件。

   ![Chat 视图中的智能体选择器](../../../_images/shared-chat-mode-selector.png)

4. 发送以下提示：

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

5. Copilot 会探索项目并更新代码，通常包括辅助函数和测试文件。
6. 注意生成的辅助函数是类型化函数，以 `db` 客户端为第一个参数，并返回类型化发行商数组。这来自适用于 `src/lib/*.ts` 的 `.github/instructions/drizzle.instructions.md` 数据层约定。
7. 注意生成的代码**缺少** TSDoc 文档注释和文件头注释。

> [!CAUTION]
> Copilot 具有非确定性，因此即使未明确要求，也可能添加文档注释。这没有问题；重点是观察更新说明后行为的一致性改进。

## 添加存储库级标准

在 `.github/copilot-instructions.md` 的 **Code formatting requirements** 部分添加规则，让 Copilot 提供更符合标准的代码：

1. 重新打开 `.github/copilot-instructions.md`。
2. 找到大约位于第 27 行的 **Code formatting requirements** 部分。这里还没有代码内文档标准。
3. 在现有标准下方添加：

   ```markdown
   - Every exported function should have a TSDoc comment describing its purpose, parameters, and return value.
   - Before imports or any code, add a comment block to the file that explains its purpose.
   ```

4. 保存文件。

> [!TIP]
> 存储库级说明文件适合放置项目通用标准；`*.instructions.md` 则适合提供特定语言、文件类型或任务的指导。

## 再次运行提示并观察变化

1. 返回 Copilot Chat，选择 **New Chat** 清除上下文。
2. 再次打开 `src/lib/publishers.ts`，使 Copilot 聚焦于正确的文件。
3. 再次发送前面使用的相同提示。
4. 观察建议的文件是否以类似以下注释块开头：

   ```typescript
   /**
    * Tailspin Toys 众筹平台的发行商数据访问辅助函数。
    * 提供从数据库检索发行商信息的函数。
    */
   ```

5. 观察建议的函数是否包含类似以下 TSDoc 注释：

   ```typescript
   /**
    * 返回所有发行商的 ID 和名称列表。
    *
    * @param db - Drizzle 数据库客户端。
    * @returns 解析为发行商对象数组的 Promise。
    */
   ```

你刚刚引导 Copilot 遵循新的项目标准，并将其应用于下一练习将继续完善的实际代码。

## 提交说明并推送分支

说明文件与存储库中的其他资源一样，需要通过源代码管理进行维护。

1. 按 <kbd>Control</kbd>+<kbd>`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>`</kbd>（Windows/Linux）打开终端。
2. 运行以下命令，确认说明和辅助函数更改已存在：

   ```bash
   git status
   ```

3. 暂存并提交说明更新及 Copilot 为辅助函数基础所做的所有更改：

   ```bash
   git add .github/copilot-instructions.md src/lib/publishers.ts
   # 如果 git status 显示其他配套更改（例如测试），也要添加这些文件。
   git commit -m "Add doc comment standards and publishers helper foundation"
   ```

4. 推送分支：

   ```bash
   git push -u origin custom-instructions
   ```

## 创建并合并拉取请求

将分支推送后，创建拉取请求并关联待办事项中的文档标准议题。Copilot 可以使用已连接到项目的 GitHub 工具查找议题、创建关闭该议题的 PR 并合并更改。

> [!NOTE]
> 这是第一次让 Copilot 代表你在 GitHub 上操作。项目模板已连接 GitHub 工具，因此无需设置。Copilot 首次使用时，VS Code 可能会提示你登录 GitHub。按提示授权即可。后续练习会介绍这种连接（Model Context Protocol）的工作方式。

1. 在 Codespace 中打开 Copilot Chat。
2. 按 <kbd>Control</kbd>+<kbd>Command</kbd>+<kbd>I</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>I</kbd>（Windows/Linux）打开 Copilot Chat 视图，确认已在智能体选择器中选择 **Agent**。
3. 让 Copilot 查找说明文件议题，并从当前分支创建 PR，说明其中既有说明更新也有筛选功能所需的发行商辅助函数基础：

   ```text
   Find the issue related to updating the instructions file. Create a new PR from the current branch, highlight that the PR closes that issue, and include that we also added the publishers helper foundation for the upcoming filtering work.
   ```

4. Copilot 会查找议题并创建 PR。
5. 当 Copilot 请求代表你执行 GitHub 操作时，检查命令并酌情选择 **Allow**。
6. PR 创建后，使用以下提示要求 Copilot 合并 PR 并将分支切回 `main`：

   ```text
   Merge the PR into main. Then return to main locally, and pull the latest code so we are up to date.
   ```

7. 当 Copilot 请求执行 GitHub 操作或 shell 命令时，先检查命令，再酌情选择 **Allow**。

现在已借助 GitHub Copilot 创建并合并拉取请求。

## 总结和后续步骤

你了解了 Copilot 如何获取项目说明文件中的上下文，并在 VS Code Copilot Chat 中：

- 发送代码生成提示，观察现有说明产生的结果。
- 向 `.github/copilot-instructions.md` 添加存储库级标准。
- 再次发送相同提示，观察建议的代码如何遵循新标准。
- 将说明更新和辅助函数基础提交到 `main`，为下一练习做准备。

接下来，在[智能体模式][next-lesson]中运用这些说明，让 Copilot 跨代码库添加新功能。

## 资源

- [GitHub Copilot 自定义说明文件][instruction-files]
- [创建自定义说明的最佳实践][instructions-best-practices]
- [编写更优 Copilot 自定义说明的 5 个技巧][copilot-instructions-five-tips]
- [GitHub Copilot 个人自定义说明][personal-instructions]
- [Awesome Copilot：说明文件集合][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-agent-mode/
[instruction-files]: https://code.visualstudio.com/docs/copilot/copilot-customization
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[personal-instructions]: https://docs.github.com/copilot/customizing-copilot/adding-personal-custom-instructions-for-github-copilot
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/

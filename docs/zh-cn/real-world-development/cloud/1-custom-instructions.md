---
title: "练习 1 - 自定义说明（云智能体）"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 上一课：先决条件][previous-lesson] · [下一课：Copilot 云智能体 →][next-lesson]

使用生成式 AI 时，上下文至关重要。如果任务必须以特定方式完成，或 Copilot 需要了解背景信息，就要确保它能访问这些上下文。[说明文件][instruction-files]用于提供此类指导，让 Copilot 不仅了解你想做什么，也了解希望如何完成。

本练习将带你：

- 了解项目上下文、编码指南和文档标准如何通过存储库自定义说明及按路径应用的说明文件提供给 Copilot。
- 向 `.github/copilot-instructions.md` 添加存储库级标准。

> [!NOTE]
> 与 VS Code 和 CLI 环境不同，这里不会通过前后对比提示来展示效果。Copilot 云智能体会异步处理 GitHub 议题，因此难以实时并排展示差异。后续练习中审查云智能体创建的拉取请求时，你会看到说明文件的影响。

## 说明文件

### 场景

Tailspin Toys 有一套开发实践指南和要求，包括：

- 数据层必须始终有单元测试。
- UI 应采用暗色模式并具有现代外观。
- 使用 TSDoc 文档注释为代码添加文档。
- 在每个文件开头添加说明文件用途的注释块。

使用说明文件可以让 Copilot 获得正确上下文，使其按照这些实践执行任务。

### 自定义说明

自定义说明为 Copilot 提供上下文和偏好，帮助它理解编码风格和要求。你可以指定编码约定、库以及注释风格，从而引导它生成更合适的建议和代码片段。说明可以覆盖整个存储库，也可以针对特定文件类型。

有两类说明文件：

- `.github/copilot-instructions.md`：每次针对该存储库的请求都会发送此文件。它应包含对大多数聊天或 CLI 请求都有用的项目级信息，例如技术栈、项目概述、最佳实践和全局指导。
- `.github/instructions/*.instructions.md`：针对特定任务或文件类型提供说明，例如 TypeScript、Astro、UI 组件或单元测试。

> [!NOTE]
> 在 IDE 中，说明文件仅用于 Copilot Chat 代码生成，不用于代码补全或下一步编辑建议。Copilot Chat、Copilot CLI 和 Copilot 云智能体在生成代码时会使用存储库级说明和带有 `applyTo` frontmatter 的 `*.instructions.md` 文件。Copilot 还支持其他规范的说明文件，例如 AGENTS.md 和 CLAUDE.md。

### 管理说明文件的最佳实践

- 让 `copilot-instructions.md` 专注于项目级指导，例如项目内容、结构和全局编码标准。
- 使用 `*.instructions.md` 提供特定文件类型或任务（如单元测试、Astro 组件和数据层）的说明。
- 使用自然语言，确保指导清晰，并提供正反示例。

说明文件没有唯一的编写方式，AI 的使用方式也不止一种。通过实践找出最适合项目的方法。

> [!TIP]
> 每个使用 GitHub Copilot 的项目都应有一组完善的说明文件。项目中包含适用于 [UI 更新][ui-instructions]和 [Astro][astro-instructions] 等任务的说明。Copilot 也可以帮助生成说明文件：VS Code 中可使用 **Configure Chat → Generate Agent Instructions**，Copilot CLI 中可使用 `/init`。如需模板和起点，请浏览包含说明文件、自定义智能体和其他资源的 [awesome-copilot][awesome-copilot]。

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[astro-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/astro.instructions.md
[awesome-copilot]: https://github.com/github/awesome-copilot
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support

## 探索项目中的自定义说明文件

存储库包含一个核心 `copilot-instructions.md` 和多个适用于不同任务的 `*.instructions.md` 文件。可在编辑器或 GitHub 网页界面中打开这些文件。

1. 打开 `.github/copilot-instructions.md`，查看项目简介及 **Agent notes**、**Code standards**、**Scripts** 和 **Repository Structure** 等部分。**Code standards** 中还有 **GitHub Actions Workflows** 子部分；这些内容适用于 Copilot 在存储库中的所有交互。
2. 打开 `.github/instructions` 文件夹，查看 Astro、Drizzle 数据层、测试等说明。
3. 打开 `.github/instructions/unit-tests.instructions.md`。顶部的 `applyTo` 字段指定适用文件的 glob；例如匹配 `**/*.test.ts` 的 TypeScript 测试文件。
4. 查看该文件中针对本项目单元测试的说明。
5. 打开 `.github/instructions/drizzle.instructions.md` 并查看底部链接。它链接到其他说明文件（如 `unit-tests.instructions.md`）和项目现有文件。这样可以拆分较大的说明集并提供示例；其中路径相对于说明文件所在位置，而不是存储库根目录。

> [!NOTE]
> `copilot-instructions.md` 中的 **Code formatting requirements** 部分介绍了项目编码标准，但尚未要求代码内文档。接下来将添加 TSDoc 文档注释和文件头注释规则。

## 添加存储库级标准

现在添加一条项目级规则，要求代码使用 TSDoc 文档注释和文件头注释。后续练习中，云智能体处理分配给它的议题时会读取此规则。

先创建分支：

1. 在 Codespace 终端中创建并切换到新分支：

   ```bash
   git checkout -b custom-instructions-cloud
   ```

2. 重新打开 `.github/copilot-instructions.md`，找到大约位于第 27 行的 **Code formatting requirements** 部分。当前没有代码内文档规则。
3. 在现有标准下方添加以下 Markdown：

   ```markdown
   - Every exported function should have a TSDoc comment describing its purpose, parameters, and return value.
   - Before imports or any code, add a comment block to the file that explains its purpose.
   ```

4. 保存 `copilot-instructions.md`。

> [!TIP]
> 说明文件可以放在存储库级别（`.github/copilot-instructions.md`）以提供全局指导，也可以使用 `*.instructions.md` 针对语言、文件类型或任务提供指导。存储库级文件适合放置 TSDoc 等项目范围的标准。

## 提交、推送并合并说明更新

云智能体会读取议题目标分支中的说明。下一练习中，Copilot 将从 `main` 分支创建分支，因此必须先将说明更改合并到 `main`。

1. 暂存并提交更改：

   ```bash
   git add .github/copilot-instructions.md
   git commit -m "Add doc comment and file-header standards to copilot instructions"
   ```

2. 推送分支：

   ```bash
   git push -u origin custom-instructions-cloud
   ```

3. 在 github.com 上创建从 `custom-instructions-cloud` 合并到 `main` 的拉取请求并合并。后续练习中，云智能体就会读取这些说明。

> [!TIP]
> 如果希望直接在 `main` 上完成本研讨会，可以跳过分支步骤并直接提交到 `main`。此处采用分支，是为了模拟真实项目的工作方式。

## 总结和后续步骤

你已了解 Copilot 如何获取本项目说明文件中的上下文，并在 `.github/copilot-instructions.md` 中添加了存储库级标准。后续练习中，可以在云智能体创建的拉取请求中看到该标准的效果。

接下来，将第一个议题[分配给 Copilot 云智能体][next-lesson]。

## 资源

- [GitHub Copilot 自定义说明文件][instruction-files]
- [创建自定义说明的最佳实践][instructions-best-practices]
- [编写更优 Copilot 自定义说明的 5 个技巧][copilot-instructions-five-tips]
- [Awesome Copilot：说明文件集合][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-cloud-agent/
[instruction-files]: https://docs.github.com/copilot/customizing-copilot/about-customizing-github-copilot-chat-responses
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/

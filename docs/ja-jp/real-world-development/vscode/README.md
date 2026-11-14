---
slug: ja-jp/real-world-development/vscode
title: "Visual Studio Code"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

VS Code の **[GitHub Copilot Chat](https://code.visualstudio.com/docs/copilot/chat/copilot-chat)** を使うと、使い慣れたコードエディターで GitHub Copilot を利用できます。Visual Studio Code と GitHub Codespaces で Copilot Chat のエージェントモードを使い、MCP で外部ツールに接続し、カスタムエージェントを活用します。IDE を離れずに、ファイル、ターミナル、問題をすべて Copilot に参照させながら作業できます。

まずカスタム指示を追加し、Copilot が指示に従う様子を確認します。次にエージェントモードで UI、データレイヤー、テストにわたるフィルター機能を構築します。続いて Playwright MCP サーバーに接続し、Copilot にブラウザーを操作させて機能をテストしてから pull request を作成します。最後にアクセシビリティ用のカスタムエージェントを確認して使用し、エディター内で Copilot の変更を監視、調整、改善します。

## 演習

| 演習 | トピック | 説明 |
|----------|-------|-------------|
| [0. 前提条件][ex0] | セットアップ | リポジトリと codespace を作成します |
| [1. カスタム指示][ex1] | コンテキスト | VS Code でカスタム指示を追加して検証します |
| [2. エージェントモード][ex2] | コード生成 | エージェントモードでフィルター機能を構築します |
| [3. Playwright MCP][ex3] | 外部ツール | Playwright MCP サーバーを使ってブラウザーで機能をテストします |
| [4. カスタムエージェント][ex4] | 専門エージェント | カスタムエージェントを確認して使用します |
| [5. エージェントの管理][ex5] | 監視 | エージェントセッションを監視し、方向を調整します |
| [6. 繰り返し改善する][ex6] | レビュー | Copilot の作業をローカルで確認し、次の手順を選びます |
| [オプション: Foundry を組み込む][foundry-toolkit] | AI エージェント | VS Code と Foundry Toolkit でモデルを準備し、エージェントをデプロイしてサイトに接続するまで、3つのモジュールで進めます |

## 前提条件

このワークショップに参加する前に、次のものを用意してください。

- [ ] 有効な **Copilot Student、Pro、Pro+、Business、Enterprise** のいずれかのプランがある GitHub アカウント
- [ ] GitHub Codespaces へのアクセス

> [!TIP]
> 有料プランを利用していない場合、認証済みの学生は [GitHub Education][callout-student-plan-education] を通じて GitHub Copilot を無料で利用できます。**Copilot Student** プランには、このワークショップで使用するエージェント、MCP、コードレビュー、Copilot CLI の機能が含まれているため、すべてのハーネスを完了できます。

[callout-student-plan-education]: https://github.com/education/students
## はじめる

**[演習 0: 前提条件から始める →][ex0]**

[ex0]: 0-prerequisites/
[ex1]: 1-custom-instructions/
[ex2]: 2-agent-mode/
[ex3]: 3-mcp/
[ex4]: 4-custom-agents/
[ex5]: 5-managing-agents/
[ex6]: 6-iterating/
[foundry-toolkit]: 7-foundry-toolkit/

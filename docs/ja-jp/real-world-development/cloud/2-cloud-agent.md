---
title: "演習 2 - GitHub Copilot cloud agent"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 前のレッスン: カスタム指示][previous-lesson] |
|:--|

多くの組織が技術的負債に悩まされています。未解決のセキュリティ課題、更新が必要なレガシーコード、実装する時間がなくバックログに残った機能リクエストなどがあります。GitHub Copilot cloud agent は、コードの更新や機能の追加などのタスクを自律的に実行できます。作業が終わると、人間の開発者がレビューできるドラフト PR を作成します。面倒な作業を任せて開発を加速し、より重要な作業に集中できます。

Copilot cloud agent を使って、次の内容を学びます。

- コード生成環境のカスタマイズ
- 操作を安全に実行する方法
- Issue のスコープを明確にする重要性
- Issue の Copilot への割り当て

## シナリオ

Tailspin Toys には解決したい技術的負債があります。最初の Web サイトを構築した委託先が、ドキュメントを十分に作成していません。まず、アプリケーションのすべてのエクスポート関数に TSDoc doc comment を追加します。

また、デザインチームはゲームを見つけやすくしたいと考えています。各ゲームの詳細ページに、同じカテゴリーのほかのゲームを表示したいとのことです。まだ完成度の高い実装は必要なく、UX の受け入れテストに使えるものを求めています。この作業は現在ブロッカーですが、優先度の高い Issue がほかにあります。

どちらも後回しになりやすいタスクです。Copilot cloud agent に割り当てれば、開発者が別の作業をしている間に非同期で処理し、あとで結果をレビューできます。

## GitHub Copilot cloud agent について

[GitHub Copilot cloud agent](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent) は、人間の開発者と同じようにバックグラウンドでタスクを実行できます。たとえば、[GitHub Issue を Copilot に割り当てる][assign-issue]方法があります。割り当てると、Copilot は進捗を追跡するドラフト pull request を作成し、環境を準備してタスクに取りかかります。実行中でも完了後でもセッションを確認できます。提案された解決策のレビュー準備ができると、pull request で通知されます。

## 適切なスコープの指示が重要

GitHub Copilot に魔法はありません。考える作業を省略できるわけではありません。一見簡単な操作でも、詳しく見ると複雑なことがあります。[Copilot cloud agent のタスクのスコープを適切に設定][cloud-agent-best-practices]してください。AI ペアプログラマーとして扱い、段階的に進め、学び、試し、必要に応じて調整します。生成 AI を加えても、ソフトウェア開発の基本は変わりません。

## このリポジトリのカスタム指示

前の演習では、カスタム指示とその働きを確認しました。カスタム指示を実際に操作したことがあれば、`.github/copilot-instructions.md` が機能することを確認済みです。まだの場合は、ここで簡単に確認しましょう。Copilot cloud agent に作業を割り当てる前に、このリポジトリに含まれる指示ファイルを読み取り専用で確認し、後でその影響を確認できるようにします。

GitHub Web UI または起動済みの codespace で、次のファイルを開きます。

- `.github/copilot-instructions.md` - **Code standards** セクション、特に TypeScript の規約と TSDoc doc comment の要件を確認します。
- `.github/instructions/unit-tests.instructions.md` - `applyTo` フロントマターで `**/*.test.ts` ファイルに指示が適用されることを確認します。

次のセクションで **Code lacks documentation** Issue を cloud agent に割り当てたら、作成された pull request で TSDoc doc comment、TypeScript の規約、コメントヘッダーを確認してください。後の演習で PR をレビューするときに、改めて説明します。

## Copilot cloud agent 用の開発環境を準備する

誰がコードを書く場合でも、通常は特定の環境とセットアップスクリプトが必要です。Copilot にタスクを割り当てる場合も同様で、Copilot は SWE と同じように作業します。

Cloud agent は作業環境として [GitHub Actions][github-actions] を使います。`.github/workflows/copilot-setup-steps.yml` に設定した[専用のセットアップワークフロー][setup-workflow]を作成すると、作業開始前に実行される環境をカスタマイズできます。必要な開発ツールや依存関係を使用できるようになります。このラボでは、学習を進めやすいように事前設定済みです。単一の Astro アプリであるプロジェクトに必要な Node.js、依存関係、E2E テスト用のブラウザーバイナリ、マイグレーションとシードを適用した SQLite データベースが準備されます。

```yaml
name: "Copilot Setup Steps"

# リポジトリの Actions タブからセットアップ手順をテストできます
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # 必要な手順に対して最低限の権限を設定します。Copilot は操作用の独自トークンを受け取ります。
    permissions:
      # 依存関係のインストールなどでセットアップ手順内からリポジトリをクローンする場合は、contents: read 権限が必要です。手順内でクローンしない場合は、手順の完了後に Copilot が自動でクローンします。
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # フロントエンド / アプリのセットアップ - Node.js (アプリ全体は Astro + Drizzle/libSQL)
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

      # ビルドやテストでデータを利用できるよう、ローカル SQLite データベースをマイグレーションしてシードします。
      - name: Set up the database
        run: npm run db:setup
```

ほかの GitHub Actions ワークフローと同じように見えますが、いくつか重要な点があります。

- `copilot-setup-steps` という単一のジョブがあります。Copilot が pull request の作業を始める前に、GitHub Actions で実行されます。
- `workflow_dispatch` トリガーを使うと、リポジトリの **Actions** タブから手動でワークフローを実行できます。Copilot の実行を待たずに、正常に動くかテストできます。

## ドキュメントを追加する

ドキュメントが重要なのは誰もが理解していますが、多くのプロジェクトでは情報が古かったり、ドキュメント自体がなかったりします。この種の技術的負債は放置されがちで、生産性を下げ、コードベースの保守や新しい開発者の参加を難しくします。Copilot はドキュメントの作成を得意とするため、Copilot cloud agent に割り当てるのに適した Issue です。バックグラウンドで必要なドキュメントを作成し、後の演習で作業をレビューします。

1. 新しいブラウザータブで github.com のリポジトリを開きます。
2. **Issues** タブを選択します。
3. **New issue** を選択して、新しい Issue ダイアログを開きます。
4. **Blank issue** を選択します。
5. **Title** に `Code lacks documentation` と入力します。
6. **Description** に次の内容を入力します。

    ```plaintext
    関数とメソッドの動作を明確にするため、必要に応じて TSDoc doc comment を含めることが組織の要件です。残念ながら、最近の更新はこの標準に従っていません。動作を説明する必要がある箇所に doc comment が含まれるよう、既存のコードを更新してください。
    ```

7. **Create** を選択して Issue を作成します。
8. 右側にある **Assign to Copilot** を選択し、割り当てダイアログを開きます。

  ![Issue に Copilot を割り当てる画面](../../../_images/shared-assign-copilot.png)

9. **Assign** を選択します。
10. **Pull Requests** タブを選択します。
11. 新しく作成された pull request (PR) を開きます。タイトルは `[WIP]: Code lacks documentation` のようなものです。一覧に PR が表示されない場合は、しばらく待ってからブラウザーを更新してください。
12. 数分後、Copilot が todo リストを作成したことを確認できます。

> [!NOTE]
> Copilot の todo リストが PR に表示されるまで数分かかることがあります。Copilot は環境を作成し (前述のワークフローを実行します)、プロジェクトを分析して、問題を解決する方法を決めています。

13. todo リストと、実行予定のタスクを確認します。
14. pull request のタイムラインを下にスクロールし、Copilot が Issue の作業を開始したことを示す更新を確認します。
15. **View session** ボタンを選択します。

  ![Copilot のセッションビュー](../../../_images/ex4-view-session.png)

> [!CAUTION]
> 更新されたインジケーターを表示するには、ウィンドウの再読み込みが必要な場合があります。

16. ライブセッションをスクロールし、Copilot が問題をどのように解決しているか確認します。コードを調査して状態を把握し、計画を考えて判断し、コードを作成する様子を確認できます。

作業には数分かかることがあります。Copilot cloud agent の大きな目的の1つは、非同期でタスクを処理し、別の作業に集中できるようにすることです。この機能を活用し、もう1つのタスクを Copilot cloud agent に割り当ててから、アプリケーションに機能を追加する作業を進めます。

## ゲーム詳細ページに関連ゲームのセクションを追加する

デザインチーム向けに関連ゲームのセクションを追加する作業は、時間がかからないかもしれません。それでも、Copilot cloud agent に任せれば、その時間をほかの作業に使えます。さっそくタスクを割り当てましょう。

1. github.com のリポジトリに戻ります。
2. **Issues** タブを選択します。
3. **New issue** を選択して、新しい Issue ダイアログを開きます。
4. 空のテンプレートを使うには **Blank issue** を選択します。
5. **Title** に `Show related games on the game details page` と入力します。
6. **Description** に次の内容を入力します。

    ```markdown
    ゲームの詳細ページに関連ゲームを表示して、ほかのゲームを見つけやすくしたいと考えています。デザインチームは UX を確認し、受け入れテストを行いたいと考えています。要件は次のとおりです。

   - 指定されたゲームを除き、同じカテゴリーのほかのゲームを返すデータアクセスヘルパーを `src/lib/` に追加する
   - ヘルパーを使って、ゲーム詳細ページに「関連ゲーム」セクションを表示する
   - 関連ゲームがない場合に対応する
   - 新しいヘルパーのユニットテストを作成する
   - PR を作成する前に、すべてのテストが成功することを確認する
    ```

7. **Create** を選択して Issue を作成します。
8. 右側の **Assign to Copilot** を選択し、割り当てダイアログを開きます。

  ![Issue に Copilot を割り当てる画面](../../../_images/shared-assign-copilot.png)

9. **Assign** を選択します。

まもなく Issue の最初のコメントに 👀 が表示され、Copilot が作業を開始したことがわかります。

![Issue に対する作業中を示す目の絵文字を使う Copilot](../../../_images/ex4-issue-eyes-emoji.png)

Copilot は2つ目の依頼にも取り組んでいます。Copilot cloud agent は SWE と同じように動作するため、常に監視する必要はありません。完了後にレビューできます。次は、カスタムエージェントの作成と使用を確認します。

## まとめと次のステップ

[GitHub Copilot cloud agent][copilot-agents] を確認しました。cloud agent に Issue を割り当てると、非同期で作業を行います。技術的負債への対応、新機能の作成、別のフレームワークへのコード移行などに利用できます。

次の概念を学びました。

- コード生成環境のカスタマイズ
- 操作を安全に実行する方法
- Issue のスコープを明確にする重要性
- Issue の Copilot への割り当て

cloud agent がバックグラウンドで作業を進めている間に、次はカスタムエージェントを作成して使用します。[Copilot cloud agent は MCP サーバーも使用でき][cloud-agent-mcp]、前のモジュールで確認したカスタム指示も利用できます。

## リソース

- [Copilot cloud agent について][copilot-agents]
- [GitHub Issue を Copilot に割り当てる][assign-issue]
- [Copilot cloud agent のセットアップワークフローのベストプラクティス][cloud-agent-best-practices]

| [次のレッスン: カスタムエージェント →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions

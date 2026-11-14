---
title: "演習 4 - エージェントの監視と管理"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

ここまでの演習では、ユーザー体験の改善と機能追加を目的に、Copilot cloud agent に3つのタスクを依頼しました。cloud agent は非同期かつ自律的に作業しますが、タスクを監視できることも重要です。

cloud agent に割り当てたタスクを管理する方法はいくつかあります。[GitHub.com のエージェントページ][agents-page]もその1つです。この mission control では、オープンな pull request (PR) があるすべてのエージェントタスクを確認できます。実行内容を調べ、進行中のセッションに追加の指示を送ることもできます。

このレッスンでは、次の作業を行います。

- エージェントページを開き、cloud agent のタスクを監視します。
- 進行中のセッションに指示を送り、追加機能を依頼します。

## シナリオ

エージェントにハイコントラストモードを依頼した後、チームはライトモードも追加したいと考えました。サイトのスタイル更新と切り替え機能をすでに作業中なので、これも含めるのが合理的です。エージェントに指示を送り、ハイコントラストモードとライトモードの両方を追加してもらいます。

## Copilot cloud agent のタスクを確認する

Copilot cloud agent に割り当てたすべてのタスクの現在の状態を確認します。

1. [Copilot agents ページ](https://github.com/copilot/agents)を開きます。
2. メインペインと左ペインの両方に表示されるタスクの一覧を確認します。次のようなタスクが表示されます。
    - コードベースのドキュメントの更新
    - ゲーム変更用 API の生成
    - Web サイトへのハイコントラストモードの追加
3. 実行中のタスクを選択し、Copilot が実行した作業を確認します。次のような内容が表示されます。
    - リポジトリからのコードのチェックアウト
    - Copilot の作業環境の作成
    - MCP サーバーのセットアップ
    - タスクを完了するために実行された手順

> [!NOTE]
> 表示される手順は、Copilot の作業状況や実行方法によって異なります。

4. 右側に表示される pull request (PR) ペインも確認します。PR と変更されたファイルが表示されるため、さらに監視できます。

## cloud agent に追加の指示を送る

アクティブなタスクを確認できたので、cloud agent がハイコントラストモードを作業している間に、ライトモードの切り替えも追加するよう依頼します。

1. ハイコントラストモードを追加するセッションを選択します。正確なタイトルは Copilot が付けた名前や作業状態によって異なります。

    ![mission control の Accessibility セッション](../../../_images/ex6-accessibility-session.png)

2. セットアップが完了し、作業を開始したことがわかるまで数分間セッションを確認します。次のようなメッセージが表示されたら作業開始の目印です。
3. **Steer active session while Copilot is working** ダイアログに次のプロンプトを入力します。

    ```plaintext
    ハイコントラストモードを作成するのに合わせて、ライトモードも追加してください。ユーザーが希望する表示モードを選べるよう、ライトモードの切り替えも追加してください。
    ```

    ![Copilot が作業中のセッションに指示を送るダイアログを強調表示した、agents ページの画面](../../../_images/ex6-steer-cloud-agent-task.png)

4. <kbd>Enter</kbd> を押してプロンプトを送信します。
5. Copilot がプロンプトを確認し、作業に取り込むことを確認します。

## Copilot の作業を続ける

前回と同じように、Copilot は更新されたタスクに取りかかります。メッセージを送信した時点で実行していた手順が終わると、新しい依頼が作業に組み込まれます。

作業には数分かかります。その間に、ここまでに学んだことを振り返りましょう。

## まとめと次のステップ

Copilot cloud agent のタスクを監視し、ガイドする中心的な場所である Copilot agents ページを確認しました。この mission control を使うと、アクティブなタスクと完了済みタスクを追跡し、実行中の作業をレビューし、進行中のタスクの範囲を変更したり追加の指示を送ったりできます。

次の概念を学びました。

- mission control とエージェントページで cloud agent のタスクを監視する方法
- 進行中のセッションに指示を送り、追加機能を依頼する方法

Copilot がアクセシビリティ機能の作業を完了したら、次は [Copilot が作成した pull request を繰り返し改善します][next-lesson]。

## リソース

- [Copilot agents ページ][agents-page]
- [カスタムエージェントについて][custom-agents]

---

| [← 前のレッスン: カスタムエージェント][previous-lesson] | [次のレッスン: Copilot の作業を繰り返し改善する →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents

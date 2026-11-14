---
title: "レッスン 8 - 機能の PR の作成とマージ"
description: "フィルター機能、指示、スキルの更新、QA プロファイル、テストをまとめてレビューし、PR を作成して Agent Merge を使用します。"
authors:
  - geektrainer
lastUpdated: 2026-10-22
---

フィルター機能の実装、指示の更新、スキルの更新、品質保証 (QA) プロファイル、テストを1つのブランチに保存しました。すべてまとめてレビューし、pull request を作成します。星評価の pull request (PR) は自分でマージしましたが、今回は **Agent Merge** にプロセスの管理を任せます。

> [!NOTE]
> 通常は、機能、指示の更新、スキルの更新、QA エージェントを複数の PR に分けます。ワークショップを簡潔にするため、フィルター機能と品質ワークフロー全体を1つのセッションとブランチで進め、この PR にまとめています。

このレッスンでは、次の作業を行います。

- Agent Merge と、マージのライフサイクルを自動化する方法を学びます。
- 機能の PR 全体と検証の根拠を確認します。
- レビュー後にのみ Agent Merge を許可し、PR がマージされたことを確認します。

## シナリオ

フィルター機能のワークフロー全体を通じて、Copilot を使って機能を計画、実装、検証しました。Tailspin Toys は、マージの許可を開発者の管理下に置きながら、残りの PR 作業を自動化したいと考えています。

## Agent Merge について

**Agent Merge** は、GitHub Copilot app で pull request を取り込むための残りの作業を自動化します。有効にすると、アプリのセッションが pull request を読み取り、失敗した継続的インテグレーション (CI) チェックの修正、レビューコメントへの対応、必要に応じたリベースなど、マージを妨げる問題に対処し、GitHub が許可するとマージします。バックグラウンドで実行され、アプリを再起動しても継続し、pull request がマージされると自動的に停止します。

これまでは自分で **Merge pull request** を選択していました。Agent Merge に任せることもできますが、コードの編集やマージには明示的な許可が必要です。マージを許可する前に、許可される操作と作業内容をレビューしてください。

## Agent Merge で PR を管理する

コードの作成とレビューが完了したら、Agent Merge に PR のプロセスを任せます。

1. エージェントピッカーで **Default agent** を選択します。
2. **Create PR** の横にあるドロップダウンを選択します。
3. **Agent Merge** を選択します。ボタンが **Agent Merge** に変わります。
4. **Agent Merge** ボタンを選択してプロセスを開始します。ドロップダウンの項目を選んだだけでは、プロセスは始まりません。

Agent Merge のプロセスが始まり、次の作業を行います。

- タイトルと説明を含む pull request を作成します。
- Issue からセッションを開始した場合は、説明の本文で関連する Issue を参照します。
- ターゲットブランチに対してリベースを行うか、マージ競合に対処します。
- CI を監視し、すべてのチェックが成功することを確認します。
- 他の開発者または Copilot code review からのフィードバックがないか PR を監視し、コメントに対応する更新を行います。
- すべての処理が成功した後、必要に応じて PR を自動的にマージします。

すべてのチェックに成功した後は、Agent Merge に PR のマージも任せます。

5. マージを許可する前に、PR の差分と検証の根拠をレビューします。
6. **Agent Merge** の横にあるドロップダウンを選択します。
7. **Merge pull request** の横にチェックが付いていることを確認します。
8. エージェントに明示的にマージを依頼します。

    ```plaintext
    Merge the PR once all required checks and approvals pass. Do not bypass repository protections.
    ```

9. 進捗を確認し、報告されたブロッカーを解決します。続行前に PR の状態が **Merged** であることを確認します。マージの許可を設定したことやエージェントが実行中であることは、マージ完了の確認にはなりません。

> [!IMPORTANT]
> Agent Merge は、リポジトリの保護や権限不足を回避しません。続行前にそれらのブロッカーを解消してください。

## まとめと次のステップ

コードの生成、テストと検証、pull request のプロセスなど、開発プロセスの複数の部分を自動化しました。具体的には、次の作業を行いました。

- Agent Merge と、マージのライフサイクルを自動化する方法を学びました。
- 機能の PR 全体と検証の根拠を確認しました。
- レビュー後にのみ Agent Merge を許可し、PR がマージされたことを確認しました。

次は、[既存のキャンバスを使い、トリアージキャンバスを作成します][next-lesson]。エージェントと一緒に作業を確認、計画、視覚化する方法を学びます。

## リソース

- [GitHub Copilot app での Issue と pull request の管理][managing-issues-prs]
- [GitHub Copilot app について][about-copilot-app]

[next-lesson]: ../9-canvases/
[managing-issues-prs]: https://docs.github.com/copilot/how-tos/github-copilot-app/managing-issues-and-pull-requests
[about-copilot-app]: https://docs.github.com/copilot/concepts/agents/github-copilot-app

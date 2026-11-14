---
title: "レッスン 12 - GitHub Pages でサイトをデプロイする"
description: "GitHub Copilot app を使って Pages ワークフローを設定し、pull request をレビューして、公開された Space Quiz を確認します。"
authors:
  - jamesmontemagno
lastUpdated: 2026-10-12
---

Space Quiz を GitHub Pages にデプロイし、公開しましょう。Copilot はワークフローを準備してデプロイをガイドできますが、変更をレビューし、サイトが公開されたことを確認するのはユーザーの役割です。

このレッスンでは、次の作業を行います。

- Copilot に GitHub Pages のデプロイを設定させます。
- ワークフローと pull request をレビューしてからマージします。
- デプロイの状態を確認し、公開サイトを検証します。

## シナリオ

Space Quiz はすでに GitHub リポジトリにありますが、手軽に試す方法が必要です。GitHub Pages を使って単一ファイルのサイトを公開すると、サーバーや実行時依存関係を追加せず、公開 URL から利用できるようになります。

## Copilot にデプロイを準備させる

> [!WARNING]
> pull request の作成により、コードがコミットおよびプッシュされることがあります。このプロンプトを送る前に、承認設定と送信先のリポジトリを確認してください。**Approve all** が有効な場合、別の確認プロンプトは表示されないことがあります。

`space-quiz` の Copilot app セッションで、次のプロンプトを送信します。

```plaintext
Prepare a GitHub Pages deployment for this repository. Create a GitHub Actions workflow that builds the website if needed and deploys it to GitHub Pages after changes reach the default branch. Add a README describing the app and how to run it locally if one does not already exist. Create a pull request with these changes and report its link and any required Pages settings or permissions. Do not merge, enable auto-merge, change Pages settings, or deploy. Stop and wait for my review.
```

## ワークフローと pull request をレビューする

1. README はリポジトリにまだ存在しない場合にのみ追加されていることを確認します。
2. ビルドとデプロイの手順、トリガー、権限など、ワークフローを含む pull request の変更をレビューします。
3. ワークフローが未マージの pull request ではなく、既定のブランチからデプロイすることを確認します。
4. 必要な修正を依頼して更新された差分をレビューします。マージを許可する前に、必須の pull request チェックが成功するまで待ちます。

## マージとデプロイを許可する

> [!WARNING]
> 次のプロンプトを送ると、レビュー済み pull request のマージ、リポジトリの Pages 設定の変更、サイトの公開をエージェントに許可します。レビューを完了し、リポジトリと pull request が正しいことを確認してから送信してください。承認設定によっては、別の確認プロンプトなしで操作が実行されます。

`<PR_NUMBER>` をレビューした pull request の番号に置き換え、別のプロンプトとして送信します。

```plaintext
I have reviewed pull request <PR_NUMBER> in this repository and authorize its merge and deployment. Enable GitHub Pages using GitHub Actions, merge that pull request only after required checks pass, and monitor the deployment until it succeeds. Report the final URL of the live website. If you lack permission to enable Pages or merge, tell me exactly what I need to approve or do; do not report success until the site is live.
```

![Space Quiz の pull request がマージされた状態、Build website・Upload artifact・Deploy to Pages の各手順が成功した GitHub Actions の Deploy website 実行、および yourname.github.io/space-quiz/ で公開された Space Quiz の3段階を示す図](../../../_images/first-steps-app-pages-deploy.svg)

## デプロイを確認する

1. GitHub Actions のデプロイ実行が成功するまで進捗を確認します。
2. 報告された URL を開き、Space Quiz が表示されることを確認します。

エージェントが Pages の有効化や pull request のマージを行えない場合は、具体的な指示に従います。デプロイが成功し、公開サイトが読み込まれるまで完了と判断しないでください。

## まとめと次のステップ

Space Quiz が公開され、エージェントの報告だけに頼らず、デプロイを確認しました。[レッスン 13: レビューと次のステップ][next-lesson]に進んでください。

[next-lesson]: ../13-review/

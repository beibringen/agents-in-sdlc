---
title: "演習 2 - Copilot エージェントモードで機能を追加する"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

アプリケーションの簡単な更新でも、通常は複数のファイルを変更し、テストを実行するなどの作業が必要です。開発者は、必要なファイルを探し、変更を行い、テストを実行し、問題を修正し、見落としたファイルに気付いて再度更新する、といった作業を繰り返します。

このような作業に役立つのが Copilot エージェントモードです。

Copilot エージェントモードは、IDE 内でより自律的に作業できるように設計されています。開発者と同様に、既存のプロジェクト構造を調べ、必要な更新を行い、テストなどのタスクを実行し、見つかった問題を自動的に修正します。ここでは、エージェントモードでサイトに機能を追加する方法を確認します。

> [!NOTE]
> 名前は似ていますが、エージェントモードと cloud agent は異なる用途向けに作られています。エージェントモードは IDE 内でタスクを実行し、すばやいフィードバックとやり取りを可能にします。Cloud agent はチームメンバーのように非同期で作業し、Issue と pull request を通じてやり取りします。

この演習では、次の内容を学びます。

- Copilot エージェントモードがプロジェクトを調べ、関連ファイルを特定し、複数のファイルを連携して変更する方法
- GitHub Copilot エージェントモードが UI とデータレイヤーにわたって機能を実装する方法
- コードベースにマージする前に、Copilot エージェントモードが生成した変更とテストをレビューする方法

## シナリオ

ゲームの一覧が増えてきたため、カテゴリーとパブリッシャーで絞り込めるようにします。前の演習では publishers ヘルパーを追加しました。ここでは、Copilot エージェントモードを使って、残りのデータレイヤー、UI、テストの作業を完了します。

## Tailspin Toys Web サイトを起動する

変更を加える前に、Tailspin Toys Web サイトの現在の機能を確認します。

この Web サイトは、開発者をテーマにしたボードゲームのクラウドファンディングプラットフォームです。ゲームを一覧表示し、詳細を表示できます。単一の Astro アプリであり、ページはビルド時に静的 HTML として生成されます。ページはフロントマターで Drizzle ORM を使ってローカルの SQLite データベースを直接参照します。独立したバックエンド API やクライアントサイドの UI フレームワークはありません。再利用可能なデータアクセスヘルパーは `src/lib/` に置き、インタラクティブな機能は標準 DOM API を使う小さな Astro `<script>` で追加します。

### Web サイトを起動する

単一の Astro 開発サーバーを起動する `npm run dev` タスクが用意されています。GitHub Codespace で次の手順を実行します。

1. codespace に戻ります。前の演習で main に戻っているはずです。
2. <kbd>Control</kbd>+<kbd>`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux) を押して、Codespace 内に新しいターミナルウィンドウを開きます。
3. フィルター機能の作業用に、新しいブランチを作成して切り替えます。

   ```bash
   git checkout main
   git pull
   git checkout -b filtering-vscode
   ```

4. 次のコマンドを実行して Web サイトを起動します。

   ```bash
   npm run dev
   ```

   Astro の開発サーバーが起動すると、次のような URL を示すメッセージが表示されます。

   ```bash
   🚀 Tailspin Toys is ready!
      Astro server: http://localhost:4321
      Press Ctrl-C to stop.
   ```

> [!NOTE]
> `http://localhost:4321` を開くよう促すダイアログが表示された場合は、**x** を選択して閉じます。

5. <kbd>Command</kbd> (Mac) または <kbd>Ctrl</kbd> (Windows/Linux) を押しながら、ターミナルに表示された Astro サーバーのアドレス `http://localhost:4321` を選択して Web サイトを開きます。

> [!NOTE]
> Codespace のターミナルで localhost の URL を選択すると、自動的に `https://<your-codespace-name>-4321.app.github.dev/` にリダイレクトされます。これは Web サーバーを実行している codespace へのプライベートトンネルです。

### Web サイトを確認する

Web サイトが起動したら、機能を確認します。主な機能は次のとおりです。

- **ホームページ**: タイトル、画像、説明とともにボードゲームを一覧表示します。
- **ゲーム詳細ページ**: ゲームを選択すると、タイトル、説明、パブリッシャー、カテゴリーなどの情報を表示する詳細ページが開きます。

## Copilot でバックログを確認する

> [!TIP]
> **Copilot Chat を開く**
>
> 次の演習を始める前に codespace に戻り、Copilot Chat パネルを開きます。**New Chat** を選択して、新しい会話を始めてください。モードとモデルの選択は演習によって異なります。必要な場合は、各手順で説明します。

Web サイトの最初の実装は動作しますが、新しい機能を追加して改善します。まずバックログを確認します。テンプレートからプロジェクトを作成したときに、Issue のバックログも自動的に作成されています。GitHub Copilot に一覧を表示するよう依頼します。

1. Chat ビューのエージェントドロップダウンから **Agent** を選択します。**Agent** は複数ファイルにわたる変更の計画と実装、ターミナルコマンドやツールの実行を自律的に行います。

   ![Chat ビューのエージェント選択を示す画面](../../../_images/shared-chat-mode-selector.png)

2. 使用可能なモデル一覧から **Claude Sonnet 4.5** を選択します。

> [!CAUTION]
> このワークショップの作成者が特定のモデルを推奨する意図はありません。ワークショップを作成したときに Claude Sonnet 4.5 を使用したため、手順にも記載しています。提案の一貫性を高め、学習しやすくすることを目的としています。ただし、LLM は確率的に動作するため、受け取る提案がワークショップの例と異なる場合があります。これは通常の動作であり、想定されています。

> [!NOTE]
> LLM の確率的な性質により、Copilot が別の MCP コマンドを使う場合がありますが、タスクは完了できるはずです。

3. 次のプロンプトを Copilot に送信し、Issue のバックログを確認します。

   ```plaintext
   Please show me the backlog of items from my GitHub repository. Help me prioritize them based on those which will be most useful to the user.
   ```

4. すべての Issue を一覧表示するコマンドを実行するには **Continue** を選択します。
5. Copilot が生成した Issue の一覧を確認します。

Copilot がユーザーに最も役立つと思われる項目を優先順位付けしたことも確認できます。

## 指示ファイルを確認する

Copilot にコードを生成させる前に、作業のコンテキストを与える指示ファイルを確認しましょう。[ユーザーインターフェイス (UI) 指示ファイル](https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md)には、Web サイトへの機能追加方法に関するコンテキストが記載されています。

1. codespace で `.github/instructions/ui.instructions.md` を開きます。
2. 機能追加の進め方に関する全体的なガイダンスを確認します。次の内容が含まれます。
   - アーキテクチャの概要
   - コンポーネントの設計、テスト可能性、アクセシビリティの原則
   - Astro、Tailwind CSS、Drizzle など、ファイル種別に固有の指示へのリンク

> [!TIP]
> 指示ファイルでは、ほかの指示ファイルやプロジェクト内のファイルを参照できます。パスは指示ファイルからの相対パスです。指示を再利用し、大きなファイルを小さく分け、例やテンプレートを示すことができます。

## フィルター機能を実装する

フィルター機能を完成させるには、少なくとも次の3種類の更新が必要です。

- データレイヤー (`src/lib/`) にフィルター処理を追加または調整する
- フィルターの動作をテストするテストを追加または更新する
- ゲーム一覧ページにフィルター UI を追加する

さらに、コードベースにマージする前にテストを実行し、成功させる必要があります。Copilot エージェントモードに作業を依頼しましょう。

1. Copilot との現在の会話を続けるか、**New Chat** を選択して新しい会話を始めます。
2. **Add Context**、**Instructions**、**ui** の順に選択して、指示ファイルを追加します。

   ![UI 指示ファイルを選択する例を示す画面](../../../_images/ex3-select-instructions-file.png)

3. Chat ビューのエージェントドロップダウンで **Agent** が選択されていることを確認します。

   ![Chat ビューのエージェント選択を示す画面](../../../_images/shared-chat-mode-selector.png)

4. モデルに **Claude Sonnet 4.5** が選択されていることを確認します。
5. 関連するバックログ Issue の要件に基づいて機能を実装するよう、次のプロンプトで依頼します。

   ```plaintext
   Please update the site to include filtering by publisher and category based on the requirements from the related GitHub issue in the backlog. A publishers helper already exists from the previous exercise, so preserve and refine that work as needed while completing the remaining data-layer, UI, and tests. Ensure all tests are passing before completion. The server is already running, so you do not need to start it up.
   ```

6. Copilot がプロジェクトを調べ、必要なファイルを探す様子を確認します。データレイヤー、UI、テストを見つけ、ファイルの変更とテストの実行を開始します。

   ![プロジェクトファイルを確認する Copilot の画面](../../../_images/ex3-agent-mode-explores.png)

> [!NOTE]
> Copilot はプロジェクトの調査、ファイルの変更、テストの実行などを行います。タスクやコードベースの複雑さによっては、完了まで数分かかります。作業中、コードエディターに **Keep** と **Undo** ボタンが表示されることがあります。Copilot が完了すると、変更ごとに **Keep** または **Undo** が表示されます。作業中に選択する必要はありません。

7. Copilot から求められたら **Continue** を選択してテストを実行します。

   ![Copilot Chat でテスト実行の確認を求めるダイアログ](../../../_images/ex3-agent-mode-run-tests.png)

8. 作業中に待機が発生したり、テストが失敗したりすることがあります。問題ありません。Copilot はタスクを完了してエラーが見つからなくなるまで、コード生成とテストを繰り返します。

   ![Copilot エージェントモードの完了した Chat セッション](../../../_images/ex3-agent-mode-proposed-changes.png)

9. 生成されたコードに問題がないか確認します。

> [!CAUTION]
> Copilot などの AI ツールが生成したコードは、必ずレビューしてください。

10. Web サイトを表示しているブラウザーに戻り、新機能を確認します。
11. 動作とコードを確認したら、Copilot Chat ウィンドウで **Keep** を選択します。

## まとめと次のステップ

GitHub Copilot エージェントモードを使い、Tailspin Toys Web サイトに機能を追加する方法を確認しました。次の内容を学びました。

- GitHub Copilot エージェントモードで UI とデータレイヤー全体に新機能を実装する方法
- Copilot エージェントモードがプロジェクトを調べ、関連ファイルを特定し、複数のファイルを連携して変更する方法
- コードベースにマージする前に、Copilot エージェントモードが生成した変更とテストをレビューする方法

次は [Playwright MCP サーバーで機能をテストし][next-lesson]、pull request を作成します。

### おまけの演習 — ページ分割を実装する

ゲーム一覧が増えるにつれてページ分割が必要になります。この演習で学んだ方法を使い、Copilot に Web サイトのページ分割機能の実装を依頼してください。次の点を検討します。

- 既存の指示ファイルなど、現在のベストプラクティスに従います。
- ページサイズをユーザーが選べるようにするか、値を固定するかなど、ページ分割の方法を検討します。
- 希望する実装に必要なガイダンスをプロンプトに含めます。
- GitHub Copilot に変更やコンテキストを伝え、繰り返し改善することがあります。これは Copilot を使う通常の作業方法です。

## リソース

- [Copilot の Ask、Edit、Agent モード: それぞれの役割と使い分け][choose-mode]
- [VS Code のエージェントモード][vs-code-agent-mode]

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-mcp/
[choose-mode]: https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/
[vs-code-agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode

# GitHub Actions 学習リポジトリ

GitHub Actionsの基本的な概念と実用的なパターンを学ぶためのリポジトリです。

## 📚 概要

このリポジトリでは、GitHub Actionsの以下の機能について、動作するサンプルと詳細なドキュメントを提供しています:

- ジョブ間でのデータ受け渡し (outputs)
- 環境変数の使い方
- アーティファクトの管理
- 条件分岐 (if)
- キャッシュ戦略
- イベントのフィルタリング

## 🚀 ワークフローサンプル

### [hello.yml](.github/workflows/hello.yml)

実行中のメインワークフロー。以下の3つのジョブで構成されています:

```yaml
jobs:
  say-hello:      # ジョブ間でデータを受け渡す例
  read-greeting:  # 他ジョブの出力を受け取る例
  echo-env-val:   # 環境変数を使う例
```

**学べること:**
- `$GITHUB_OUTPUT` を使ったジョブ出力の設定方法
- `needs` を使った他ジョブの出力の参照方法
- `env` を使った環境変数の定義と参照

### [old-hello.yml](.github/workflows/old-hello.yml)

アーティファクトの使い方を学ぶサンプル（現在は無効化されています）

**学べること:**
- `actions/upload-artifact@v4` でファイルをアップロード
- `actions/download-artifact@v4` でファイルをダウンロード
- ジョブ間でのファイル受け渡し

## 📖 ドキュメント

各トピックについて、実務で必要な知識を詳しく解説しています。

### [fetch-depth.md](docs/fetch-depth.md)
Gitの履歴をどこまで取得するかを制御する設定について

**重要なポイント:**
- デフォルトは `1` (最新コミットのみ)
- CI高速化の鍵
- ただし `git diff` や changelog生成には `fetch-depth: 0` が必要

### [types.md](docs/types.md)
Pull Requestイベントのフィルタリング

**重要なポイント:**
- `types: [opened, synchronize]` でコード変更時のみCI実行
- CI無駄実行の削減
- デフォルトでは全PRイベントで発火してしまう

### [if.md](docs/if.md)
条件分岐によるステップ・ジョブの制御

**重要なポイント:**
- `if: github.event_name == 'push'` で特定イベントのみ実行
- `failure()`, `success()`, `cancelled()`, `always()` 関数
- スキップは失敗ではない (`skipped ≠ failed`)

### [cache.md](docs/cache.md)
npm/yarn/pnpmのキャッシュ戦略

**重要なポイント:**
- `cache: npm` で60〜180秒 → 5〜15秒に高速化
- `node_modules` ではなく `~/.npm` をキャッシュ
- lockfileのハッシュで自動無効化
- GitHub側のストレージに7日間保存

### [external-uses.md](docs/external-uses.md)
外部アクションの使い方と調べ方

**重要なポイント:**
- `uses: actions/setup-node@v4` の仕組み
- `@v4` はメジャーバージョン固定（推奨）
- GitHub Marketplace / action.yml で仕様確認

## 💡 クイックリファレンス

### ジョブ出力の設定と参照

```yaml
jobs:
  job1:
    outputs:
      result: ${{ steps.set-output.outputs.value }}
    steps:
      - id: set-output
        run: echo "value=hello" >> $GITHUB_OUTPUT

  job2:
    needs: job1
    steps:
      - run: echo "${{ needs.job1.outputs.result }}"
```

### 環境変数の使用

```yaml
jobs:
  job1:
    env:
      MY_VAR: my-value
    steps:
      - run: echo "Value is $MY_VAR"
```

### キャッシュの設定

```yaml
- uses: actions/setup-node@v4
  with:
    node-version: 20
    cache: npm  # 60〜180秒 → 5〜15秒に高速化
```

### イベントフィルタリング

```yaml
on:
  pull_request:
    types: [opened, synchronize]  # コード変更時のみ実行
```

### 条件分岐

```yaml
steps:
  - name: Only on push
    if: github.event_name == 'push'
    run: echo "This runs only on push"

  - name: On failure
    if: failure()
    run: echo "Previous step failed"
```

## 🎯 実務で役立つパターン

### CI高速化の基本セット

```yaml
- uses: actions/checkout@v4
  with:
    fetch-depth: 1  # 最新コミットのみ (デフォルト)

- uses: actions/setup-node@v4
  with:
    node-version: 20
    cache: npm  # キャッシュ有効化
```

### PRの無駄実行を防ぐ

```yaml
on:
  pull_request:
    types: [opened, synchronize, ready_for_review]
    # ラベル付与やコメントでは実行されない
```

### テスト失敗時のみログ保存

```yaml
- name: Run tests
  run: npm test

- name: Upload logs on failure
  if: failure()
  uses: actions/upload-artifact@v4
  with:
    name: test-logs
    path: logs/
```

## 🔧 開発のヒント

### ワークフローをローカルでテスト

[act](https://github.com/nektos/act) を使うとローカルでGitHub Actionsを実行できます

```bash
# インストール (macOS)
brew install act

# ワークフローを実行
act push
```

### デバッグログの有効化

リポジトリのSecretsに以下を追加:
- `ACTIONS_STEP_DEBUG`: `true`
- `ACTIONS_RUNNER_DEBUG`: `true`

## 📝 参考リンク

- [GitHub Actions 公式ドキュメント](https://docs.github.com/ja/actions)
- [ワークフロー構文](https://docs.github.com/ja/actions/using-workflows/workflow-syntax-for-github-actions)
- [GitHub Actions Marketplace](https://github.com/marketplace?type=actions)

## 📄 ライセンス

このリポジトリは学習目的で作成されています。自由に参照・改変してください。

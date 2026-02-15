# アクションとは

アクションは、GitHub Actions における再利用可能な処理の単位です。アクションは、頻繁に必要となる処理をパッケージ化したもので、ワークフロー内のステップから呼び出して使用することができます。

アクションは以下のような形式でワークフロー内から呼び出します。

```yaml
steps:
  - uses: actions/checkout@v4
    with:
      repository: "myorg/myrepo"
      ref: "main"
```

この例では、`actions/checkout` というアクションを使用しています。`@v4` はアクションのバージョンを指定しており、`with` 以下でアクションに渡すパラメータを定義しています。

## アクションの主要なタイプ

アクションには三つの主要なタイプがあります。

### JavaScript アクション
Node.js を使用して実装されたアクションです。以下は使用例です。

```yaml
steps:
  - uses: actions/setup-node@v4
    with:
      node-version: "14"
```

### Docker コンテナアクション
Docker コンテナ内で実行されるアクションです。

```yaml
steps:
  - uses: docker://alpine:3.8
    with:
      args: "echo hello"
```

### コンポジットアクション
複数のステップをまとめて一つのアクションとして定義したものです。

```yaml
steps:
  - uses: actions/setup-java@v3
    with:
      distribution: "temurin"
      java-version: "17"
```

## GitHub Marketplace

アクションは GitHub Marketplace で公開・共有することができます。GitHub Marketplace には、以下のようなカテゴリーの多数のアクションが公開されています。

- **継続的インテグレーション用のアクション**：ビルド、テスト、デプロイなど。
- **セキュリティ関連のアクション**：コードスキャン、脆弱性チェックなど。
- **プロジェクト管理用のアクション**：イシュー管理、通知など。
- **開発ツール連携用のアクション**：IDE やプロジェクト管理ツールとの連携など。

## バージョン管理

アクションを使用する際は、バージョン管理に注意が必要です。アクションのバージョンは以下の方法で指定できます。

```yaml
steps:
  # メジャーバージョンの指定
  - uses: actions/checkout@v4

  # 完全なバージョンの指定
  - uses: actions/checkout@v4.1.0

  # コミットSHAの指定
  - uses: actions/checkout@a81bbbf8298c0fa03ea29cdc473d45769f953675
```

## 入出力の使用

アクションはワークフローファイル内で入力パラメータを受け取ることができ、出力を生成することもできます。以下は入出力を使用する例です。

```yaml
steps:
  - id: random
    uses: actions/generate-random@v1
    with:
      length: 10

  - run: echo ${{ steps.random.outputs.number }}
```

このように、アクションを使用することで、共通の処理を簡単に再利用でき、ワークフローの記述を大幅に簡略化することができます。

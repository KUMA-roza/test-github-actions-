```yaml
- uses: actions/setup-node@v4
```

これは

> **GitHub 上の別リポジトリにある Action を呼び出している**

という意味です。
（in **GitHub Actions**）

---

# どこから来てるのか

書式はこれ

```
uses: <owner>/<repo>@<version>
```

つまり

```
actions/setup-node@v4
↑      ↑
owner  repository
```

= GitHub のこのリポジトリ

👉 [https://github.com/actions/setup-node](https://github.com/actions/setup-node)

---

# 調べる方法（3つ）

## ① ブラウザで直接開く（最速）

```
https://github.com/<owner>/<repo>
```

今回なら

```
https://github.com/actions/setup-node
```

README に使い方・inputs・examples が全部書いてあります。

---

## ② Marketplace から調べる（おすすめ）

GitHub の Actions タブ → Marketplace → 検索
「setup-node」

ここ
→ [https://github.com/marketplace/actions/setup-node-js-environment](https://github.com/marketplace/actions/setup-node-js-environment)

特徴：

* オプション一覧が見やすい
* usage例がまとまってる

---

## ③ action.yml を読む（本質）

リポジトリ内の

```
action.yml
```

ここに定義が全部あります。

例（抜粋イメージ）：

```yaml
inputs:
  node-version:
    description: 'Node.js version'
  cache:
    description: 'Used to specify a package manager for caching'
```

👉 ここが **仕様書そのもの**

---

# @v4 の意味

```yaml
@v4
```

これはタグ（バージョン固定）

| 書き方     | 意味         |
| ------- | ---------- |
| @v4     | メジャー固定（安全） |
| @v4.0.2 | 完全固定       |
| @main   | 最新（危険）     |

CIでは基本 `vX` を使います

---

# まとめ

`uses:` は

> GitHub上の公開アクションを import して実行している

調べ方は

1. GitHub repo 開く
2. Marketplace 見る
3. action.yml 読む ← 最重要

---

必要なら「自作アクションの作り方」も解説できるよ（理解一気に深まる）

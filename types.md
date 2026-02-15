**Pull Request のイベントでよく使う**やつ。

---

# 例

```yaml
on:
  pull_request:
    types: [opened, synchronize, ready_for_review, reopened]
```

👉 「PR が起きた」だけじゃなく
**どんな操作で起きたか** を限定しています。

---

# 各 `types` の意味

| type               | いつ発火する？           | 具体例         |
| ------------------ | ----------------- | ----------- |
| `opened`           | PRが作成された          | 新しくPRを出した   |
| `synchronize`      | PRのブランチにpush      | 追加コミットを積んだ  |
| `ready_for_review` | Draft → Ready に変更 | WIP解除       |
| `reopened`         | Close → Reopen    | 再度レビューしてほしい |

---

# 実際の挙動

PRのライフサイクルはこんな感じ：

```
PR作る(opened)
  ↓
commit追加(synchronize)
  ↓
Draft解除(ready_for_review)
  ↓
close
  ↓
reopen(reopened)
```

上の4つのタイミングでだけ CI が走ります。

---

# 逆に実行されない操作

例えばこれらでは動きません

| 操作         | 動く？ |
| ---------- | --- |
| ラベル付与      | ❌   |
| assignee変更 | ❌   |
| コメント       | ❌   |
| レビュー投稿     | ❌   |

（types を書かなければ全部で発火）

---

# なぜ使うの？

CIの無駄実行を減らすため

例えばレビューコメントで毎回CI走るのは無駄なので止める：

```yaml
on:
  pull_request:
    types: [opened, synchronize]
```

→ 「コードが変わったときだけテスト」

---

# 超重要ポイント

`pull_request` は **めちゃくちゃイベントが多い**

types を書かないと

> PRのあらゆる操作でCIが走る

だから実務ではほぼ必ず制限します。

---

必要なら `pull_request` と `pull_request_target` の違いも説明できるよ
（セキュリティでめちゃ重要）

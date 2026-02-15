GitHub Actions の `if:` は **その step / job を実行するかどうかの条件式** です。
（bash の if ではなく、Actions 独自の “式エンジン”）

---

# まずこの例

```yaml
steps:
  - name: Step 1
    if: github.event_name == 'push'
    run: echo "This step runs only on push events"

  - name: Step 2
    if: failure()
    run: echo "This step runs only if previous step failed"
```

---

## Step 1 の意味

```yaml
if: github.event_name == 'push'
```

### `github.event_name`

ワークフローを起動したイベント名が入ります

例：

| イベント              | 値                   |
| ----------------- | ------------------- |
| push              | `push`              |
| pull_request      | `pull_request`      |
| workflow_dispatch | `workflow_dispatch` |
| schedule          | `schedule`          |

つまり

👉 **push のときだけ Step1 を実行**

---

## Step 2 の意味

```yaml
if: failure()
```

これは特殊関数です。

### `failure()`

「それまでの step のどれかが失敗していたら true」

つまり

👉 **前の step が失敗した時だけ実行**

---

# 実行フロー

### push の場合

```
Step1 実行
  └ 成功 → Step2 は実行されない
  └ 失敗 → Step2 実行
```

### pull_request の場合

```
Step1 スキップ
Step2 も実行されない（失敗してないから）
```

---

# よく使う条件関数

めちゃ重要なのでまとめ

| 関数            | 意味             |
| ------------- | -------------- |
| `success()`   | ここまで全部成功       |
| `failure()`   | どこか失敗          |
| `cancelled()` | キャンセルされた       |
| `always()`    | 必ず実行（cleanup用） |

---

## 例：テスト失敗時だけログ保存

```yaml
- run: npm test

- name: upload log
  if: failure()
  run: cat test.log
```

---

# ポイント

**if は step を「スキップ」するだけ**
失敗扱いにはなりません

つまり

```
skipped ≠ failed
```

CI設計でかなり重要な違い。

---


# `cancelled()` 
**ワークフロー（または job）が “途中で中断された状態” になった時に true になる条件関数** です。

失敗でも成功でもなく
👉 **「人間 or システムが止めた」状態**

---

# どういう時に Cancel になるの？

代表例：

| 状況                                 | 結果        |
| ---------------------------------- | --------- |
| UI の「Cancel workflow」ボタンを押した       | cancelled |
| 新しい push で古い CI を自動停止（concurrency） | cancelled |
| タイムアウト                             | cancelled |
| 親 job が停止して依存 job が止まった            | cancelled |

つまり
**エラーではないけど最後まで走ってない**

---

# 重要：failure() とは別物

| 状態    | success() | failure() | cancelled() |
| ----- | --------- | --------- | ----------- |
| 成功    | true      | false     | false       |
| テスト失敗 | false     | true      | false       |
| 手動停止  | false     | false     | true        |

---

# よくある使い道

## 後処理（クリーンアップ）

途中停止でもリソースは消したい

```yaml
- name: stop preview server
  if: cancelled()
  run: docker compose down
```

---

## always() と組み合わせる（実務で多い）

```yaml
- name: upload logs
  if: failure() || cancelled()
  run: cat logs.txt
```

→ 失敗でも中断でもログ回収

---

# 典型的な実務パターン（超重要）

CI の無駄実行を止める設定

```yaml
concurrency:
  group: ci-${{ github.ref }}
  cancel-in-progress: true
```

新しい push が来る
↓
古い CI が **cancelled**
↓
`cancelled()` の cleanup が走る

---

# まとめ

`cancelled()` =

> 「失敗ではないが、完走していない」状態を検知する条件

CI を「安全に止める」ためのフックです。

---

必要なら
`always()` との使い分けを図で整理するよ（ここ混乱しがち）

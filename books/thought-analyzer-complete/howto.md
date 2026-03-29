---
title: "使い方・継続活用・record_id管理"
---

## 起動方法

### 思考パターン分析（9軸）

Claude.aiで以下のいずれかを入力する：

```
思考パターンを分析して
ログを分析して
thought analyze
analyze my thinking
analyze this log
```

### コーディング指示力分析（6軸）

```
コーディング指示力を分析して
技術指示を分析して
analyze my coding direction
analyze my technical direction
```

### 現在の会話 vs 外部ログ

**現在のセッション分析：**「この会話を分析して」と言えば、現在のセッションのやり取りを対象に分析する。

**外部ログ分析：**過去の会話ログをコピーして貼り付ける。skillが自動的にユーザー発言のみを抽出して分析する。

---

## 分析前のデータ準備

### 何を貼り付けるか

**含めるべきもの：**
- 過去のClaude.ai・ChatGPT・その他AIとの会話ログ
- エンジニアとのテキストコミュニケーション（Slack・GitHub等）
- 自分の発言が含まれているもの

**含めなくてよいもの：**
- AIの返答のみの部分（skillが自動除外する）
- コードブロックの内容（skillが自動除外する）
- URLや固有名詞（skillが自動除外する）

### ログの量と質

- **最低限**：30件以上のユーザー発言
- **推奨**：100件以上・複数の文脈（作業・概念設計・質問）を含む
- **高精度**：100件以上・複数の日付にわたるセッション

単一の長い会話より、複数の異なるセッションの組み合わせの方が習慣的パターンを正確に測定できる。

---

## 時系列追跡：トークンとrecord_id

### なぜトークンを設定するか

分析結果をデータベースに送信する際、**時系列追跡トークン**を設定できる。

トークンを設定すると：
- 次回以降の分析結果が同一人物として照合される
- 3ヶ月後・6ヶ月後の自分との比較が可能になる
- 「この軸が伸びた」「この軸が変わっていない」という差分が見えてくる

### トークンの設計

- **自分だけが知る任意の文字列**を入力する（例：ペットの名前+生まれた年）
- サーバーにはハッシュ値のみが保存される。元の文字列は復元できない
- 忘れると照合できなくなるため、手元に控えておく

### record_idとは

送信が成功すると `record_id`（UUID形式）が返される。

```
record_id：xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

record_idは：
- そのデータの一意の識別子
- 後から「このデータを取り消したい」場合の照合に使える
- 将来的に比較機能が実装されたとき、このデータを紐付けるために使う

**record_idは手元に控えておくことを強く推奨する。**

---

## 継続的な活用法

### ステップ1：ベースラインを作る

最初の分析を「現時点のベースライン」として記録する。

```
analyzed_at: 2026-03
fingerprint: { ... }
record_id: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

この時点でのフィンガープリントを保存しておく（ローカルファイル・Notionなど）。

### ステップ2：定期的に再分析する

目安は3ヶ月〜6ヶ月ごと。同じトークンで送信することで時系列照合が可能になる。

再分析のタイミングの目安：
- 新しいプロジェクトを始めた後
- 新しい技術領域に入った後
- AIとの協働スタイルを意識的に変えようとした後

### ステップ3：差分を読む

| 変化のパターン | 読み取れること |
|--------------|----------------|
| 軸の値が変わった | そのパターンが変化した（成長 or 文脈移行） |
| 軸の値が変わらない | そのパターンが安定している（強みとして定着 or 変えにくい癖） |
| `low_confidence` が消えた | データ量が増えて信頼度が上がった |
| `integrative_complexity` のスコアが上がった | 思考の構造的複雑さが増した |

---

## 9軸と6軸を組み合わせて読む実践例

### ケース1：構想は豊かだが実現が詰まる人

```json
// 9軸
"abstraction_direction": "stays_abstract",
"integrative_complexity": 6,
"concept_distance": { "distance": "far", "count": 4 }

// 6軸
"specification_precision": 2,
"iteration_style": "batch_vague"
```

**読み取り：** 構想力は高い（抽象的・複雑な思考・遠い概念の接続）が、それをAIに伝える具体化が弱い。発想を構造化して伝える訓練が有効。

---

### ケース2：実装は速いが同じ壁に当たり続ける人

```json
// 9軸
"problem_style": "fix",
"epistemic_curiosity": "deprivation_type",
"integrative_complexity": 3

// 6軸
"specification_precision": 4,
"decision_quality": "routine",
"system_abstraction": "component"
```

**読み取り：** 指示力は高く、エラーは修正できる（fix + 高精度な要件定義）。しかし既知パターンへの依存が強く（routine + IC低め）、新しい問題の構造的理解が弱い。コンポーネントの先のアーキテクチャレベルに視野を広げることが次のステップ。

---

### ケース3：思考は柔軟だがAI協働が不安定な人

```json
// 9軸
"problem_style": "pivot",
"abstraction_direction": "abstract_to_concrete",
"need_for_cognition": "high"

// 6軸
"decision_quality": "deferred",
"error_recognition": "behavioral",
"iteration_style": "incremental_vague"
```

**読み取り：** 思考スタイルは発散的・探索的だが、技術的判断を委ねる傾向がある。AIへの指示は頻繁だが曖昧になりやすい。構想力をそのまま指示に変換する精度（specification_precision）を上げることが鍵。

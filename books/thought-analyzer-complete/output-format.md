---
title: "出力フォーマット・全フィールド解説"
---

## 2つのJSONスキーマ

thought-analyzerは2つの独立したskillを持つ。それぞれが異なるJSONスキーマで出力する。

| skill | スキーマバージョン | 主要フィールド |
|-------|-----------------|--------------|
| 思考パターン分析 | `2.0` | `fingerprint`（9軸）+ `commentary` |
| コーディング指示力分析 | `1.0` | `coding_direction`（6軸）+ `commentary` |

---

## 思考パターン分析の完全JSONフォーマット

```json
{
  "schema_version": "2.0",
  "analyzed_at": "YYYY-MM",
  "message_count": 87,
  "fingerprint": {
    "abstraction_direction": "concrete_to_abstract",
    "problem_style": "fix",
    "perspective_taking": "spontaneous",
    "face_strategy": {
      "value": "moderate",
      "score": 0.62
    },
    "concept_distance": {
      "bridges": ["技術 × 倫理", "経済 × 感情"],
      "distance": "far",
      "count": 2
    },
    "evaluation_framing": "gain_first",
    "need_for_cognition": "high",
    "integrative_complexity": 5,
    "epistemic_curiosity": "deprivation_type"
  },
  "commentary": {
    "summary": "120字以内の全体像",
    "holistic_profile": "軸の言葉を使わず、思考の癖・傾向を200字以内で自然に記述",
    "strengths": ["強みとして読める軸と理由（構造的根拠のみ）"],
    "blind_spots": ["盲点として読める軸と理由（構造的根拠のみ）"],
    "universality_note": "どの文脈で機能しやすいパターンか",
    "notable": "最も特徴的だと判断した軸とその根拠",
    "low_confidence": ["精度が低いと判断した軸名"]
  },
  "theoretical_references": [
    "Trope & Liberman (2010) Construal Level Theory",
    "Kirton (1976) KAI",
    "..."
  ]
}
```

### commentaryフィールドの詳細

**`summary`（120字以内）**
全体像の簡潔な記述。軸名を使わずに書く。`holistic_profile` の短縮版ではなく、より客観的な記述。

**`holistic_profile`（200字以内）**
軸の言葉を使わない自然言語での全体像。「`need_for_cognition` が high で」という記述は避け、「自発的に問いを構築し、複数の視点から考察する傾向がある」のように行動・傾向として記述する。

9軸の離散値だけでは捉えられない「質感」を補完する。たとえば `integrative_complexity: 5` と `need_for_cognition: high` が同時に出ても、それが「慎重な統合型」なのか「矛盾を保留する懐疑型」なのかは値だけでは判断できない。

**`strengths`（配列）**
強みとして読める軸と理由。構造的根拠のみに基づく。励ましや評価ではなく、「この軸のこの値が、こういう文脈で機能しやすい」という記述。

**`blind_spots`（配列）**
盲点として読める軸と理由。批判ではなく「この軸のこの値では、こういう文脈でつまずきやすい」という記述。

**`universality_note`**
このパターンがどの文脈で機能しやすく、どの文脈で課題になりやすいか。

**`notable`**
最も特徴的だと判断した1軸とその根拠。

**`low_confidence`（配列）**
データ不足・文脈依存などの理由で精度が低いと判断した軸名のリスト。

---

## コーディング指示力分析の完全JSONフォーマット

```json
{
  "schema_version": "1.0",
  "analyzed_at": "YYYY-MM",
  "message_count": 54,
  "coding_direction": {
    "specification_precision": 4,
    "error_recognition": "structural",
    "system_abstraction": "component",
    "decision_quality": "adaptive",
    "technical_vocabulary": "precise",
    "iteration_style": "incremental_clear"
  },
  "commentary": {
    "summary": "120字以内の全体像",
    "holistic_profile": "軸の言葉を使わず、AIへの指示スタイルの癖・傾向を200字以内で記述",
    "strengths": ["強みとして読める軸と根拠"],
    "growth_areas": ["伸びしろとして読める軸と根拠"],
    "collaboration_profile": "AIやエンジニアとどう協働するタイプかを自然言語で記述",
    "low_confidence": ["精度が低いと判断した軸名"]
  },
  "theoretical_references": [
    "Wing (2006) Computational Thinking",
    "Chi, Feltovich & Glaser (1981) Expert-Novice distinction",
    "..."
  ]
}
```

### 9軸と6軸のcommentaryの違い

| フィールド | 9軸（思考パターン） | 6軸（コーディング指示力） |
|-----------|-------------------|------------------------|
| `holistic_profile` | 思考の癖・傾向の全体像 | AIへの指示スタイルの全体像 |
| `strengths` | 思考パターンとしての強み | 指示力としての強み |
| `blind_spots` | 思考パターンとしての盲点 | （6軸では `growth_areas`） |
| `growth_areas` | （9軸にはない） | 指示力としての伸びしろ |
| `collaboration_profile` | （9軸にはない） | AIとの協働スタイルの全体像 |
| `universality_note` | どの文脈で機能しやすいか | （6軸にはない） |
| `notable` | 最も特徴的な1軸 | （6軸にはない） |

**`collaboration_profile` と `holistic_profile` の違い（6軸）：**
- `holistic_profile`：指示スタイルの癖・傾向（記述的）
- `collaboration_profile`：AIやエンジニアとの協働においてどういうタイプか（より実践的・評価的）

---

## 可視化：現在と今後

### 現在

分析結果はHTMLビジュアライザーとして出力される。JSONの値をテキストベースで整理した画面表示だ。

### 今後の構想

現在のHTMLビジュアライザーに加えて、以下の可視化を検討している。

- **レーダーチャート**：9軸・6軸を一目で把握できる比較表示
- **時系列グラフ**：過去の分析との差分をトラッキング
- **分布表示**：蓄積データとの比較で「上位X%」を可視化

これらは分析の精度を上げるものではなく、結果の解釈と継続的な活用のためのものだ。詳細は第8章で述べる。

---

## 送信用JSONとフル出力JSONの違い

分析結果は「フル出力JSON」と「送信用JSON」の2種類がある。

**フル出力JSON**（画面表示用）：
- `commentary` フィールド含む
- `theoretical_references` 含む
- 個人の思考パターンに関する詳細な記述を含む

**送信用JSON**（データベース蓄積用）：
- `commentary` と `theoretical_references` を除外
- `fingerprint`（または `coding_direction`）の値のみ
- 2000バイト以内に収まる設計

送信するかどうかはユーザーが判断する。フル出力JSONはローカル（画面上）にのみ表示される。

---

## `low_confidence` の読み方

`low_confidence` に軸名が含まれる場合、その軸の値は参考値として扱う。

主な原因：
- データ量不足（10〜30件）
- 文脈の偏り（特定タスクのみの会話）
- 言語混在
- 性質上判定しにくい（`face_strategy` の言語依存など）

`low_confidence` がついた軸を「間違い」として捉えるのではなく、「現時点のデータでの暫定値」として扱い、会話量が増えたタイミングで再分析するのが正しい活用法だ。

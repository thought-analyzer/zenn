---
title: "【第4回】思考パターン8類型の設計根拠 ── 9軸からキャラクターを導出する仕組み"
emoji: "🧭"
type: "tech"
topics: ["AI", "claude", "認知科学", "思考分析", "設計"]
published: false
---

:::message
thought-analyzerが出力する8類型の判定ロジックと設計思想を解説する技術記事。9軸の分析結果がどのようにキャラクター類型に変換されるかを知りたい方向け。
:::

thought-analyzerの分析結果には、9軸のスコアとは別に「設計者型」「橋渡し型」といったキャラクター類型が出力される。これは単なる演出ではない。9軸の値から論理的に導出される、思考パターンの圧縮表現だ。本記事はその設計根拠を説明する。

---

## なぜ類型化するのか

9軸のスコアは精密だが、情報量が多い。`abstraction_direction`、`problem_style`、`integrative_complexity`……9つの値を並べても、「この人の思考はどういう人か」という全体像は直感的に伝わりにくい。

類型化の目的は**情報の損失なく圧縮すること**ではない。9軸の値がすべての情報を持っていて、類型はその「入り口」として機能する。類型を見てから9軸を読むことで、数値の意味が文脈を持つ。

---

## 8類型の判定ロジック

類型は以下の優先順位で決定する。最初にマッチした条件が適用される。

| 優先 | 類型 | 判定条件 |
|---|---|---|
| 1 | **ARCHITECT（設計者型）** | `problem_style = pivot` かつ `integrative_complexity ≥ 5` |
| 2 | **ANALYST（解析者型）** | `problem_style = fix` かつ `integrative_complexity ≥ 5` |
| 3 | **BRIDGER（橋渡し型）** | `concept_distance = far` |
| 4 | **EXPLORER（探索者型）** | `perspective_taking = spontaneous` かつ `epistemic_curiosity = interest_type` |
| 5 | **PIONEER（開拓者型）** | `problem_style = pivot`（上記以外） |
| 6 | **ENGINEER（工学者型）** | `problem_style = fix`（上記以外） |
| 7 | **CONDUCTOR（指揮者型）** | `problem_style = delegate` |
| 8 | **NAVIGATOR（航行者型）** | 上記いずれにも該当しない |

---

## 設計上の判断を3点説明する

### 1. problem_styleを中心軸に置いた理由

`problem_style`（KAI: Kirton, 1976）は思考スタイルの中で最も安定した弁別力を持つ軸だ。問題に直面したとき「方向転換する（pivot）」「根本解決する（fix）」「委任する（delegate）」「保留する（suspend）」——この4つは、他の8軸と独立して思考の基本姿勢を決定する。

類型の主軸として`problem_style`を選んだのは、「どう動くか」という行動様式が、「どう考えるか」という思考様式の中で最も外部から観測しやすいからだ。

### 2. integrative_complexityによる階層化

`problem_style = pivot`のユーザーが全員PIONEERになるわけではない。`integrative_complexity`（IC: Suedfeld & Tetlock, 1977）が5以上の場合はARCHITECTに昇格する。

ICは思考の**分化**（複数の側面を認識する）と**統合**（それらを接続する）の程度を測る。pivot + 高IC = 方向転換しながら複雑な構造を再設計できる——これはARCHITECT固有の能力だ。単なるpivotより一段上の抽象レベルで動いている。

同様に、fix + 高IC = ANALYSTとなる。根本原因を特定するだけでなく、多層的に分析・統合できる。

### 3. BRIDGERの特殊な位置づけ

BRIDGERは`problem_style`より先に評価される（優先順位3）。これは`concept_distance = far`という軸が、`problem_style`の軸と直交しているからだ。

遠く離れた概念・領域を接続する思考スタイルは、「どう問題を解くか」より「どこから発想するか」に関わる。ARCHITECTでもANALYSTでも、`concept_distance = far`であればBRIDGERと判定される。遠距離の概念接続は、他の問題解決スタイルを横断する特性として扱う設計判断だ。

---

## 19のペルソナラベル

8類型の下に、さらに細かい**19のペルソナラベル**がある。同じARCHITECTでも、`concept_distance`や`perspective_taking`の値によってラベルが変わる。

```
ARCHITECT
  ├ concept_distance = far        → 異分野を繋ぐ思想設計者
  ├ perspective_taking = spont.   → 探索プロジェクトの構造家
  └ その他                        → 複雑系を再設計するアーキテクト

ANALYST
  ├ concept_distance = far        → 深層まで追う構造解析者
  ├ epistemic_curiosity = D型     → 精密に詰める知識解析者
  └ その他                        → 技術課題の根本解決者

BRIDGER
  ├ spont. + interest_type        → 概念を橋渡しするビジョナリー
  ├ perspective_taking = spont.   → 異領域を繋ぐ架け橋型思考者
  └ その他                        → 遠い概念を接続する発想者

EXPLORER
  ├ problem_style = pivot         → 創造的課題の開拓探索者
  └ その他                        → 好奇心で世界を広げる探索者

PIONEER
  ├ epistemic_curiosity = I型     → 発想転換を得意とする開拓者
  └ その他                        → 柔軟に再設計する問題解決者

ENGINEER
  ├ epistemic_curiosity = D型     → 知識ギャップを詰める実装者
  └ その他                        → 原因を特定する実装型思考者

CONDUCTOR
  ├ perspective_taking = spont.   → チームを束ねる協働コーディネーター
  └ その他                        → 実行を委ねる全体指揮者

NAVIGATOR
  ├ integrative_complexity ≥ 5   → 情報を熟成させる深慮型プランナー
  └ その他                        → 状況に応じて舵を切る航行型思考者
```

ペルソナラベルは類型よりも解像度が高いが、その分条件も複雑になる。分析結果を読むときは、類型で全体像を把握してからペルソナラベルで細部を確認する順序が理解しやすい。

---

## NAVIGATORは「残余」ではない

NAVIGATORは他の条件に該当しなかった場合のデフォルト類型だが、これは「劣った類型」を意味しない。

`problem_style = suspend`——判断を保留し、情報が揃うまで動かない——は、特定の文脈では最も賢い戦略だ。不確実な状況下での早期コミットを避ける認知スタイルは、「決断力がない」のではなく「適切な判断タイミングを選んでいる」と解釈できる。

`integrative_complexity ≥ 5`のNAVIGATORは「情報を熟成させる深慮型プランナー」となる。保留と高統合の組み合わせは、複雑な問題を急がずに多角的に処理するスタイルだ。

---

## 現状の設計上の限界

8類型への変換は、9軸の情報を必然的に圧縮する。いくつかの制限がある。

- **軸の等価交換問題**：`problem_style`と`integrative_complexity`が類型決定に強く影響し、`evaluation_framing`や`face_strategy`は類型には直接反映されない。これらの軸はペルソナラベルや`holistic_profile`で補完する設計だ。
- **境界値の問題**：`integrative_complexity = 4`と`5`では類型が変わる。5という閾値はIC研究の「分化+統合」の境界に基づくが、ログ量が少ない場合の判定誤差には注意が必要だ。
- **BRIDGERの支配問題**：`concept_distance = far`は`problem_style`より優先されるため、fix傾向の強いユーザーでもBRIDGERになりうる。これは設計上の意図的なトレードオフだ。

---

## 類型の先にあるもの

8類型は、9軸の値を人間が認識しやすい形に変換したものだ。類型を知ることで9軸の数値に文脈が生まれ、「自分はどう思考しているか」が少し見えやすくなる。

ただし類型は固定ではない。3ヶ月後に同じ人が分析を受けると、類型が変わることがある。`integrative_complexity`が上がってPIONEERからARCHITECTになる、`concept_distance`が広がってBRIDGERに移行する——思考のパターンは変化する。

類型の変化を追跡することで、「自分の思考がどの方向に動いているか」が見えてくる。これはthought-analyzerの継続利用で得られる、単回の分析には出ない情報だ。

---

## 関連記事

- **[AIで理想を実現する3層構造](https://zenn.dev/thought_analyzer/articles/thought-analyzer-framework)**：発想力・構想力・指示力はなぜ分けて考えるのか
- **[思考パターン9軸の設計根拠](https://zenn.dev/thought_analyzer/articles/thought-analyzer-9axes-theory)**：各軸の設計根拠
- **[コーディング指示力6軸の設計根拠](https://zenn.dev/thought_analyzer/articles/thought-analyzer-coding-6axes)**：AI協働時代の技術指示能力
- **[AIの言葉は、かすかに揺れている](https://zenn.dev/thought_analyzer/articles/thought-analyzer-influence-visualization)**：「コンテキストへの即時同期」と設計的応答
- **[thought-analyzer（skill本体）](https://github.com/thought-analyzer/thought-analyzer/blob/main/skills/skill.md?plain=1)**：Claude.aiで実際に分析を実行する

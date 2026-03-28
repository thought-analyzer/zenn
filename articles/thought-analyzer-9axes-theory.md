---
title: "thought-analyzerの9軸はなぜこれなのか ── 認知心理学の実証研究から設計した思考パターン分析"
emoji: "🧠"
type: "tech"
topics: ["claude", "AI", "認知科学", "プロンプト", "機械学習"]
published: false
---

「思考パターンを分析する」と言うのは簡単だ。問題は何を測るかだ。

曖昧な印象や経験則ではなく、実証研究に基づいた軸を選ぶこと。かつ、AIへの指示という文脈で意味を持つこと。この2条件から9軸を選定した。本記事ではその理論的根拠と、skillへの実装方針を解説する。

## 設計の前提：なぜ「離散的な軸」で測るか

思考パターンの分析手法には大きく2つのアプローチがある。

ひとつは自己申告型のアンケート（MBTI、Big Fiveなど）。もうひとつは**行動・発話からの外部観測**だ。thought-analyzerは後者を採用している。会話ログという「実際の行動記録」から、ユーザーが意識しない思考の癖を抽出する。自己認知のバイアスを避けるためだ。

また、結果をデータとして蓄積・比較するために、定義が明確な離散値か数値スコアで表現できる軸に絞った。

## 9軸の選定基準

以下の3条件をすべて満たす理論・研究から選定した。

```
① 査読済み論文に基づいていること
② 会話ログ（テキスト）から測定可能であること
③ AIへの指示の質・スタイルに接続できること
```

## 各軸の理論的背景

### 軸1：abstraction_direction（抽象〜具体の移動方向）
**Construal Level Theory ── Trope & Liberman（2010）**

心理的距離（時間・空間・社会的距離）が遠いほど抽象的な表現が増え、近いほど具体的になるという理論。AIへの指示において、概念から入るか事象から入るかはこの軸に直接現れる。会話の冒頭と展開方向から判定する。

### 軸2：problem_style（問題へのアプローチ）
**Kirton Adaption-Innovation Inventory ── Kirton（1976）**

問題解決スタイルをAdaptor（既存の枠内で修正）とInnovator（枠を変える）の連続体で測定する。AIとの協働では「修正を積み重ねるか」「別のアプローチに切り替えるか」という指示パターンとして現れる。

### 軸3：perspective_taking（他者視点の出現）
**Empathizing-Systemizing Theory ── Baron-Cohen（2003）**

他者の内的状態への自発的な注意の程度。AIへの指示において、「読者は」「相手が」などの視点が自発的に出るか否かに現れる。求められていないのに他者視点が出る場合を`spontaneous`と定義した。

### 軸4：face_strategy（言語の配慮戦略）
**Politeness Theory ── Brown & Levinson（1987）**

Face（メンツ・自律欲求）への配慮が言語パターンに現れるという理論。「〜かな」「〜かも」などの緩和表現の頻度をスコア化（0.0〜1.0）する。AIへの指示スタイルが断定型か提案型かに直接対応する。

### 軸5：concept_distance（概念接続の距離）
**Conceptual Blending ── Fauconnier & Turner（2002）／Remote Associates ── Mednick（1962）**

意味的に遠い概念領域を接続するほど創造的な思考とされる。会話内で「経済×感情」「技術×哲学」など、どの領域ペアが接続されているかをブリッジとして抽出する。固有名詞は含めず領域名のみを記録する設計だ。

### 軸6：evaluation_framing（評価のフレーミング）
**Prospect Theory ／ Framing Effect ── Kahneman & Tversky（1979）**

同じ情報でも「利得」として提示するか「損失」として提示するかで判断が変わる。AIへの指示において、肯定から入って問題を加えるか、問題から入るかのパターンを測定する。

### 軸7：need_for_cognition（思考への欲求）
**Need for Cognition Scale ── Cacioppo & Petty（1982）**

複雑な問題を考えることへの内発的動機。高NFCの人は自発的に問いを構築し、低NFCの人は簡潔な答えを好む。AIへの質問の複雑さ・自発的な問いの生成頻度から判定する。

### 軸8：integrative_complexity（統合的複雑性）
**Integrative Complexity ── Suedfeld & Tetlock（1977）**

テキストから客観的に測定できる唯一の思考複雑性指標。**分化**（複数の側面を認識する）と**統合**（それらを接続する）の程度を1〜7で評価する。政治家の演説分析にも使われてきた実績のある手法だ。

### 軸9：epistemic_curiosity（認識論的好奇心）
**Epistemic Curiosity I/D型 ── Litman & Spielberger（2003）**

知的好奇心をI型（新情報への喜び）とD型（知識ギャップを埋めたい不快感）に分類する。AIへの問いかけが「面白そうだから試したい」動機か「分からないと気持ち悪い」動機かに対応する。

## 設計上の制約と限界

9軸はいずれもテキストから測定できるが、精度は会話量に依存する。

```
10件未満    → 分析不可
10〜30件   → 低信頼（参考値）
30〜100件  → 標準精度
100件以上  → 高精度
```

また、単一セッションでは「その文脈での発話」しか観測できない。習慣的なパターンとの区別には複数セッションの蓄積が必要だ。この限界をskillのcommentaryに`low_confidence`フラグとして明示している。

## holistic_profile：離散値で失われるものを補う

9軸の離散値だけでは捉えられない「質感」がある。

たとえば`integrative_complexity: 5`と`need_for_cognition: high`が同時に出ても、それが「慎重な統合型」なのか「矛盾を保留する懐疑型」なのかは値だけでは判断できない。このため出力に`holistic_profile`フィールドを設け、軸の言葉を使わない自然言語での全体像を200字以内で記述する設計とした。

## skillとの接続

各軸の定義はskill.mdに完全に記述されており、判定ロジックは透明だ。

→ skill.md：https://github.com/thought-analyzer/thought-analyzer/blob/main/skills/skill.md?plain=1

thought-analyzerの思想・背景はnoteシリーズで解説している。

→ note：https://note.com/thought_analyzer

---

**参考文献**
- Trope, Y., & Liberman, N. (2010). Construal-level theory of psychological distance. *Psychological Review, 117*(2), 440–463.
- Kirton, M. J. (1976). Adaptors and innovators. *Journal of Applied Psychology, 61*(5), 622–629.
- Baron-Cohen, S. (2003). *The Essential Difference*. Basic Books.
- Brown, P., & Levinson, S. C. (1987). *Politeness*. Cambridge University Press.
- Fauconnier, G., & Turner, M. (2002). *The Way We Think*. Basic Books.
- Kahneman, D., & Tversky, A. (1979). Prospect theory. *Econometrica, 47*(2), 263–292.
- Cacioppo, J. T., & Petty, R. E. (1982). The need for cognition. *Journal of Personality and Social Psychology, 42*(1), 116–131.
- Suedfeld, P., & Tetlock, P. (1977). Integrative complexity of communications in international crises. *Journal of Conflict Resolution, 21*(1), 169–184.
- Litman, J. A., & Spielberger, C. D. (2003). Measuring epistemic curiosity. *Personality and Individual Differences, 35*(6), 1295–1305.

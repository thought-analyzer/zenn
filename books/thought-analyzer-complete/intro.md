---
title: "はじめに ── 本書の位置づけと読み方"
free: true
---

## thought-analyzerとは

thought-analyzerは、会話ログから思考パターンと技術指示力を分析するskillだ。Claude.aiで「思考パターンを分析して」と入力するだけで動く。

分析結果（フィンガープリントJSON）は、9軸の思考パターンと6軸のコーディング指示力を、実証研究に基づいて可視化する。自己申告ではなく、実際の発話記録から測定する設計だ。

## 無料のZenn記事・noteで十分できること

thought-analyzerを使い始めるにあたって、**無料の記事を読むだけで十分だ。**

| コンテンツ | 内容 | 対象 |
|-----------|------|------|
| [noteシリーズ](https://note.com/thought_analyzer) | なぜAIで差がつくのか・自分の思考を知る意味 | 全員 |
| [Zenn 第1回](https://zenn.dev/thought_analyzer/articles/thought-analyzer-framework) | 発想力×構想力×指示力の3層構造 | 全員 |
| [Zenn 第2回](https://zenn.dev/thought_analyzer/articles/thought-analyzer-9axes-theory) | 9軸の理論的背景 | エンジニア・研究者 |
| [Zenn 第3回](https://zenn.dev/thought_analyzer/articles/thought-analyzer-coding-6axes) | 6軸の理論的背景 | エンジニア・技術マネージャー |

分析を実行し、結果を読み、自分の傾向を把握するには、上記の無料コンテンツで十分だ。

## 本書が必要になる人

本書は、以下のような目的を持つ人向けに書いた。

- **各軸の全値定義と判定条件を正確に知りたい**：記事では理論的背景を中心に説明したが、本書では各軸が取りうる全値・判定の根拠・エッジケースを完全に記述する
- **出力JSONの全フィールドを理解して活用したい**：`holistic_profile` / `collaboration_profile` / `strengths` / `blind_spots` など、記事で触れなかったフィールドの意味と使い方を解説する
- **時系列追跡・record_idを使った継続的な自己分析をしたい**：トークン設計・record_idの管理・比較の方法を具体的に説明する
- **skill.mdをもとに独自の分析軸を追加・改造したい**：設計判断のトレードオフと、軸を追加するための条件を解説する

## 本書の構成

| 章 | 内容 |
|----|------|
| 第1章（本章） | 位置づけと読み方 |
| 第2章 | 3層フレームワークの深掘り |
| 第3章 | 思考パターン分析 9軸 完全仕様 |
| 第4章 | コーディング指示力分析 6軸 完全仕様 |
| 第5章 | 出力フォーマット・全フィールド解説 |
| 第6章 | 使い方・継続活用・record_id管理 |
| 第7章 | プライバシー設計の詳細 |

第2章以降は有料チャプターだ。Zenn記事の内容と重複する部分は最小限に抑え、**記事では触れていない仕様・判断・実装の詳細**に絞って書いた。

## skill本体

- [skill.md（思考パターン分析）](https://github.com/thought-analyzer/thought-analyzer/blob/main/skills/skill.md?plain=1)
- [coding-direction-skill.md（コーディング指示力分析）](https://github.com/thought-analyzer/thought-analyzer/blob/main/skills/coding-direction-skill.md?plain=1)

skillのコードは公開されており、本書の説明と照合できる。

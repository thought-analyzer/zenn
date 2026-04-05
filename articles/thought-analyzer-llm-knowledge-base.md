---
title: "知識はLintingで磨ける。では人間の思考は？ ── LLM wikiから考える"
emoji: 🗂️
type: idea
topics: [claude, ai, obsidian, thoughtanalyzer, pkm]
published: false
---

先日、こんな投稿を読んだ。

---

> LLM Knowledge Bases
>
> Something I'm finding very useful recently: using LLMs to build personal knowledge bases for various topics of research interest.
>
> Data ingest: I index source documents into a raw/ directory, then I use an LLM to incrementally "compile" a wiki — a collection of .md files in a directory structure. The wiki includes summaries of all the data, backlinks, and categorizes data into concepts, writes articles for them, and links them all.
>
> IDE: I use Obsidian as the IDE "frontend" where I can view the raw data, the compiled wiki, and the derived visualizations. Important to note that the LLM writes and maintains all of the data of the wiki, I rarely touch it directly.
>
> Linting: I've run some LLM "health checks" over the wiki to find inconsistent data, impute missing data, find interesting connections for new article candidates.
>
> TLDR: raw data is collected, then compiled by an LLM into a .md wiki, then operated on by various CLIs by the LLM to do Q&A and to incrementally enhance the wiki, and all of it viewable in Obsidian.

---

この投稿も興味深いが、私が注目したのはそれに対してついたコメントだ。知覚表現研究に携わるsabakichi（@knshtyk）さんの一言が、構造の核心を突いていた。

---

> 生データとエージェントが触る乱雑な領域を分けているのが要点だが、さらに人間が生産した情報も分けるとかなり良くなるのよね
>
> — [sabakichi @knshtyk](https://x.com/knshtyk/status/2040020164893417659)

---

ここで言う「wiki」は、Wikipediaのことではない。複数のMarkdownファイルをディレクトリ構造で整理した、自分専用の知識データベースのことだ。概念ごとに記事があり、記事同士がリンクでつながっている。

```
wiki/
  ├── concepts/
  │   ├── transformer.md
  │   └── rag.md
  ├── papers/
  │   └── attention-is-all-you-need.md
  └── index.md
```

これをLLMが自動で書き・更新し続ける、という構造だ。

そしてこれは、thought-analyzerの設計思想とも共通するところがある。

## 共通する前提

これまでの記事で繰り返し書いてきたことがある。**知識は「あること」より「使えること」に価値がある**、そして**その知識を活かせるかどうかは、何を問うかという構想力が決める**、という2点だ。

この投稿も同じ結論に至る。LLMがwikiを書いても、人間の仕事は3つ残る。どの情報をwikiに取り込むか選ぶこと（index）、wikiに対してどんな問いを投げるか、そしてLLMが出した答えを良いか悪いか判断すること——この3つはいずれも「何が目的か・何が良いか」の基準を持つ人間にしかできない。ここは前の記事と重なるので、深くは掘り下げない。

## LLMが書く知識と、人間が書く知識の非対称性

前回の[Obsidian記事](https://zenn.dev/thought_analyzer/articles/thought-analyzer-obsidian-thinking)は「人間が自分の言葉で書く」前提だった。この投稿は「LLMが書き・維持する」前提だ。

両者は根本的に異なる。

LLMが書くwikiは、整合性・網羅性・構造の一貫性が高い。索引も自動で維持される。人間が直接触ることはほとんどない。しかしLLMは構造上、統計的に平均的な知識を生成する装置だ。問いの角度が曖昧であれば、それに収束した出力が積み上がっていく。

人間が書いたノートには盲点がある。偏りがある。しかしその盲点と偏りの中に、**その人固有の問いの角度**が宿っている。ある概念を別の領域に結びつける独自の視点、習慣的に問い直す問いの構造——これはLLMには書けない。

**LLMが書くwikiは、誰が書いても似たものになる。人間が書いたノートは、その人にしか書けない。**

## 3層の分離という発想

コメントが指摘した「さらに人間が生産した情報も分ける」という一文が核心だ。

元の投稿が分けていたのは2層だった。

- **生データ（raw）**：論文・記事・リポジトリなどの一次ソース
- **LLMが触る領域**：コンパイルされたwiki、Q&Aの結果、健全性チェックの出力

コメントはここに3層目を加えることを提案している。

- **人間が生産した情報**：自分の言葉で書いたメモ・問い・考察

この3層目は、LLMが書いた知識とは性質が根本的に異なる。LLMの出力は生データの「変換」だが、人間の生産物は**その人の思考そのもの**だ。混ぜてしまうと、何がLLMの出力で何が自分の考えかが分からなくなる。分けることで、それぞれの性質を活かした操作ができるようになる。

## thought-analyzerが3層目だけを測る理由

thought-analyzerは、会話ログからユーザーの発言だけを抽出して分析する。AIの返答・引用・コードブロックは意図的に除外する。

この設計判断は、コメントが言う「3層の分離」と同じ発想から来ている。

AIが生成したコンテンツと人間の思考を混ぜた状態で分析すると、何を測っているかが曖昧になる。「このフレームはAIが提案したものか、それとも自分が設計したものか」が区別できなくなる。

3層目——人間が自分の意志で選んだ表現・問い・判断——だけを対象にすることで、**その人の思考パターンの構造的な特徴**が浮かび上がる。LLMが書いた知識が整合性を持っているかどうかではなく、**その知識を扱う人間がどのような問いを立てているか**を測るのがthought-analyzerの役割だ。

## Lintingとthought-analyzerの類似と違い

元の投稿で興味深かったのが「Linting」という概念だ。LLMがwiki全体を健全性チェックし、矛盾を見つけ、欠損を補い、新しい記事候補を提案する。

この構造はthought-analyzerに似ている。どちらも**外部から対象をチェックする**設計だ。

ただし対象が異なる。LLMのLintingは「外部知識の整合性」を見る。thought-analyzerは「内部思考のパターン」を見る。前者はwikiが正しいかを問い、後者はその人の思考がどのような構造を持つかを問う。

知識ベースの健全性と、それを運用する人間の思考の健全性は、別々に測る必要がある。

## まとめ

- LLMが書く知識は整合性が高いが、問いの独自性を持たない
- 人間が書く知識には盲点があるが、その人固有の思考が宿る
- 生データ／LLM処理領域／人間が生産した情報の3層を分けることが設計の要点
- thought-analyzerは3層目だけを分析対象にする——それは偶然ではなく、設計の必然だ
- 知識ベースの整合性と、それを問う人間の思考パターンは、別々に測定される必要がある

---

**関連記事**

**← 知識は使えて初めて資産になる** [知識は"使えて"初めて資産になる ── Obsidian論への応答](https://zenn.dev/thought_analyzer/articles/thought-analyzer-obsidian-thinking)：人間が書く知識の価値と盲点

**← 自律型AIと構想力** [自律型AIでは、あなたの思考がどれほど関与するのか](https://zenn.dev/thought_analyzer/articles/thought-analyzer-autonomous-ai)：LLMに任せるほど問いの設計が唯一の変数になる

**skill本体 →** [thought-analyzer](https://github.com/thought-analyzer/thought-analyzer/blob/main/skills/skill.md?plain=1)：Claude Codeで実際に分析を実行する

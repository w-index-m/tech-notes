# Claudeをより使いこなす: API機能ガイド

> [Claude Cookbook](https://platform.claude.com/cookbook/)(Anthropic公式のAPI活用レシピ集)から、特に実用性の高いトピックを厳選して整理した開発者向けノート。これまでの `cca-f-study-guide` や `claude-camp-training-notes` が「Claude Code(CLI)の使い方」「資格対策」「組織研修」中心だったのに対し、こちらは **Claude API / Agent SDKを使って自分のアプリにClaudeを組み込む** 側の知識。
>
> サンプルコードはPython SDK(`anthropic`パッケージ)を想定。

## 目次

1. [ツール利用(Function Calling)](#1-ツール利用function-calling)
2. [拡張思考(Extended Thinking)](#2-拡張思考extended-thinking)
3. [プロンプトキャッシュ(Prompt Caching)](#3-プロンプトキャッシュprompt-caching)
4. [引用機能(Citations)](#4-引用機能citations)
5. [Vision(画像入力)](#5-visionとは)
6. [Batch API](#6-batch-api)
7. [RAG(検索拡張生成)](#7-ragretrieval-augmented-generation)
8. [Evals(評価の仕組み)](#8-evals評価の仕組み)
9. [Skills(Agent Skills)](#9-skillsagent-skills)
10. [コスト管理(Admin API / Usage & Cost)](#10-コスト管理admin-api--usage--cost-reports)

---

## 1. ツール利用(Function Calling)

### 何ができるか
Claudeに「使える道具(関数)」をJSON Schemaで教えておくと、Claudeが「この道具を、この引数で呼びたい」と判断して返してくる。実際に関数を実行するのはこちら側で、その結果をClaudeに返すとClaudeが続きを考える、という往復(エージェントループ)が基本形。

### 基本パターン
```python
tools = [{
    "name": "get_weather",
    "description": "指定した場所の現在の天気を取得する。ユーザーが天気や気温について聞いた時に呼び出す",
    "input_schema": {
        "type": "object",
        "properties": {
            "location": {"type": "string", "description": "都市名、例: 東京"},
        },
        "required": ["location"],
    },
}]

response = client.messages.create(
    model="claude-opus-5-5",
    max_tokens=1024,
    tools=tools,
    messages=[{"role": "user", "content": "東京の天気は?"}],
)

if response.stop_reason == "tool_use":
    tool_call = next(b for b in response.content if b.type == "tool_use")
    result = get_weather(tool_call.input["location"])  # 実際の関数を実行
    # tool_result を次のメッセージに含めてClaudeに送り返す
```

### 重要なコツ
- **description(説明文)が命**: Claudeは説明文を読んでツールを使うか判断するので、「いつ呼ぶべきか」を具体的に書くと呼び出し精度が上がる
- **並列ツール呼び出し**: 1回の応答で複数のツール呼び出しを含められる(デフォルトON)。結果は**1つのuserメッセージにまとめて**返す(分割すると並列呼び出しをしなくなる)
- **失敗したら`is_error: true`を付けて返す**(結果を握りつぶさない)
- **Tool Runner(SDKのヘルパー)**を使うと、この「呼び出し→実行→結果を返す」のループをSDKが自動で回してくれる(自分でwhileループを書かなくて良い)

---

## 2. 拡張思考(Extended Thinking)

### 何ができるか
複雑な推論が必要なタスクで、Claudeに「考える時間」を与える機能。回答を出す前に内部で段階的に思考し、その上で最終回答を返す。数学的推論・コード設計・複雑な分析タスクで精度が上がる。

### 基本パターン
```python
response = client.messages.create(
    model="claude-opus-5-5",
    max_tokens=4096,
    thinking={"type": "adaptive"},  # Claudeが必要な分だけ自動で思考する
    messages=[{"role": "user", "content": "この複雑な設計課題を分析して"}],
)
```

### 重要なコツ
- 今の主要モデルは`budget_tokens`で思考量を固定する古い方式ではなく、**`{"type": "adaptive"}`で「どれだけ考えるか」をClaude自身に判断させる**のが基本
- 思考の深さは`output_config: {effort: "low"|"medium"|"high"|"xhigh"|"max"}`で調整する(effortが高いほど丁寧に考えるがコストも上がる)。コーディング・長時間のエージェントタスクほどeffortの効果が大きく、チャットや分類タスクは`low`でも十分なことが多い
- 思考の中身(生の思考過程)はデフォルトでは見えない。ユーザーに表示したい場合は`display: "summarized"`を明示的に指定する

---

## 3. プロンプトキャッシュ(Prompt Caching)

### 何ができるか
長いシステムプロンプトや共通のツール定義など、「毎回同じ内容」をキャッシュしておき、2回目以降のリクエストでその部分の処理をスキップしてコスト・レイテンシを削減する機能。**最大90%近いコスト削減**になることもある。

### 基本パターン
```python
response = client.messages.create(
    model="claude-opus-5-5",
    max_tokens=1024,
    system=[
        {
            "type": "text",
            "text": very_long_system_prompt,  # 長い固定の指示文
            "cache_control": {"type": "ephemeral"},  # ここまでをキャッシュ
        }
    ],
    messages=[{"role": "user", "content": "質問内容"}],
)
```

### 重要なコツ
- **「前方一致(prefix match)」方式**: キャッシュされた内容のどこか1バイトでも変わると、それ以降が全部無効になる。だから**変化しない固定コンテンツを先頭に、変化する内容(日時・個別の質問など)を末尾に**配置する
- リクエストの構築順序は「tools → system → messages」。この順番を意識して「固定部分→可変部分」を並べる
- 本当にキャッシュされているかは `usage.cache_read_input_tokens` の値で確認できる。ここが毎回0なら、どこかで無意識にキャッシュを壊している(例: システムプロンプトに現在時刻を埋め込んでいる、JSONのキー順序が毎回バラバラ、など)

---

## 4. 引用機能(Citations)

### 何ができるか
PDFやドキュメントを読ませて回答させる際、「回答のこの部分は、ドキュメントのこの箇所から来ている」という根拠(引用元)を自動的に付けてくれる機能。ハルシネーション(事実と異なる回答)対策や、回答の検証可能性を高めたい場面で有効。

### 基本パターン
```python
response = client.messages.create(
    model="claude-opus-5-5",
    max_tokens=1024,
    messages=[{
        "role": "user",
        "content": [
            {
                "type": "document",
                "source": {"type": "base64", "media_type": "application/pdf", "data": pdf_base64},
                "citations": {"enabled": True},  # 引用を有効化
            },
            {"type": "text", "text": "この契約書の解約条件を教えて"},
        ],
    }],
)
```

### 重要なコツ
- 引用が有効なとき、回答は複数の`text`ブロックに分割され、引用元のあるブロックには`citations`配列が付く(「どの文書の何ページ・何文字目か」まで特定できる)
- 社内ドキュメントQ&A・契約書レビュー・論文要約など「出典を明示したい」用途と相性が良い

---

## 5. Visionとは

### 何ができるか
画像(スクリーンショット・写真・図表・グラフなど)を入力として渡し、内容の説明・分析・テキスト抽出(OCR的な用途)・比較などをさせる機能。

### 基本パターン
```python
response = client.messages.create(
    model="claude-opus-5-5",
    max_tokens=1024,
    messages=[{
        "role": "user",
        "content": [
            {
                "type": "image",
                "source": {"type": "base64", "media_type": "image/png", "data": image_base64},
            },
            {"type": "text", "text": "このグラフから読み取れる傾向を説明して"},
        ],
    }],
)
```

### 重要なコツ
- 対応形式: JPEG・PNG・GIF・WebP
- 複数画像を1回のリクエストに含めて「比較」させることも可能(例: Before/Afterのスクリーンショット比較)
- グラフ・チャート・UIスクリーンショット・手書きメモの読み取りなど、テキストでは伝えにくい情報を渡したい時に使う

---

## 6. Batch API

### 何ができるか
「今すぐ返事が欲しい」わけではない大量のリクエスト(例: 1万件のレビューを一括要約する、など)を、**通常の半額(50%オフ)**でまとめて非同期処理してもらう機能。結果は数分〜数時間後に取得する想定。

### 基本パターン
```python
batch = client.messages.batches.create(requests=[
    {"custom_id": "review-1", "params": {"model": "claude-sonnet-5-5", "max_tokens": 512, "messages": [...]}},
    {"custom_id": "review-2", "params": {"model": "claude-sonnet-5-5", "max_tokens": 512, "messages": [...]}},
    # ... 大量に追加可能
])

# ポーリングして完了を待つ
status = client.messages.batches.retrieve(batch.id).processing_status
# "ended" になったら結果を取得
for result in client.messages.batches.results(batch.id):
    print(result.custom_id, result.result.type)  # succeeded / errored / canceled / expired
```

### 重要なコツ
- 結果は**順不同で返ってくる**ため、必ず`custom_id`をキーにして突き合わせる(届いた順=送った順ではない)
- リアルタイム性が不要な「大量処理」タスク(ログ分析、コンテンツ一括生成、データセットのラベリングなど)に向く

---

## 7. RAG(Retrieval-Augmented Generation)

### 何ができるか
Claude自体は学習データの範囲でしか知識を持たないため、「自社の最新データ」「個人の文書」など外部知識を検索して回答に反映させる仕組み。典型的な流れ:

1. 手元の文書群を事前に「埋め込みベクトル(Embeddings)」に変換してベクトルDBに格納
2. ユーザーの質問も埋め込みベクトル化し、類似度検索で関連文書を取得
3. 取得した文書をプロンプトに含めてClaudeに渡し、その内容に基づいて回答させる

### Claude API側での関連機能
- **Web検索ツール(`web_search`)**: Anthropicがホストするサーバー側ツール。ウェブ検索結果を直接ツール結果として受け取れる(自前でベクトルDBを持たなくても「最新情報の検索」ができる)
- **引用機能(Citations)**: 検索・取得した文書のどこを根拠にしたか明示できる(上記4番と組み合わせて使う)
- **Files API**: 一度アップロードしたファイルをIDで使い回せる(毎回base64で送り直さなくて良い)

### 重要なコツ
- 「社内文書Q&A」のようなユースケースでは、ベクトル検索(MongoDB Atlas・Pinecone等との連携がCookbookに例あり)+Claudeでの回答生成、という組み合わせが定番
- 検索結果をそのまま全部詰め込むと無駄にコンテキストを消費するので、関連度の高い上位数件だけに絞る設計が重要

---

## 8. Evals(評価の仕組み)

### 何ができるか
「プロンプトを変更した」「モデルを切り替えた」時に、**本当に改善したのか/劣化していないか**を定量的に判断するための評価の仕組み。勘や「なんとなく良さそう」で変更を本番投入しないための仕組み。

### Evalの3要素
1. **入力例のセット**: 実際に遭遇する(または遭遇しうる)入力パターンを集める
2. **実行方法**: その入力に対してアプリ(Claude呼び出しを含む)を実際に動かす手段
3. **採点方法**: 出力が「良い」かどうかを判定する方法(ルールベースの一致判定、Claude自身を審査員にする「LLM-as-judge」方式など)

### 重要なコツ
- 1つのEvalで「全部」を測ろうとせず、**1つのユースケースに対して1つのEval**に絞る(分類タスクと要約タスクでは評価方法が全く異なる)
- グレーディング(採点)方法に納得できるまで検証してから本番の改善サイクルに使う。採点基準が曖昧だと、改善したつもりで実は悪化しているケースを見逃す
- モデル移行・プロンプト改善・コスト最適化など、「変更して良くなったか」を判断する全ての場面の土台になる

---

## 9. Skills(Agent Skills)

### 何ができるか
「よく使う作業手順」を再利用可能な形でパッケージ化し、Claudeに「このSkillを使って」と指示できる仕組み。Claude Codeで使われているのと同じ概念がAPI経由でも使える。

### 代表的な用途
- **ドキュメント生成**: Word(.docx)・PowerPoint(.pptx)・Excel(.xlsx)・PDFなどを決まったフォーマットで自動生成するSkill
- **ファイナンス系**: 財務モデリングのテンプレートに沿った分析Skill
- **カスタムワークフロー**: 自社の作業手順(例: 「このチェックリストに沿ってコードレビューする」)をSkillとして定義

### 基本パターン(概念)
```python
response = client.beta.messages.create(
    model="claude-opus-5-5",
    max_tokens=4096,
    container={"skills": [{"type": "custom", "skill_id": "..."}]},
    tools=[{"type": "code_execution_20260521", "name": "code_execution"}],
    betas=["code-execution-2025-08-25"],
    messages=[{"role": "user", "content": "このデータから四半期レポートの.pptxを作って"}],
)
```

### 重要なコツ
- Skillはコード実行環境(サンドボックス)の中で動くため、`code_execution`ツールとセットで使う
- 「毎回同じ指示を書き直す」のではなく「1回作って使い回す」ことで、品質の安定とプロンプトの簡潔化につながる

---

## 10. コスト管理(Admin API / Usage & Cost Reports)

### 何ができるか
組織全体のAPI利用状況・コストをプログラムから可視化・管理する機能群。「どのプロジェクトが/どのAPIキーが/どのモデルで、どれだけ使っているか」を把握できる。

### できること(Admin API)
- メンバー・招待・ワークスペースの管理
- APIキーの一覧・更新
- 組織・ワークスペース単位のレート制限(Rate Limits)の確認
- サービスアカウント・WIF(Workload Identity Federation)・CMEK(暗号化キー)の管理

### Usage & Cost Reports(別エンドポイント、raw HTTPのみ)
- 実際の**トークン使用量・金額ベースのコストレポート**はAdmin APIのSDKには含まれず、生のHTTPリクエストで取得する専用エンドポイントを使う
- モデル別・ワークスペース別・API キー別などの粒度でコストを集計できる

### 重要なコツ
- 管理系の操作には**管理者権限の認証情報**(Admin APIキー `sk-ant-admin...` または `org:admin` スコープのOAuthトークン)が必要。通常のAPIキーでは権限エラーになる
- コスト最適化の第一歩は「現状把握」: まずUsage & Cost Reportsで実際の内訳を見てから、プロンプトキャッシュ・Batch API・モデル選定(Opus/Sonnet/Haikuの使い分け)・エフォート調整などの打ち手を検討するのが基本的な流れ

---

## まとめ: どう組み合わせるか

- **コスト削減の王道**: プロンプトキャッシュ(3番)→ Batch API(6番、リアルタイム性不要な場合)→ モデル/effort選定、の順で検討する
- **精度・信頼性を上げたい**: 拡張思考(2番)+ 引用機能(4番)+ Evals(8番)で「考えさせて」「根拠を出させて」「本当に良くなったか測る」の3点セット
- **自分のデータ/最新情報を使わせたい**: RAG(7番)やWeb検索ツール、ツール利用(1番)の組み合わせ
- **繰り返し作業の効率化**: Skills(9番)で手順をパッケージ化

*出典: [Claude Cookbook](https://platform.claude.com/cookbook/)(Anthropic公式)の各カテゴリを基に、概念・コード例を整理*

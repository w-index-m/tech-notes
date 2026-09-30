# PyTorch / 深層学習 学習ノート

> [Zero to Mastery: Learn PyTorch for Deep Learning](https://github.com/mrdbourke/pytorch-deep-learning) コース(Ali-hey-0/DeepLearning-YouTube フォーク経由)を、Python歴2年の初学者向けに整理した学習ノート。ML未経験でも読めるよう、セクション0に機械学習の基礎を前置きしている。

## 目次

- [0: 機械学習の基礎](#0-機械学習の基礎pytorchに入る前の前提知識)
- [00: PyTorch Fundamentals](#00-pytorch-fundamentals)
- [01: PyTorch Workflow](#01-pytorch-workflow)
- [02: Neural Network Classification](#02-neural-network-classification)
- [03: Computer Vision](#03-computer-vision)
- [04: Custom Datasets](#04-custom-datasets)
- [05: Going Modular](#05-going-modular)
- [06: Transfer Learning](#06-transfer-learning)
- [07: Experiment Tracking](#07-experiment-tracking)
- [08: Paper Replicating (Vision Transformer)](#08-paper-replicating-vision-transformer)
- [09: Model Deployment](#09-model-deployment)

---

## 0: 機械学習の基礎(PyTorchに入る前の前提知識)

> Python歴2年あればコードは問題なく読めるはずですが、「機械学習の考え方」自体に馴染みが薄いと、PyTorchのコードが「何のためにその処理をしているのか」分かりにくくなります。ここでは、その土台を先に埋めておきます。

### 0-1. 機械学習とは何か

**一言でいうと**: 「ルールをプログラマが書く」代わりに、「データからルール(パターン)をコンピュータ自身に見つけさせる」アプローチ。

- 従来のプログラミング: `入力 + ルール(あなたが書くコード) → 出力`
- 機械学習: `入力 + 出力(正解データ) → ルール(モデルが自動で学習)`

例: 「迷惑メール判定」を作りたいとき
- 従来: 「"当選"という単語が含まれていたら迷惑メール」のようなルールを人力で無数に書く(すぐ限界が来る)
- 機械学習: 大量の「迷惑メール/正常メールの実例」を与えると、モデルが自分でパターンを見つける

### 0-2. 学習の3つの型

| 種類 | 特徴 | 例 |
|---|---|---|
| **教師あり学習(Supervised Learning)** | 「入力」と「正解ラベル」のペアで学習する。このコースの中心 | 画像→犬/猫の分類、家の広さ→価格の予測 |
| **教師なし学習(Unsupervised Learning)** | 正解ラベルなしで、データ自体の構造・グループを見つける | 顧客データのクラスタリング |
| **強化学習(Reinforcement Learning)** | 試行錯誤の結果得られる「報酬」を最大化するように学習する | ゲームAI、ロボット制御 |

PyTorchコース(00〜09)は基本的に**教師あり学習**を扱います。

### 0-3. 2大タスク: 回帰と分類

教師あり学習は、出力が何かによってさらに2種類に分かれます。

- **回帰(Regression)**: 連続値を予測する(例: 家の価格、気温)。コースの01「PyTorch Workflow」で扱う
- **分類(Classification)**: カテゴリを予測する(例: 犬か猫か、迷惑メールか否か)。コースの02・03・04で扱う
  - **二値分類(Binary)**: 2択(例: 迷惑メールか否か)
  - **多クラス分類(Multi-class)**: 3つ以上(例: 犬/猫/鳥)

### 0-4. モデルが「学習する」とは具体的に何をしているか

これがPyTorchコードを読む上で一番重要な部分です。以下の**5ステップのループ**が、機械学習の学習プロセスの本質です(コースの01で繰り返し出てくる「トレーニングループ」そのもの)。

1. **予測する(Forward pass)**: 今のモデル(まだ未熟)で入力データから出力を予測してみる
2. **間違いを測る(Loss calculation)**: 予測と正解がどれだけズレているかを「損失関数(Loss Function)」で数値化する
3. **ズレの方向を計算する(Backward pass / Backpropagation)**: 「どのパラメータをどちらに動かせば損失が減るか」を微分(勾配計算)で求める
4. **パラメータを更新する(Optimizer step)**: 計算した勾配の方向に、モデルの中の数値(重み)を少しだけ動かす
5. **これを何千回、何万回も繰り返す(Epoch)**: 繰り返すたびに、モデルは少しずつ賢くなっていく

PyTorchのコードでは、これがほぼ毎回このパターンで書かれます(コースの01以降で何十回も同じ形が出てきます):

```python
for epoch in range(epochs):
    model.train()
    y_pred = model(X_train)          # ① 予測する
    loss = loss_fn(y_pred, y_train)  # ② 間違いを測る
    optimizer.zero_grad()
    loss.backward()                  # ③ ズレの方向を計算する
    optimizer.step()                 # ④ パラメータを更新する
    # これをepoch回繰り返す(⑤)
```

### 0-5. 重要な用語ミニ辞典

| 用語 | 意味 |
|---|---|
| **モデル(Model)** | 入力から出力を予測する関数の集合体。内部に大量の「パラメータ(重み)」を持つ |
| **パラメータ / 重み(Parameters / Weights)** | モデルの中にある、学習によって少しずつ調整されていく数値 |
| **損失関数(Loss Function)** | 予測と正解のズレを数値化する関数。小さいほど良い(例: MSE=回帰用、CrossEntropy=分類用) |
| **オプティマイザ(Optimizer)** | 損失を減らす方向にパラメータを更新するアルゴリズム(例: SGD、Adam) |
| **学習率(Learning Rate)** | パラメータを1回にどれだけ動かすかの歩幅。大きすぎると発散、小さすぎると学習が遅い |
| **エポック(Epoch)** | 訓練データ全体を1周すること。通常、数百〜数千エポック繰り返す |
| **勾配(Gradient)** | 「損失を減らすには、このパラメータをどちらにどれだけ動かせばいいか」を示す微分値 |

### 0-6. データの分け方: train / validation / test

モデルの実力を正しく測るため、データは必ず分割して使います。

- **訓練データ(Training set)**: モデルが実際に学習に使うデータ(通常 60〜80%)
- **検証データ(Validation set)**: 学習中に「ちゃんと汎化できているか」を確認するためのデータ(ハイパーパラメータ調整に使う)
- **テストデータ(Test set)**: 最後に1回だけ使う「本当の実力測定」用データ。学習には絶対に使わない

> なぜ分けるのか: 訓練データだけで評価すると「丸暗記しているだけ」を「賢い」と誤解してしまうため(次項の過学習を参照)

### 0-7. 過学習(Overfitting)と未学習(Underfitting)

機械学習で最もよく遭遇する問題です。

| | 過学習(Overfitting) | 未学習(Underfitting) |
|---|---|---|
| 状態 | 訓練データに「丸暗記」しすぎて、新しいデータに対応できない | そもそも学習が足りず、訓練データすら上手く予測できない |
| 症状 | 訓練データの精度は高いが、テストデータの精度が低い | 訓練データもテストデータも精度が低い |
| 対策の例 | データを増やす、モデルを単純にする、正則化、Dropout、Data Augmentation(コース04で登場) | モデルを複雑にする、学習をもっと続ける、学習率を調整する |

理想は「ちょうど良い(Just right)」状態 — 訓練データからパターンを学びつつ、新しいデータにも対応できる状態です。

### 0-8. モデルの評価指標

タスクの種類によって、良し悪しの測り方が変わります。

**回帰(Regression)の指標**
- **MAE(平均絶対誤差)**: 予測と正解の差の絶対値の平均
- **MSE(平均二乗誤差)**: 差を2乗して平均。大きな誤差をより強くペナルティ

**分類(Classification)の指標**
- **Accuracy(正解率)**: 全体のうち正解した割合。ただしクラスの偏りがあると誤解を招きやすい
- **Precision(適合率)** / **Recall(再現率)**: 「陽性と予測したうち実際に陽性だった割合」/「実際に陽性だったもののうち正しく陽性と判定できた割合」。迷惑メール判定や病気診断など、片方の間違いのコストが高い場面で重要
- **Confusion Matrix(混同行列)**: 正解と予測の組み合わせを表にまとめたもの。どんな種類の間違いが多いか可視化できる

### 0-9. ニューラルネットワークの超基礎

PyTorchが扱う「モデル」の正体は、多くの場合「ニューラルネットワーク」です。

- **ニューロン(層のユニット)**: 入力を受け取り、重み付けして足し合わせ、「活性化関数」を通して出力する最小単位
- **層(Layer)**: ニューロンの集まり。入力層→隠れ層(複数可)→出力層、という構造
- **活性化関数(Activation Function)**: ニューラルネットに「非線形性」を与える関数(例: ReLU、Sigmoid、Softmax)。これが無いと、何層重ねても結局「直線」しか表現できない
- **深層学習(Deep Learning)**: 隠れ層を複数(「深く」)重ねたニューラルネットワークを使う手法。このコース全体のテーマ

**なぜPyTorchのようなフレームワークが必要か**: 上記の「予測→損失計算→勾配計算→更新」のループを、数式レベルで自分で書くのは非常に大変(特に勾配計算=微分)。PyTorchは`loss.backward()`の一行で、どんなに複雑なモデルでも自動的に勾配を計算してくれる(自動微分/Autograd)。これが深層学習フレームワークの最大の価値。

---

*この「ML基礎」章を土台として、以降のPyTorchコース本編(00〜09)を読むと、コードの各行が「学習ループの5ステップ」「回帰か分類か」「過学習対策」のどれに対応しているかが見えやすくなります。*

---

## 00: PyTorch基礎

### そのセクションの目的・学べること
PyTorchにおける最も基本的なデータ構造である**テンソル（tensor）**の扱い方を学ぶセクション。テンソルの作成方法、形状（shape）・データ型（dtype）・デバイス（device）の確認方法、テンソル同士の演算（加減乗除・行列積）、reshape/view/stack/squeeze/unsqueeze/permuteによる形状操作、インデックス操作、NumPyとの相互変換、再現性（random seed）、GPU上でのテンソル操作までを一通りカバーする、いわば「PyTorchのアルファベット」にあたる回。

### 主要な概念
- **テンソルの階層**: scalar（0次元）→ vector（1次元）→ matrix（2次元）→ tensor（n次元）。外側の`[`の数を数えると次元数が分かる。
- **`torch.rand()` / `torch.zeros()` / `torch.ones()` / `torch.arange()`**: ランダム・ゼロ・1埋め・連番のテンソルを`size`パラメータで生成。ニューラルネットは基本的にランダムな数値から始まり、学習を通してその値を更新していく。
- **`shape` / `dtype` / `device`**: テンソルに関するエラーのほとんどはこの3つのどれかに起因する（「何のshape？何のdtype？どこ(device)？」の合言葉）。
- **基本演算と行列積（matmul）**: 要素ごとの積（`*`）と行列積（`@` / `torch.matmul()`）は別物。行列積には「内側の次元が一致する」というルールがあり、これが**shape mismatch**という最頻出エラーの原因になる。
- **転置（`.T` / `torch.transpose()`）**: 行列積の形状を合わせるために使う。`nn.Linear()`の内部でも`y = x·A^T + b`という形で使われている。
- **reshape / view / stack / squeeze / unsqueeze / permute**: 値を変えずに形状だけを変える一連の操作。`view()`は元テンソルとメモリを共有する点に注意（値を変えると元テンソルも変わる）。
- **インデックス**: Python/NumPyと同様、`:`で「その次元は全部」を意味する。次元が増えるほど混乱しやすいので「visualize, visualize, visualize（可視化せよ）」が合言葉。
- **PyTorch ⇔ NumPy**: `torch.from_numpy(ndarray)`と`tensor.numpy()`で相互変換。NumPyはデフォルトfloat64なので、PyTorch側の標準float32に変換したい場合は`.type(torch.float32)`を使う。
- **再現性（reproducibility）**: `torch.manual_seed(seed)`で疑似乱数を固定できる。ただし2つのランダムテンソルを同じにしたい場合、seedは各生成呼び出しの直前で毎回セットし直す必要がある。
- **GPU利用**: `torch.cuda.is_available()`でGPUの有無を確認し、`device = "cuda" if torch.cuda.is_available() else "cpu"`という**device-agnostic（デバイスに依存しない）コード**を書くのがベストプラクティス。`tensor.to(device)`でテンソルを移動（コピーが返るので再代入が必要）。NumPyはGPU非対応なので、戻す際は`.cpu()`を使う。

### 重要なコードパターン
```python
# デバイスに依存しないコードの定番パターン
device = "cuda" if torch.cuda.is_available() else "cpu"
```
GPUがあれば使い、なければCPUにフォールバックする、以後何度も登場する定型句。

```python
tensor_A = torch.tensor([[1, 2], [3, 4], [5, 6]])
tensor_B = torch.tensor([[7, 10], [8, 11], [9, 12]])
# torch.matmul(tensor_A, tensor_B) はエラー（内側の次元が不一致: (3,2)@(3,2)）
output = torch.matmul(tensor_A, tensor_B.T)  # (3,2)@(2,3) -> (3,3) でOK
```
行列積では転置で内側の次元を合わせる、という最頻出のトラブルシューティングパターン。

```python
torch.manual_seed(42)
random_tensor_C = torch.rand(3, 4)
torch.manual_seed(42)
random_tensor_D = torch.rand(3, 4)
# C と D は同じ値になる
```
再現性を確保するためのseed固定パターン。

### 押さえておくべきポイント・つまずきやすい点
- shape・dtype・deviceの不一致が、PyTorchで最もよく遭遇する3大エラー。特に「片方float32、片方float16」「片方CPU、片方GPU」の組み合わせでの演算はエラーになる。
- `torch.mean()`など一部の関数はfloat型（`torch.float32`など）を要求するため、int型テンソルのままだとエラーになる。
- `tensor.to(device)`は**コピー**を返すので、元のテンソルを更新したい場合は`some_tensor = some_tensor.to(device)`のように再代入する必要がある。
- `view()`やpermuteは元データとメモリを共有する「view（ビュー）」を返すため、値を書き換えると元のテンソルにも影響する（`reshape()`は必ずしもそうではない点に注意）。

---

## 01: PyTorchワークフロー基礎

### そのセクションの目的・学べること
「直線（linear regression）」という最も単純な問題を題材に、**PyTorchの標準的な機械学習ワークフロー**（データ準備→モデル構築→学習→推論→保存/読み込み）を一通り体験するセクション。`nn.Module`のサブクラス化、損失関数とオプティマイザの選び方、学習ループ・テストループの書き方、モデルの保存・読み込みという、以降すべてのノートブックで繰り返し使われる型を学ぶ、コース全体の土台となる回。

### 主要な概念
- **データの準備と分割**: 既知の`weight`・`bias`から`y = weight * X + bias`で直線データを人工的に作り、train/testに分割（train: モデルが学習するデータ、test: 汎化性能を評価するデータ）。
- **PyTorchモデル構築の4大要素**: `torch.nn`（計算グラフの部品）、`nn.Parameter`（学習対象のテンソル、`requires_grad=True`で勾配計算=autogradが有効になる）、`nn.Module`（モデルの基底クラス、`forward()`の実装が必須）、`torch.optim`（パラメータの更新方法＝最適化アルゴリズム）。
- **損失関数（loss function）とオプティマイザ（optimizer）**: 損失関数はモデルの予測がどれだけ間違っているかを測る（例: 回帰なら`nn.L1Loss()` = MAE）。オプティマイザはその損失を下げる方向にパラメータをどう更新するかを決める（例: `torch.optim.SGD(params, lr)`）。学習率(`lr`)は自分で設定する**ハイパーパラメータ**。
- **PyTorch学習ループの5ステップ**（コース内で繰り返し登場する黄金パターン）: ①forward pass ②loss計算 ③`optimizer.zero_grad()` ④`loss.backward()`（逆伝播） ⑤`optimizer.step()`（勾配降下）。
- **`torch.inference_mode()`**: 推論時に勾配計算などを無効化して高速化するコンテキストマネージャ。古いコードでは`torch.no_grad()`が使われることもあるが、`inference_mode()`の方が新しく推奨される。
- **`model.train()` / `model.eval()`**: 学習モードと評価モードの切り替え。テストループでは`.eval()` + `torch.inference_mode()`を組み合わせて使う。
- **モデルの保存・読み込み**: モデル全体ではなく**`state_dict()`（パラメータの辞書）**を保存・読み込みするのが推奨される方法（`torch.save`/`torch.load`/`load_state_dict()`）。理由はモデル全体を保存するとクラス定義やディレクトリ構造に強く依存し、後でコードを変更すると壊れやすいため。
- **`nn.Parameter`と`nn.Linear`の関係**: 最初は`nn.Parameter()`で重み・バイアスを自前定義するが、実務では`nn.Linear(in_features, out_features)`が同じ計算を内部でやってくれる。

### 重要なコードパターン
```python
class LinearRegressionModel(nn.Module):
    def __init__(self):
        super().__init__()
        self.weights = nn.Parameter(torch.randn(1, dtype=torch.float), requires_grad=True)
        self.bias = nn.Parameter(torch.randn(1, dtype=torch.float), requires_grad=True)

    def forward(self, x: torch.Tensor) -> torch.Tensor:
        return self.weights * x + self.bias
```
`nn.Module`をサブクラス化する最も基本的な形。`__init__`でパラメータを定義し、`forward()`で計算を定義する。

```python
class LinearRegressionModelV2(nn.Module):
    def __init__(self):
        super().__init__()
        self.linear_layer = nn.Linear(in_features=1, out_features=1)

    def forward(self, x: torch.Tensor) -> torch.Tensor:
        return self.linear_layer(x)
```
`nn.Parameter`を自前で書く代わりに`nn.Linear()`を使う、より実務的な書き方。

```python
for epoch in range(epochs):
    model_0.train()
    y_pred = model_0(X_train)          # 1. forward pass
    loss = loss_fn(y_pred, y_train)    # 2. loss計算
    optimizer.zero_grad()              # 3. 勾配リセット
    loss.backward()                    # 4. 逆伝播
    optimizer.step()                   # 5. パラメータ更新

    model_0.eval()
    with torch.inference_mode():
        test_pred = model_0(X_test)
        test_loss = loss_fn(test_pred, y_test)
```
コース全体を通じて何十回も再利用される「PyTorch学習/テストループ」の黄金パターン。

```python
torch.save(obj=model_0.state_dict(), f="models/01_pytorch_workflow_model_0.pth")
model_0.load_state_dict(torch.load(f="models/01_pytorch_workflow_model_0.pth"))
```
state_dict()ベースでのモデル保存・読み込みの定番パターン。

### 押さえておくべきポイント・つまずきやすい点
- 順序のルール: `loss = ...`を`loss.backward()`より前に計算する／`optimizer.zero_grad()`を`loss.backward()`より前に呼ぶ／`optimizer.step()`は`loss.backward()`の後、という順序を守らないと勾配が正しく蓄積・更新されない。
- 勾配（gradient）はデフォルトで蓄積（accumulate）されるため、各ステップの最初に`optimizer.zero_grad()`を呼ばないと前のステップの勾配が混ざってしまう。
- テストループでは`loss.backward()`や`optimizer.step()`を呼ばない（パラメータは更新しない、あくまで評価のみ）。
- モデルとデータを両方同じデバイス（CPU/GPU）に載せないと`RuntimeError`になる。GPU上のテンソルはmatplotlib/NumPy/pandasでは直接使えないため`.cpu()`で戻す必要がある。
- `pickle`ベースの`torch.save`/`torch.load`はセキュリティ上安全でないため、信頼できるソースのモデルファイルのみロードすべき。

---

## 02: PyTorchニューラルネットワーク分類

### そのセクションの目的・学べること
回帰（数値予測）から一歩進んで、**分類問題（classification）**をPyTorchで扱う方法を学ぶセクション。二値分類・多クラス分類のアーキテクチャ設計、ロジット→確率→ラベルという変換の流れ、そして「なぜモデルが学習しないのか」を線形/非線形の観点からデバッグする過程を通して、**非線形活性化関数（ReLUなど）の重要性**を体感するのが最大のポイント。

### 主要な概念
- **分類問題の種類**: 二値分類（binary、選択肢2つ）、多クラス分類（multi-class、選択肢3つ以上、1サンプル1ラベル）、多ラベル分類（multi-label、1サンプルに複数ラベル）。
- **分類ニューラルネットの定番アーキテクチャ**: 入力層の形状=特徴量数、出力層の形状=二値分類なら1、多クラス分類ならクラス数分。隠れ層の活性化は通常ReLU、出力の活性化は二値分類ならSigmoid、多クラス分類ならSoftmax。損失関数は二値分類ならBCE、多クラス分類ならCross Entropy。
- **ロジット（logits）→予測確率→予測ラベルの変換パイプライン**: モデルの生出力（logits）は解釈しづらいので、二値分類は`torch.sigmoid()`で確率化し0.5を境に丸めてラベル化、多クラス分類は`torch.softmax()`で各クラスの確率（合計1）にしてから`torch.argmax()`で最も確率の高いクラスを取る。
- **`nn.BCEWithLogitsLoss()` vs `nn.BCELoss()`**: 前者はSigmoidを内蔵しており数値的に安定するため、一般的に前者が推奨される（モデル側でsigmoidをかける必要がない）。
- **`nn.Sequential`**: 単純に層を順番に並べるだけならクラスをサブクラス化せずに`nn.Sequential(...)`で簡潔にモデルを書ける。ただし単純な順次計算しかできない。
- **underfitting（未学習）**: 円形（非線形）データに対して直線しか引けないモデルは、いくら層を増やしても・長く学習しても精度50%（ランダム推測と同等）から改善しない、という実例を通じて体験する。
- **非線形性（non-linearity）の必要性**: `nn.ReLU()`などの非線形活性化関数を線形層の間に挟むことで、モデルは直線だけでなく曲線的な決定境界を学習できるようになる。「無限の直線と曲線を組み合わせれば、ほぼどんなパターンも描ける」という直感。
- **評価指標**: 損失（wrongさの指標）に加えてaccuracy（rightさの指標）など複数の視点でモデルを評価することが推奨される。より発展的にはprecision, recall, F1-score, confusion matrixなど（`torchmetrics`や`sklearn.metrics`）。

### 重要なコードパターン
```python
class CircleModelV0(nn.Module):
    def __init__(self):
        super().__init__()
        self.layer_1 = nn.Linear(in_features=2, out_features=5)
        self.layer_2 = nn.Linear(in_features=5, out_features=1)
    def forward(self, x):
        return self.layer_2(self.layer_1(x))
```
二値分類の最小構成モデル（隠れ層を挟んだ2層の線形モデル）。この時点ではまだ非線形性がない。

```python
class CircleModelV2(nn.Module):
    def __init__(self):
        super().__init__()
        self.layer_1 = nn.Linear(in_features=2, out_features=10)
        self.layer_2 = nn.Linear(in_features=10, out_features=10)
        self.layer_3 = nn.Linear(in_features=10, out_features=1)
        self.relu = nn.ReLU()
    def forward(self, x):
        return self.layer_3(self.relu(self.layer_2(self.relu(self.layer_1(x)))))
```
ReLUを層の間に挟むことで非線形なデータ（円形データ）にも対応できるようになったモデル。

```python
loss_fn = nn.BCEWithLogitsLoss()  # Sigmoid内蔵、二値分類用
optimizer = torch.optim.SGD(params=model_0.parameters(), lr=0.1)

def accuracy_fn(y_true, y_pred):
    correct = torch.eq(y_true, y_pred).sum().item()
    return (correct / len(y_pred)) * 100
```
二値分類の損失関数・オプティマイザ・自作accuracy関数の定番セット。

```python
class BlobModel(nn.Module):
    def __init__(self, input_features, output_features, hidden_units=8):
        super().__init__()
        self.linear_layer_stack = nn.Sequential(
            nn.Linear(input_features, hidden_units),
            nn.Linear(hidden_units, hidden_units),
            nn.Linear(hidden_units, output_features),
        )
    def forward(self, x):
        return self.linear_layer_stack(x)

loss_fn = nn.CrossEntropyLoss()  # 多クラス分類用
```
多クラス分類のモデルと損失関数。`nn.CrossEntropyLoss()`は内部でsoftmaxを含むため、モデル側にsoftmax層を入れる必要はない。

### 押さえておくべきポイント・つまずきやすい点
- モデルの性能が上がらないとき、「層を増やす」「隠れユニットを増やす」「エポック数を増やす」だけでは解決しないことがある（このセクションの実例そのもの）。データが非線形なら非線形の活性化関数が必要、という原因究明の思考プロセスが重要。
- デバッグの定石として「小さく始める（小さいモデル・小さいデータでまず動くか確認し、overfittingさせてから徐々にスケールアップする）」という方針が紹介されている。
- 多クラス分類では`torch.argmax(y_logits, dim=1)`のように、softmaxを経由せず直接logitsからラベルを取ることも可能（確率値が不要な場合は計算を1ステップ省略できる）。
- 学習ループの構造自体は01と全く同じ（forward→loss→zero_grad→backward→step）。分類特有の違いは「損失関数の選び方」と「logits→ラベルへの変換ステップ」にある。

---

## 03: PyTorchコンピュータビジョン

### そのセクションの目的・学べること
画像データ（FashionMNIST）を題材に、**コンピュータビジョン特有のPyTorchワークフロー**を学ぶセクション。`torchvision`ライブラリ、`DataLoader`によるミニバッチ処理、単純な線形モデルから非線形モデル、そして畳み込みニューラルネットワーク（CNN、`nn.Conv2d`/`nn.MaxPool2d`）へと段階的にモデルを改良し、複数モデルの性能・学習時間を比較しながら、CNNがなぜ画像に強いのかを体感する回。confusion matrixによる評価やモデルの保存・読み込みの実践も含む、01・02の集大成的なセクション。

### 主要な概念
- **`torchvision`の主要モジュール**: `torchvision.datasets`（画像データセット群）、`torchvision.models`（既存の有名アーキテクチャ）、`torchvision.transforms`（画像の前処理・変換）。`Dataset`と`DataLoader`は画像に限らず汎用的に使えるPyTorchの仕組み。
- **画像テンソルの形状**: `[color_channels, height, width]`（CHW形式）。グレースケールなら`color_channels=1`、RGBなら3。バッチを含むと`[batch_size, C, H, W]`（NCHW）になる。PyTorchはNCHWがデフォルトだが、NHWC（channels-last）の方が性能面でベストプラクティスとされる場合もある。
- **`DataLoader`とミニバッチ**: `Dataset`（60,000枚の画像全体）を`batch_size`（例: 32）ごとの小さなイテラブルに分割する仕組み。バッチに分けることで計算効率が上がり、1エポックあたりの勾配降下の回数も増える（バッチごとに1回更新される）。`shuffle=True`で毎エポックデータ順序をシャッフル（学習データのみ、テストデータは通常シャッフル不要）。
- **`nn.Flatten()`**: `[C, H, W]`の画像テンソルを1本の特徴ベクトルに潰す層。`nn.Linear()`はベクトル入力を前提とするため、画像を線形層に通す前に必要。
- **ベースラインモデル→非線形モデル→CNNという段階的比較**: model_0（Flatten+Linear×2）→model_1（同構成+ReLU）→model_2（CNN/TinyVGG）と3つのモデルを作り、精度と学習時間を比較する実験的アプローチ。意外にも非線形化しただけのmodel_1はベースラインより悪化（過学習）し、CNNが最も性能が良かったが学習時間は最長、という**性能とスピードのトレードオフ**を実例で確認する。
- **`nn.Conv2d()`（畳み込み層）**: `in_channels`/`out_channels`/`kernel_size`/`stride`/`padding`というハイパーパラメータを持ち、画像から局所的なパターンを抽出する層。入力は4次元`[N, C, H, W]`が必須（単一画像は`.unsqueeze(dim=0)`でバッチ次元を追加する必要がある）。
- **`nn.MaxPool2d()`（プーリング層）**: 指定した`kernel_size`の領域内の最大値だけを残すことで、テンソルの空間サイズを縮小（圧縮）する層。ニューラルネットの各層は基本的に「情報を圧縮しながらパターンを学習する」という発想。
- **train_step / test_step関数化**: 01・02で毎回書いていた学習/テストループのコードを、再利用可能な関数（`train_step()`, `test_step()`）に切り出すリファクタリング。以降のノートブックでも同様のパターンが使われる。
- **confusion matrix（混同行列）**: `torchmetrics.ConfusionMatrix`と`mlxtend.plotting.plot_confusion_matrix()`を使い、どのクラスとどのクラスを取り違えやすいかを可視化する評価手法。単なるaccuracyより「どこで・なぜ間違えるか」が分かる。

### 重要なコードパターン
```python
train_dataloader = DataLoader(train_data, batch_size=32, shuffle=True)
test_dataloader = DataLoader(test_data, batch_size=32, shuffle=False)
```
DataLoaderでデータセットをミニバッチのイテラブルに変換する定番パターン。

```python
class FashionMNISTModelV0(nn.Module):
    def __init__(self, input_shape, hidden_units, output_shape):
        super().__init__()
        self.layer_stack = nn.Sequential(
            nn.Flatten(),
            nn.Linear(input_shape, hidden_units),
            nn.Linear(hidden_units, output_shape)
        )
    def forward(self, x):
        return self.layer_stack(x)
```
画像分類のベースラインモデル。Flatten→Linear×2というシンプルな構成。

```python
def train_step(model, data_loader, loss_fn, optimizer, accuracy_fn, device):
    model.to(device)
    for X, y in data_loader:
        X, y = X.to(device), y.to(device)
        y_pred = model(X)
        loss = loss_fn(y_pred, y)
        optimizer.zero_grad()
        loss.backward()
        optimizer.step()
```
バッチ単位で回す学習ループを関数化したもの（テスト側は`test_step()`として対になる、`model.eval()`+`torch.inference_mode()`を使用）。

```python
class FashionMNISTModelV2(nn.Module):  # TinyVGGアーキテクチャの再現
    def __init__(self, input_shape, hidden_units, output_shape):
        super().__init__()
        self.block_1 = nn.Sequential(
            nn.Conv2d(input_shape, hidden_units, kernel_size=3, padding=1),
            nn.ReLU(),
            nn.Conv2d(hidden_units, hidden_units, kernel_size=3, padding=1),
            nn.ReLU(),
            nn.MaxPool2d(kernel_size=2, stride=2)
        )
        self.block_2 = nn.Sequential(...)  # 同様の構成をもう1セット
        self.classifier = nn.Sequential(
            nn.Flatten(),
            nn.Linear(hidden_units * 7 * 7, output_shape)
        )
    def forward(self, x):
        return self.classifier(self.block_2(self.block_1(x)))
```
Conv2d+ReLU+MaxPool2dのブロックを重ねるCNN（TinyVGG）の典型構成。最後にFlatten+Linearで分類。

### 押さえておくべきポイント・つまずきやすい点
- `nn.Conv2d()`は4次元テンソル`[N, C, H, W]`を要求するため、単一画像（3次元）をそのまま渡すと`RuntimeError`になる。`unsqueeze(dim=0)`でバッチ次元を追加する必要がある（PyTorch 1.11.0+では自動対応される場合もある）。
- GPUを使っても必ずしも高速化するとは限らない。小さいモデル・小さいデータセットでは、CPU⇔GPU間のデータ転送コストがGPUの計算優位性を上回ることがある（実際にmodel_1のGPU学習はCPUより遅くなった実例が紹介されている）。
- 自作の評価関数（`eval_model()`など）を書くときも、モデルやデータと同じ`device`に明示的に載せないと「一部の演算がcuda、一部がcpu」という`RuntimeError`が起きる（device-agnosticはモデル・データだけでなく評価関数側にも徹底する必要がある）。
- 非線形化（ReLU追加）しても必ず性能が上がるとは限らない例（model_1がベースラインより悪化＝overfitting気味）が示されており、「仮説通りに動くとは限らない、実験して確かめる」という機械学習の経験主義的な姿勢が強調されている。
- `nn.Conv2d`の`in_features`（Linear層への入力サイズ）は、Conv/Pooling層を経た後の特徴マップのH×W×チャンネル数から逆算する必要があり（例: `hidden_units*7*7`）、ここでのshape計算ミスも頻出のつまずきポイント。

---

## 04: カスタムデータセット (04_pytorch_custom_datasets.ipynb)

### 1. 目的・学べること
このセクションでは、PyTorch組み込みのデータセットではなく、自前の画像データ(pizza・steak・sushiの3クラス、Food101データセットのサブセット)を読み込んでモデルを訓練する方法を学ぶ。データフォルダの構造把握から`Dataset`/`DataLoader`の作成、独自`Dataset`クラスの実装、データ拡張(data augmentation)、TinyVGGモデルでの訓練・評価、そして訓練済みモデルで自分の画像に予測をかけるところまで、画像分類の一連のワークフローをカバーする。

### 2. 主要な概念
- **標準的な画像分類フォーマット**: `train/<class_name>/*.jpg`, `test/<class_name>/*.jpg` のようにクラス名をフォルダ名にする構造(ImageNetなどでも一般的)
- **`os.walk()`** でデータディレクトリを探索し、各サブフォルダのファイル数を確認する("データと一体化する"ステップ)
- **`torchvision.transforms`**: `Resize()`, `RandomHorizontalFlip()`, `ToTensor()` を `Compose()` でまとめて画像→テンソル変換パイプラインを作る
- **`torchvision.datasets.ImageFolder`**: 標準フォーマットのフォルダから自動的に`Dataset`を作る組み込み機能(オプション1)
- **独自`Dataset`のサブクラス化**(オプション2): `torch.utils.data.Dataset`を継承し、`__init__`, `__len__`, `__getitem__`を実装して`ImageFolder`相当の機能を自作する
- **`os.scandir()`を使ったクラス名検出**(`find_classes()`ヘルパー関数)
- **データ拡張(data augmentation)**: `transforms.TrivialAugmentWide(num_magnitude_bins=31)` を使い、訓練データの多様性を人工的に増やして汎化性能を上げる(テストデータには適用しない)
- **TinyVGGモデル**(CNN Explainerと同じアーキテクチャ、`in_channels=3`のRGB版)と、`train_step()`/`test_step()`/`train()`という訓練・評価ループの関数化パターン
- **損失曲線(loss curves)**によるoverfitting(過学習)/underfitting(学習不足)の診断と対処法(データ追加、モデル簡略化、data augmentation、transfer learning、dropout、learning rate decay、early stoppingなど)
- **カスタム画像への推論時の3大エラー**: dtype不一致(`uint8` vs `float32`)、device不一致(cpu vs cuda)、shape不一致(バッチ次元の欠落、`unsqueeze(dim=0)`で解決)

### 3. 重要なコードパターン

**os.walkでデータ構造を確認**
```python
import os
def walk_through_dir(dir_path):
  for dirpath, dirnames, filenames in os.walk(dir_path):
    print(f"There are {len(dirnames)} directories and {len(filenames)} images in '{dirpath}'.")
```

**ImageFolderで簡単にDatasetを作る(オプション1)**
```python
from torchvision import datasets
train_data = datasets.ImageFolder(root=train_dir, transform=data_transform, target_transform=None)
test_data = datasets.ImageFolder(root=test_dir, transform=data_transform)
```

**独自Datasetクラスのスケルトン(オプション2、ImageFolder相当を自作)**
```python
from torch.utils.data import Dataset

class ImageFolderCustom(Dataset):
    def __init__(self, targ_dir: str, transform=None) -> None:
        self.paths = list(pathlib.Path(targ_dir).glob("*/*.jpg"))
        self.transform = transform
        self.classes, self.class_to_idx = find_classes(targ_dir)

    def load_image(self, index: int) -> Image.Image:
        image_path = self.paths[index]
        return Image.open(image_path)

    def __len__(self) -> int:
        return len(self.paths)

    def __getitem__(self, index: int) -> Tuple[torch.Tensor, int]:
        img = self.load_image(index)
        class_name = self.paths[index].parent.name
        class_idx = self.class_to_idx[class_name]
        if self.transform:
            return self.transform(img), class_idx
        else:
            return img, class_idx
```

**データ拡張ありの訓練用transform**
```python
train_transforms = transforms.Compose([
    transforms.Resize((224, 224)),
    transforms.TrivialAugmentWide(num_magnitude_bins=31),
    transforms.ToTensor()
])
# テスト用は拡張なし
test_transforms = transforms.Compose([
    transforms.Resize((224, 224)),
    transforms.ToTensor()
])
```

**カスタム画像で予測する際に必須の3ステップ(dtype→device→shape)**
```python
custom_image = custom_image.type(torch.float32) / 255.  # dtype修正(値も[0,1]に)
custom_image_transformed = custom_image_transform(custom_image).to(device)  # deviceに送る
model.eval()
with torch.inference_mode():
    pred = model(custom_image_transformed.unsqueeze(dim=0))  # バッチ次元を追加
```

### 4. 押さえておくべきポイント・つまずきやすい点
- **まず「データと一体化する」こと**(become one with the data)。モデルを作る前にデータの構造・件数・見た目を必ず確認する習慣を強調している。
- 小さく始めて増やす(start small, increase when necessary): 101クラス・全データではなく3クラス・10%のサブセットから始める。
- 独自`Dataset`は柔軟だが**コード量が増えバグの温床になりやすい**というトレードオフがある。
- `DataLoader`の`num_workers`は通常`os.cpu_count()`に設定するとよい。
- テストデータにはdata augmentationを適用しない(訓練データの多様性のみを増やすのが目的)。ただしリサイズ・テンソル化は必須。
- 本ノートブックのTinyVGGモデル(model_0, model_1)は**どちらも性能が悪い**(過学習・学習不足が起きている)ことを意図的に見せており、これが次の05・06章(モジュール化・転移学習)への伏線になっている。
- カスタム画像で推論する際は「**dtype・device・shape**」の3点が典型的なエラー原因になることを繰り返し強調(この3点は06章でも同様に登場する)。

---

## 05: モジュール化 (05_pytorch_going_modular.md + going_modular/going_modular/*.py)

### 1. 目的・学べること
このセクションは「ノートブックのコードをどうやってPythonスクリプトに変換するか」という問いに答える。04章で書いたコードを、`data_setup.py`, `model_builder.py`, `engine.py`, `utils.py`, `train.py`という一連の再利用可能な`.py`ファイルに分割し、コマンドライン一行(`python train.py`)でモデルを訓練できる状態を目指す。ノートブックとスクリプトそれぞれのメリット・デメリットも議論する。

### 2. 主要な概念
- **モジュール化(going modular)**: ノートブックの有用なコードセルを、目的別の複数の`.py`ファイルに切り分けること
- **ノートブック vs Pythonスクリプトのトレードオフ**: ノートブックは実験・共有・可視化に強いがバージョン管理や部分利用が苦手。スクリプトはgitでの管理・パッケージ化・クラウド実行に強いが視覚的な実験がしにくい
- **典型的なファイル構成**:
  - `data_setup.py` — データ準備・`Dataset`/`DataLoader`作成(`create_dataloaders()`関数)
  - `model_builder.py` — モデルクラス定義(`TinyVGG`)
  - `engine.py` — 訓練・評価ループ(`train_step()`, `test_step()`, `train()`)
  - `utils.py` — 補助関数(`save_model()`など)
  - `train.py` — 上記すべてを組み合わせてモデルを訓練するエントリーポイント
- **セルモード vs スクリプトモード**: `05_pytorch_going_modular_cell_mode.ipynb`(通常のノートブック)と`05_pytorch_going_modular_script_mode.ipynb`(`%%writefile`でセルの内容を`.py`ファイルに書き出す)の2つのノートブックで学ぶ
- **`%%writefile going_modular/data_setup.py`**のようなJupyterマジックコマンドでノートブックセルから直接スクリプトファイルを生成する手法
- **Google docstringスタイル**での関数ドキュメント化、**スクリプト冒頭でのimport集約**という規約
- **`argparse`**によるコマンドラインからのハイパーパラメータ設定(`python train.py --model MODEL_NAME --batch_size BATCH_SIZE --lr LEARNING_RATE --num_epochs NUM_EPOCHS`のような呼び出し方。※実際にリポジトリに入っている`train.py`自体はハードコードされた定数を使っており、`argparse`化は演習問題として読者に委ねられている)

### 3. 重要なコードパターン

**data_setup.py の中心関数**
```python
def create_dataloaders(
    train_dir: str, test_dir: str, transform: transforms.Compose,
    batch_size: int, num_workers: int = NUM_WORKERS
):
  train_data = datasets.ImageFolder(train_dir, transform=transform)
  test_data = datasets.ImageFolder(test_dir, transform=transform)
  class_names = train_data.classes
  train_dataloader = DataLoader(train_data, batch_size=batch_size, shuffle=True,
                                 num_workers=num_workers, pin_memory=True)
  test_dataloader = DataLoader(test_data, batch_size=batch_size, shuffle=False,
                                num_workers=num_workers, pin_memory=True)
  return train_dataloader, test_dataloader, class_names
```

**ディレクトリ構成(going_modular/going_modular/配下、実際にリポジトリにあるファイル)**
```
going_modular/
├── going_modular/
│   ├── data_setup.py
│   ├── engine.py        # train_step(), test_step(), train()
│   ├── model_builder.py # TinyVGGクラス
│   ├── train.py         # 上記すべてを結合するエントリーポイント
│   ├── utils.py         # save_model()
│   └── predictions.py   # (追加)推論用ユーティリティ、06章由来のpred_and_plot_image()
├── models/
└── data/pizza_steak_sushi/{train,test}/{pizza,steak,sushi}/
```

**train.py の全体像(各スクリプトを import して結合)**
```python
import os, torch
import data_setup, engine, model_builder, utils
from torchvision import transforms

NUM_EPOCHS = 5; BATCH_SIZE = 32; HIDDEN_UNITS = 10; LEARNING_RATE = 0.001
train_dir = "data/pizza_steak_sushi/train"
test_dir = "data/pizza_steak_sushi/test"
device = "cuda" if torch.cuda.is_available() else "cpu"

data_transform = transforms.Compose([transforms.Resize((64, 64)), transforms.ToTensor()])
train_dataloader, test_dataloader, class_names = data_setup.create_dataloaders(
    train_dir=train_dir, test_dir=test_dir, transform=data_transform, batch_size=BATCH_SIZE)

model = model_builder.TinyVGG(input_shape=3, hidden_units=HIDDEN_UNITS,
                               output_shape=len(class_names)).to(device)
loss_fn = torch.nn.CrossEntropyLoss()
optimizer = torch.optim.Adam(model.parameters(), lr=LEARNING_RATE)

engine.train(model=model, train_dataloader=train_dataloader, test_dataloader=test_dataloader,
             loss_fn=loss_fn, optimizer=optimizer, epochs=NUM_EPOCHS, device=device)

utils.save_model(model=model, target_dir="models",
                  model_name="05_going_modular_script_mode_tinyvgg_model.pth")
```

**model_builder.py 内から呼び出す例(6章で実際に使われる)**
```python
from going_modular import model_builder
model = model_builder.TinyVGG(input_shape=3, hidden_units=10,
                               output_shape=len(class_names)).to(device)
```

### 4. 押さえておくべきポイント・つまずきやすい点
- `train.py`自体は`going_modular`ディレクトリの**内側**にあるため、他スクリプトのimportは`from going_modular import ...`ではなく`import data_setup, engine, model_builder, utils`のように相対的に書く(ディレクトリ内から呼ぶか外から呼ぶかでimportの書き方が変わる点に注意)。
- ファイル名・レイアウトは一例であり、プロジェクトの要件に応じて自由に変えてよい(`model.py`でも可、など)。
- 実際にリポジトリに含まれる`train.py`はまだ`argparse`化されておらず、ハイパーパラメータはファイル冒頭の定数として直書きされている。CLIから可変にするのは演習(exercise 2)として提示されている。
- 演習として、データダウンロードを`get_data.py`に切り出す、予測用の`predict.py`を作る、といった発展課題も用意されている(`predictions.py`が実際のリポジトリに存在し、06章の`pred_and_plot_image()`がベースになっている)。
- モジュール化の目的は「同じコードを何度も書き直さない」こと。以降の06章以降ではこの`going_modular`パッケージ(`data_setup.py`, `engine.py`)をそのままimportして再利用していく。

---

## 06: 転移学習 (06_pytorch_transfer_learning.ipynb)

### 1. 目的・学べること
04・05章で自作したTinyVGGモデルの性能が低かったことを受け、**転移学習(transfer learning)**を使ってImageNetで事前学習済みのモデル(`efficientnet_b0`)を取得し、出力層(classifier)だけを付け替えて pizza/steak/sushi の3クラス分類に転用する。05章で作った`going_modular`パッケージ(`data_setup.py`, `engine.py`)を再利用しながら、少ないデータ・短い訓練時間で大幅な精度向上(TinyVGGの2倍近い約85%)を達成する過程を学ぶ。

### 2. 主要な概念
- **転移学習(transfer learning)**: 別の問題(ImageNetの大規模画像分類)で学習された重み(パターン)を、自分の問題に流用する手法。少ないカスタムデータでも高い精度が得られやすい
- **事前学習済みモデルの入手先**: `torchvision.models`, HuggingFace Hub, `timm`ライブラリ, Papers with Code など
- **`torchvision`の新しい multi-weight API (v0.13+)**: `weights = torchvision.models.EfficientNet_B0_Weights.DEFAULT` のように重みをEnumとして指定し、`torchvision.models.efficientnet_b0(weights=weights)` でモデルを構築する(旧`pretrained=True`方式は非推奨)
- **transformの自動生成**: `weights.transforms()` を呼ぶことで、そのモデルが訓練時に使ったのと**同じ**前処理(リサイズ・正規化のmean/std等)を自動で取得できる(手動で`transforms.Normalize(mean=[0.485,0.456,0.406], std=[0.229,0.224,0.225])`等を組む「manual creation」との対比)
- **モデルの3つのパート**: `features`(畳み込み層=特徴抽出器)、`avgpool`(特徴ベクトルへの集約)、`classifier`(出力層)
- **base layersの凍結(freezing)**: `param.requires_grad = False` を`features`の全パラメータに設定し、勾配計算・更新の対象から外す(訓練対象パラメータが528万→3,843個まで激減し、訓練が高速化する)
- **classifier(出力層)の付け替え**: ImageNet用の`out_features=1000`から、自分のクラス数(3)に合わせた新しい`nn.Sequential`に置き換える
- **feature extraction vs fine-tuning**: 本章で扱うのは「base layersを完全凍結し出力層のみ訓練する」feature extraction方式。fine-tuning(base layersも一部/全部再学習する手法、データが多い場合に有効)はExtra-curriculumとして触れられるのみ
- **`torchinfo.summary()`**でTrainable列を見て、どの層が凍結/訓練可能かを確認する

### 3. 重要なコードパターン

**事前学習済み重み・モデルの取得と自動transform**
```python
weights = torchvision.models.EfficientNet_B0_Weights.DEFAULT  # ImageNetでの最良の重み
model = torchvision.models.efficientnet_b0(weights=weights).to(device)

auto_transforms = weights.transforms()  # このモデルの訓練時と同じ前処理を自動取得
```

**base layers(features)を凍結**
```python
for param in model.features.parameters():
    param.requires_grad = False
```

**classifier(出力層)を自分のクラス数に付け替え**
```python
torch.manual_seed(42)
output_shape = len(class_names)  # 3 (pizza, steak, sushi)

model.classifier = torch.nn.Sequential(
    torch.nn.Dropout(p=0.2, inplace=True),
    torch.nn.Linear(in_features=1280, out_features=output_shape, bias=True)
).to(device)
```

**05章で作ったengine.py/data_setup.pyをそのまま再利用して訓練**
```python
from going_modular import data_setup, engine

train_dataloader, test_dataloader, class_names = data_setup.create_dataloaders(
    train_dir=train_dir, test_dir=test_dir, transform=auto_transforms, batch_size=32)

loss_fn = torch.nn.CrossEntropyLoss()
optimizer = torch.optim.Adam(model.parameters(), lr=0.001)

results = engine.train(model=model, train_dataloader=train_dataloader,
                        test_dataloader=test_dataloader, optimizer=optimizer,
                        loss_fn=loss_fn, epochs=5, device=device)
```

### 4. 押さえておくべきポイント・つまずきやすい点
- **「まず既存の良いモデルがないか探す」**のが良い習慣(問題に取り組む最初の一手として推奨)。
- 事前学習済みモデルを使う際は**カスタムデータをそのモデルの訓練時と同じ形式(リサイズ・正規化)に前処理することが必須**。これを誤ると性能が大きく劣化する。`weights.transforms()`による自動生成がこのミスを防ぐ簡単な方法。
- モデル名の数字が大きいほど(`efficientnet_b0`→`b7`)一般に性能は上がるがモデルサイズ・計算コストも増える。デバイス制約(モバイル等)とのトレードオフを考慮する必要がある。特定のアーキテクチャに固執せず実験を重ねることが推奨されている。
- 凍結すると訓練対象パラメータが大幅に減り(この例では528万→3,843個)、**訓練が非常に高速**になる(著者のGPUで約5秒、Colabで約15秒/5エポック)。これが転移学習の大きな利点の一つ。
- 04章と同様に、カスタム画像で推論する際は**shape・dtype・deviceを訓練データと揃える**必要がある、という同じ3点の注意が繰り返し強調される。
- 本章のEfficientNet_B0モデルは訓練データを5エポックしか回していないのに、TinyVGG(04章)よりはるかに高精度(約85%)。ただし依然として過学習/学習不足の余地があり、より長いエポック数やより多いデータでの実験が演習として提案されている。
- Extra-curriculumとして「fine-tuning」(base layersも訓練対象に含める手法)についての調査が課題になっている。一般に、カスタムデータが多い場合はfine-tuningが、少ない場合はfeature extraction(本章の手法)がより効果的とされる。

---

## 07: 実験管理 (07_pytorch_experiment_tracking.ipynb)

### 目的
複数のモデル・データ量・エポック数を変えながら何度も学習を回すようになると、「どの実験がどんな設定でどんな結果だったか」を記録・比較する仕組みが必要になる。本セクションでは`torch.utils.tensorboard.SummaryWriter`を使って学習過程をTensorBoard形式でログに残し、EffNetB0/EffNetB2 × 10%/20%データ × 5/10エポックという**8パターンの実験グリッドサーチ**を自動で回し、TensorBoard上で結果を比較して最良モデルを選ぶところまでを学ぶ。

### 主要な概念
- **実験管理(experiment tracking)**: 学習を回すたびにハイパーパラメータと結果(loss/accuracy)を記録し、後から比較できるようにする仕組み。実験数が増えるほど「どれが一番良かったか」を目視で追うのは非現実的になる
- **`SummaryWriter`**: PyTorch組み込みのロガー。デフォルトでは`runs/CURRENT_DATETIME_HOSTNAME`ディレクトリにログを書き出す。`writer.add_scalars(main_tag, tag_scalar_dict, global_step)`でtrain/testのloss・accuracyをまとめて記録できる
- **`train()`関数への`writer`パラメータ追加**: `engine.py`の`train()`に`writer: torch.utils.tensorboard.SummaryWriter`を渡せるようにし、各epochの結果を`writer`があれば記録、エポック終了後に`writer.close()`を呼ぶ
- **`create_writer()`ヘルパー関数**: `runs/YYYY-MM-DD/experiment_name/model_name/extra`という規則的なディレクトリ構造でログを吐かせるための自作ヘルパー。実験日付・実験名・モデル名・追加情報の4要素を含めることで後から実験を追跡しやすくする
- **実験のスケールアップ**: 「モデル(EffNetB0 vs EffNetB2)」「データ量(10% vs 20%のpizza/steak/sushi)」「エポック数(5 vs 10)」の3軸・2水準ずつ = 2×2×2 = **8実験**を三重forループで自動実行する
- **TensorBoardでの可視化**: Jupyter/Colab上で`%load_ext tensorboard`→`%tensorboard --logdir runs`、またはターミナルで`tensorboard --logdir=runs`を実行し、ブラウザで各実験のtrain/test loss・accuracy曲線を重ねて比較する
- **モデル選定の考え方**: 「実験、実験、実験」の精神で幅広く試し、TensorBoard上でtest lossが最も低い(かつtest accuracyが高い)実験を選ぶ。パラメータ数・データ量・エポック数のどれもある程度多い方が有利という一般的傾向(The Bitter Lessonへの言及)が観察される

### 重要なコードパターン

**writerを組み込んだtrain()関数**
```python
def train(model, train_dataloader, test_dataloader, optimizer, loss_fn,
          epochs, device, writer: torch.utils.tensorboard.SummaryWriter = None):
    results = {"train_loss": [], "train_acc": [], "test_loss": [], "test_acc": []}
    for epoch in range(epochs):
        train_loss, train_acc = train_step(model, train_dataloader, loss_fn, optimizer, device)
        test_loss, test_acc = test_step(model, test_dataloader, loss_fn, device)

        if writer:
            writer.add_scalars(main_tag="Loss",
                                tag_scalar_dict={"train_loss": train_loss, "test_loss": test_loss},
                                global_step=epoch)
            writer.add_scalars(main_tag="Accuracy",
                                tag_scalar_dict={"train_acc": train_acc, "test_acc": test_acc},
                                global_step=epoch)
    if writer:
        writer.close()
    return results
```

**runs/YYYY-MM-DD/experiment_name/model_name/extra 形式のcreate_writer()**
```python
import os
from datetime import datetime

def create_writer(experiment_name: str, model_name: str, extra: str = None):
    timestamp = datetime.now().strftime("%Y-%m-%d")
    if extra:
        log_dir = os.path.join("runs", timestamp, experiment_name, model_name, extra)
    else:
        log_dir = os.path.join("runs", timestamp, experiment_name, model_name)
    print(f"[INFO] Created SummaryWriter, saving to: {log_dir}...")
    return torch.utils.tensorboard.SummaryWriter(log_dir=log_dir)
```

**8実験を回す三重forループ(モデル×データ量×エポック数)**
```python
%%time
experiment_number = 0
for dataloader_name, train_dataloader in train_dataloaders.items():
    for epochs in num_epochs:
        for model_name in models:
            experiment_number += 1
            print(f"[INFO] Experiment number: {experiment_number}")
            print(f"[INFO] Model: {model_name}")
            print(f"[INFO] DataLoader: {dataloader_name}")
            print(f"[INFO] Number of epochs: {epochs}")

            model = create_effnetb0() if model_name == "effnetb0" else create_effnetb2()
            loss_fn = torch.nn.CrossEntropyLoss()
            optimizer = torch.optim.Adam(params=model.parameters(), lr=0.001)

            train(model=model, train_dataloader=train_dataloader,
                  test_dataloader=test_dataloader, optimizer=optimizer,
                  loss_fn=loss_fn, epochs=epochs, device=device,
                  writer=create_writer(experiment_name=dataloader_name,
                                        model_name=model_name,
                                        extra=f"{epochs}_epochs"))

            save_filepath = f"07_{model_name}_{dataloader_name}_{epochs}_epochs.pth"
            save_model(model=model, target_dir="models", model_name=save_filepath)
```

**TensorBoardの起動(Jupyter/Colab)**
```python
%load_ext tensorboard
%tensorboard --logdir runs
```

### つまずきやすい点
- 各実験ごとに**モデルインスタンスを新規に作り直す**必要がある(同じインスタンスを使い回すと前の実験の重みが残ったまま学習が続いてしまい、公平な比較にならない)。
- `writer`は実験ごとに別インスタンスを渡す。1つの`SummaryWriter`を使い回すと全実験のログが同じグラフに混在してしまう。
- `create_writer()`のディレクトリ規則(`runs/日付/実験名/モデル名/追加情報`)を決めておかないと、後からTensorBoardで「どれがどの実験か」を追うのが困難になる。
- TensorBoardで比較する際は、**test loss(汎化性能)を最優先**で見るべきで、train lossだけが低いモデルは過学習している可能性がある。
- 8実験は環境によっては数分〜十数分かかる。`%%time`マジックで所要時間を確認しつつ、まずは小さいエポック数で動作確認してから本番実行するのが安全。
- 最終的に最良モデルを選んだあとは、必ずそのモデルを`state_dict()`から再ロードして**カスタム画像での定性評価(visualize, visualize, visualize)**を行い、数値だけでなく実際の予測結果も確認する。

---

## 08: 論文の再現実装 / Vision Transformer (08_pytorch_paper_replicating.ipynb)

### 目的
"[An Image is Worth 16x16 Words: Transformers for Image Recognition at Scale](https://arxiv.org/abs/2010.11929)"(ViT論文)を読み解き、そのアーキテクチャをPyTorchで**ゼロから再現実装**する。パッチ埋め込み・クラストークン・位置埋め込み・Multi-Head Self-Attentionブロック・MLPブロック・Transformer Encoderブロックという構成要素を1つずつ組み立て、最終的に完全なViTモデルを作る。その上で、ゼロから訓練したViTと`torchvision`の事前学習済み`vit_b_16`を比較し、「論文の再現実装は勉強になるが、実務では事前学習済みモデルを転移学習する方が圧倒的に効率的」という結論に至る回。

### 主要な概念
- **機械学習論文を読む・再現する能力**: 研究論文の図(architecture diagram)や数式(equations)、実装詳細の表(Table 1など)を、実際に動くコードに落とし込む一連のワークフロー。「まずは全体を俯瞰し、次に図を分解し、最後に各コンポーネントを実装する」という進め方
- **ViTの全体アーキテクチャ**: 画像を固定サイズのパッチに分割 → 各パッチを線形射影(embedding)してトークン列にする → 学習可能な`class`トークンを先頭に結合 → 位置埋め込み(position embedding)を加算 → Transformer Encoderをスタック → `class`トークンの出力をMLP Headに通してクラス分類、という流れ
- **パッチ埋め込み(Patch Embedding)**: 画像を`patch_size`(例: 16×16)ごとのパッチに分割し埋め込みベクトルに変換する処理。これは`kernel_size=patch_size, stride=patch_size`に設定した`nn.Conv2d()`1層で「パッチ分割+線形射影」を同時に実現できる(論文でいう"Linear Projection of Flattened Patches")
- **クラストークン(class token)**: `nn.Parameter`として作られる学習可能な埋め込みベクトルで、パッチ埋め込み列の先頭に`torch.cat()`で結合される。この位置の最終出力が画像全体を表す特徴としてMLP Headに渡される(BERTの`[CLS]`トークンと同じ発想)
- **位置埋め込み(position embedding)**: パッチには元々「順序」の概念がないTransformerに、各パッチの空間的な位置情報を教えるための学習可能な`nn.Parameter`。パッチ埋め込み(+class token)に単純に**加算**される
- **Multi-Head Self-Attention (MSA) ブロック**: `torch.nn.LayerNorm`の後に`torch.nn.MultiheadAttention`を適用するブロック。Query=Key=Valueすべて同じ入力から作る(self-attention)
- **MLPブロック**: `LayerNorm` → `Linear` → `GELU` → `Dropout` → `Linear` → `Dropout`という構成(論文のTable 1・3に準拠、隠れ層は入力の4倍程度のサイズ)
- **Transformer Encoderブロック**: MSAブロックとMLPブロックをそれぞれ**残差接続(residual/skip connection)**で挟んで直列に積んだもの。`torch.nn.TransformerEncoderLayer`という組み込みクラスで代用することも可能
- **ハイパーパラメータのモデルサイズ別表(Table 1)**: ViT-Base/Large/Hugeなど、`patch_size`・`hidden_size(embedding dim)`・`層数`・`head数`・`MLPサイズ`の組み合わせがモデルサイズを決める(本ノートではViT-Baseを再現)
- **ゼロから訓練したViT vs 事前学習済み`vit_b_16`の比較**: 論文のViTは**膨大なデータ(JFT-300M等)と計算資源**を前提としており、pizza/steak/sushiの小さいデータセットからゼロで訓練すると性能が全く出ない(ランダムに近い)。一方`torchvision.models.vit_b_16(weights=ViT_B_16_Weights.DEFAULT)`を使った転移学習は、06章のEfficientNetと同様にわずかなエポックで高精度に到達する

### 重要なコードパターン

**Conv2dでパッチ埋め込みを一発で実現するPatchEmbeddingクラス**
```python
class PatchEmbedding(nn.Module):
    def __init__(self, in_channels=3, patch_size=16, embedding_dim=768):
        super().__init__()
        self.patcher = nn.Conv2d(in_channels=in_channels, out_channels=embedding_dim,
                                  kernel_size=patch_size, stride=patch_size, padding=0)
        self.flatten = nn.Flatten(start_dim=2, end_dim=3)

    def forward(self, x):
        x_patched = self.patcher(x)              # [batch, embedding_dim, H/P, W/P]
        x_flattened = self.flatten(x_patched)     # [batch, embedding_dim, num_patches]
        return x_flattened.permute(0, 2, 1)       # [batch, num_patches, embedding_dim]
```

**class token・position embeddingの用意と結合**
```python
class_token = nn.Parameter(torch.randn(batch_size, 1, embedding_dim), requires_grad=True)
patch_embedding_with_class_token = torch.cat((class_token, patch_embedding), dim=1)

position_embedding = nn.Parameter(
    torch.randn(1, num_patches + 1, embedding_dim), requires_grad=True)
patch_and_position_embedding = patch_embedding_with_class_token + position_embedding
```

**MSAブロック・MLPブロック・TransformerEncoderブロック**
```python
class MultiheadSelfAttentionBlock(nn.Module):
    def __init__(self, embedding_dim=768, num_heads=12, attn_dropout=0):
        super().__init__()
        self.layer_norm = nn.LayerNorm(normalized_shape=embedding_dim)
        self.multihead_attn = nn.MultiheadAttention(embed_dim=embedding_dim,
                                                      num_heads=num_heads,
                                                      dropout=attn_dropout,
                                                      batch_first=True)
    def forward(self, x):
        x = self.layer_norm(x)
        attn_output, _ = self.multihead_attn(query=x, key=x, value=x, need_weights=False)
        return attn_output

class MLPBlock(nn.Module):
    def __init__(self, embedding_dim=768, mlp_size=3072, dropout=0.1):
        super().__init__()
        self.layer_norm = nn.LayerNorm(normalized_shape=embedding_dim)
        self.mlp = nn.Sequential(
            nn.Linear(embedding_dim, mlp_size), nn.GELU(), nn.Dropout(dropout),
            nn.Linear(mlp_size, embedding_dim), nn.Dropout(dropout))
    def forward(self, x):
        return self.mlp(self.layer_norm(x))

class TransformerEncoderBlock(nn.Module):
    def __init__(self, embedding_dim=768, num_heads=12, mlp_size=3072, mlp_dropout=0.1):
        super().__init__()
        self.msa_block = MultiheadSelfAttentionBlock(embedding_dim, num_heads)
        self.mlp_block = MLPBlock(embedding_dim, mlp_size, mlp_dropout)
    def forward(self, x):
        x = self.msa_block(x) + x   # 残差接続
        x = self.mlp_block(x) + x   # 残差接続
        return x
```

**事前学習済みvit_b_16との比較(転移学習)**
```python
pretrained_vit_weights = torchvision.models.ViT_B_16_Weights.DEFAULT
pretrained_vit = torchvision.models.vit_b_16(weights=pretrained_vit_weights).to(device)

for parameter in pretrained_vit.parameters():
    parameter.requires_grad = False

pretrained_vit.heads = nn.Linear(in_features=768, out_features=len(class_names)).to(device)
pretrained_vit_transforms = pretrained_vit_weights.transforms()
```

### つまずきやすい点
- 論文を再現実装する際は**shapeを常に確認しながら**進めるのが鉄則。`[batch_size, num_patches, embedding_dim]`のような形状がどこで変わるかを1層ごとに`print()`やコメントで追わないとミスに気づきにくい。
- `nn.MultiheadAttention`はデフォルトで`batch_first=False`(`[seq, batch, dim]`の順)なので、`batch_first=True`を明示しないと`[batch, seq, dim]`前提の他コードとshapeが合わなくなる。
- `class_token`と`position_embedding`は**学習可能な`nn.Parameter`**として定義する必要があり、単なる`torch.randn()`のテンソルのままだと勾配が伝わらず学習されない。
- **ゼロから訓練したViTはpizza/steak/sushiのような小さいデータセットでは全く実用的な性能が出ない**(論文自体、大規模データセットでの事前学習が前提のアーキテクチャであるため)。これは失敗ではなく「小さいデータではCNNの帰納バイアスに分があり、Transformer系は大規模データでこそ真価を発揮する」という論文の主張どおりの結果である。
- 逆に事前学習済み`vit_b_16`を使った転移学習は、06章のEfficientNetと同様にわずか数エポックで高精度に到達する。「アーキテクチャを一から実装できること」と「実務でどちらを使うべきか」は別の話であり、実務では基本的に事前学習済みモデルの転移学習が第一選択になる。
- `torch.nn.TransformerEncoderLayer` + `torch.nn.TransformerEncoder`というPyTorch組み込みクラスを使えば、MSA/MLPブロックを自作せずに同等のEncoderを数行で構築できる(演習として比較が推奨されている)。

---

## 09: モデルのデプロイ (09_pytorch_model_deployment.ipynb)

### 目的
これまで学んできたモデル構築・訓練・評価のスキルを、**実際に人が使えるアプリとして世に出す**段階に進める。FoodVision Mini(pizza/steak/sushiの3クラス分類)をGradioでデモアプリ化し、Hugging Face Spacesにデプロイする。さらにEffNetB2とViTという2つの候補モデルを「速度 vs 性能」のトレードオフで比較し、デプロイ先に適したモデルを選ぶ判断プロセスや、3クラスのFoodVision Miniを101クラスのFoodVision Bigへスケールアップする流れも扱う。

### 主要な概念
- **デプロイ先の選択肢**: **on-device(エッジ)**(スマホ・組み込み機器上で推論、低レイテンシ・オフライン対応だが計算資源が限られる) vs **cloud**(サーバー上で推論、計算資源は豊富だがネットワーク遅延・コストが発生)。Hugging Face SpacesはCPU/GPUベースのクラウドデプロイの一例
- **online(リアルタイム) vs offline(バッチ)推論**: onlineはリクエストごとに即座に1件ずつ予測を返す(レイテンシ重視、例: 写真を撮った瞬間に分類するアプリ)。offline/batchは大量のデータをまとめて一括処理する(スループット重視、レイテンシ制約が緩い代わりに大量データを効率よく捌く)
- **モデル選定における速度と性能のトレードオフ**: EffNetB2(パラメータ数が少なく高速、精度はやや控えめ)とViT(パラメータ数が多く高精度になりやすいが推論が重い)を比較し、**「デプロイ先での使い勝手(速さ)」を優先してEffNetB2を採用**するという意思決定プロセス。精度の差がわずかであれば、軽量・高速なモデルの方がユーザー体験上優れることが多い
- **Gradioによるデモアプリ化**: `predict()`関数(画像を受け取り前処理→モデル推論→クラスごとの確率辞書と推論時間を返す)を`gradio.Interface(fn, inputs, outputs)`に渡すことで、コード数行でWeb UIを持つデモが作れる
- **推論時間の計測**: `timeit.default_timer()`で推論前後の時刻差を取り、モデルの応答速度(レイテンシ)を定量的に比較する
- **FoodVision Mini → FoodVision Big へのスケールアップ**: 同じEffNetB2アーキテクチャの`classifier`出力を`num_classes=3`から`num_classes=101`(Food101全クラス)に変え、`torchvision.datasets.Food101`のデータで再訓練する。モデル構造・訓練パイプラインは変えず「データとクラス数だけ」を拡張する典型例
- **Hugging Face Spacesへのデプロイ手順**: Spaceを新規作成 → ローカルに`git clone`(Hugging Face Spacesはgitリポジトリとして扱える) → アプリ用ファイル(`app.py`, `model.py`, `requirements.txt`, モデルの`.pth`ファイル, `examples/`)を配置 → 10MBを超える大きいファイル(学習済みモデルなど)は**Git LFS**(`git lfs install`, `git lfs track "*.pth"`)で追跡 → `git add` → `git commit` → `git push`でデプロイが自動的に走る

### 重要なコードパターン

**Gradio用predict()関数(画像→クラス確率辞書＋推論時間)**
```python
from timeit import default_timer as timer
from typing import Tuple, Dict

def predict(img) -> Tuple[Dict, float]:
    start_time = timer()

    img = effnetb2_transforms(img).unsqueeze(0)  # バッチ次元を追加

    effnetb2.eval()
    with torch.inference_mode():
        pred_probs = torch.softmax(effnetb2(img), dim=1)

    pred_labels_and_probs = {class_names[i]: float(pred_probs[0][i]) for i in range(len(class_names))}

    pred_time = round(timer() - start_time, 5)
    return pred_labels_and_probs, pred_time
```

**gr.Interfaceでデモを組み立てて起動**
```python
import gradio as gr

title = "FoodVision Mini 🍕🥩🍣"
description = "An EfficientNetB2 feature extractor computer vision model to classify images as pizza, steak or sushi."
example_list = [["examples/" + example] for example in os.listdir("examples")]

demo = gr.Interface(
    fn=predict,
    inputs=gr.Image(type="pil"),
    outputs=[gr.Label(num_top_classes=3, label="Predictions"),
             gr.Number(label="Prediction time (s)")],
    examples=example_list,
    title=title,
    description=description,
)

demo.launch()  # share=True でColabから一時的な公開URLも発行できる
```

**EffNetB2の特徴抽出器を作るヘルパー(101クラス版FoodVision Bigにも流用)**
```python
def create_effnetb2_model(num_classes: int = 3, seed: int = 42):
    weights = torchvision.models.EfficientNet_B2_Weights.DEFAULT
    transforms = weights.transforms()
    model = torchvision.models.efficientnet_b2(weights=weights)

    for param in model.parameters():
        param.requires_grad = False

    torch.manual_seed(seed)
    model.classifier = nn.Sequential(
        nn.Dropout(p=0.3, inplace=True),
        nn.Linear(in_features=1408, out_features=num_classes),
    )
    return model, transforms

# FoodVision Mini: num_classes=3 / FoodVision Big: num_classes=101
effnetb2_food101, effnetb2_transforms = create_effnetb2_model(num_classes=101)
```

**Hugging Face Spacesへのデプロイ(Git LFSでモデルファイルを追跡)**
```bash
git clone https://huggingface.co/spaces/YOUR_USERNAME/foodvision_mini
cd foodvision_mini
git lfs install
git lfs track "*.pth"
git add .
git commit -m "Add FoodVision Mini demo files"
git push
```

### つまずきやすい点
- Gradioの`predict()`は必ず`model.eval()` + `torch.inference_mode()`で推論し、`unsqueeze(dim=0)`でバッチ次元を追加すること(04・06章と同じdtype・device・shapeの注意点がここでも再登場する)。
- 「精度が高い方が常に良いモデル」とは限らない。デプロイ先の制約(推論速度・モデルサイズ・ユーザー体験)によっては、**わずかに精度が低くても高速・軽量なモデル(EffNetB2)の方が総合的に優れたアプリになる**ことがある。本セクションのViT vs EffNetB2比較はまさにこの判断の実例。
- Hugging Face Spacesで**10MBを超えるファイル(学習済みモデルの`.pth`など)を普通の`git add`/`git push`すると失敗する**。必ず先に`git lfs install`・`git lfs track`でGit LFS管理下に置いてからコミットする必要がある。
- `requirements.txt`にバージョンを明示しておかないと、Spaces側の環境で依存関係(特に`torch`/`torchvision`/`gradio`のバージョン差)によりアプリが起動しないことがある。
- FoodVision Big(101クラス)はFoodVision Mini(3クラス)よりデータ量・クラス数が大幅に増えるため、ダウンロードや1エポックあたりの学習時間も大きく伸びる。ノートブックでは20%サブセットに絞って現実的な時間で訓練できるようにしている(全データでの訓練は演習として提示されている)。
- Gradioの`share=True`で発行される一時公開URLは**約72時間で失効する**。恒久的に公開したい場合はHugging Face Spacesなど常時稼働する環境にデプロイする必要がある。

# GitHub Foundations 認定資格 — 学習ガイド

> GitHub Foundations認定資格の学習用に整理したノート。個人のまとめWiki(dotnetdevelopmentinfrastructure.osscons.jp)にまとめられていた内容をベースに、GitHubの実際の機能(Issues/Pull Requests/Actions/Projects/セキュリティ管理等)を体系的に整理している。**フォロワー獲得等の「攻略法」ではなく、GitHubという製品そのものの機能リファレンス**という位置づけのため、`github-strategy-guide.pptx`(攻略法)とは別ファイルとして管理する。

## 参考リンク

- GitHub Foundations - 学習ガイドPDF / トレーニング / ハンズオン / MS Learn Collections(公式教材群)
- LinkedIn Learning: Prepare for the GitHub Foundations Certification
- GitHub Foundations 模擬試験
- ハンズオン教材: https://github.com/alterbooth/hol-github-foundations

---

## 目次

- 共通項(Settings・Template・エンティティ一覧)
- ドメイン1: GitとGitHub
- ドメイン2: リポジトリ
- ドメイン3: 共同作業機能
- ドメイン4: モダンな開発
- ドメイン5: プロジェクト管理
- ドメイン6: プライバシー、セキュリティ、および管理
- ドメイン7: GitHubコミュニティの利点

---

## 共通項

### Settings
エンティティ(個人Account・Repository・Organization・Enterprise)ごとにそれぞれ設定を定義できる。

### Template
エンティティのテンプレートを定義できる: Repository・Issue・Pull Request・GitHub Actions Workflow。

- **簡単なもの**: Markdown形式(`*.md`)。シンプルなガイドラインや記述例を提供する目的。
- **複雑なもの**: YAML形式(`*.yml`)。複雑な定義が可能。
- **配置場所**: `.github` → root → `docs` ディレクトリなどに配置。複数ファイルを置く場合の場所(フォルダ名)はエンティティ・ドキュメントファイルごとに決まっている。

### エンティティ一覧
- ボードや一覧があるエンティティ(Repository・Issue・Discussion・Project)はピン留めで一番上に表示できる。
- Pull Requestにはピン留めが無いのでラベルを使う。
- 一覧にはフィルタリング用のフィルターが存在する。

---

## ドメイン1: GitとGitHub

### Git and GitHub Basics
- バージョン管理・分散バージョン管理の定義
- Git と GitHub の違い(GitはVCS、GitHubはGitホスティング+コラボレーション機能)
- GitHubリポジトリ、コミット、ブランチ、リモート、GitHubフロー

**機能階層**(Gitを修飾する各種機能):
| カテゴリ | 機能 |
|---|---|
| コード管理・コラボレーション | Gitリポジトリ、Issue、Pull Requests、Projects、Discussions |
| 運用支援・開発環境 | Actions、Codespaces、Copilot |
| セキュリティ | Dependabot、Code scanning、Secret scanning |
| 管理 | Organization、Enterprise |

### GitHub Entities
- **アカウント種別**: Personal / Organization / Enterprise
- **料金プラン**:

| アカウント | Personal | Organization | Enterprise |
|---|---|---|---|
| 無料プラン | Free | Free | — |
| 有料プラン | Pro | Team | Enterprise |

- GitHub Enterpriseには異なるデプロイメントオプションがある
- ユーザープロフィールの機能: メタデータ、実績、プロフィールREADME、リポジトリ、ピン留めリポジトリ、スターなど

### GitHub Markdown
- IssueやPull requestのコメント等で使用
- 基本的なフォーマット構文(見出し、リンク、タスクリストなど)
- テキストフォーマットツールバー、スラッシュコマンドでの構文挿入

### GitHub Desktop
- `github.com` = Gitホスティングサービス、GitHub Desktop = クライアントツール
- GitHub Desktopで開発ワークフローを簡素化。クローンしたリポジトリで全てのgit操作を実行可能

### GitHub Mobile
- Issue/Pull requestのダッシュボードに素早くアクセス
- 外出先でのPull request承認
- モバイル最適化された閲覧・共同作業、検索、通知管理

---

## ドメイン2: リポジトリ

### リポジトリのドキュメントファイル
配置場所: `.github` → root → `docs` ディレクトリ(この順で表示優先度がある)

| ファイル | 役割 |
|---|---|
| `README.md` | プロジェクトの概要・使い方・インストール手順・サンプルコード・ライセンス情報 |
| `LICENSE.md` | ソフトウェアライセンス(MIT, Apache, GPL等)の条文 |
| `CODEOWNERS.md` | 特定ファイル/ディレクトリの責任者(コードオーナー)定義。PRの自動レビュー割当に使用 |
| `CONTRIBUTING.md` | プロジェクトへの貢献方法(PRルール、コードスタイル、バグ報告手順) |
| `CODE_OF_CONDUCT.md` | プロジェクトの行動規範 |
| `SECURITY.md` | 脆弱性報告方法・セキュリティ開示ポリシー |
| `SUPPORT.md` | 問い合わせ方法、FAQ、フォーラム/Issues利用方針 |
| `FUNDING.yml` | GitHub Sponsors等の資金提供手段の設定 |
| `CITATION.cff` | 学術論文向けの引用方法(Citation File Format) |

### 基本的なリポジトリのナビゲーション
Code / Issues / Pull requests / Actions / Projects / Wiki / Security / Insights / Settings(General → Template Repositoryの設定含む) / Branches / Commits(履歴) / clone(コードの取得・展開)

### 新しいリポジトリの作成方法
新規リポジトリ作成 → 新しいブランチ作成 → ファイル追加 → リポジトリのインサイト表示 → スターでお気に入り登録

**機能プレビュー(Feature preview)**(プロファイルアイコンから有効化できる実験的機能の例): Colorblind themes、Command Palette、Copilot Workspace for Pull Requests、Personal Instructions、New Commit Details Page、Rich Jupyter Notebook Diffs、New Issues Experience、New merge experience、Enhanced Repos Insights Views、Slash Commands

### テンプレートリポジトリ
リポジトリをテンプレートとしてマークすると、それを元にした新規リポジトリ作成が可能になる。

---

## ドメイン3: 共同作業機能

### Issue / Pull Request / Discussion の違い
| 機能 | 用途 |
|---|---|
| Issue | シンプルなタスクの登録・管理。Pull requests/Projectsと組み合わせ複雑なタスク管理にも対応 |
| Pull Request | Gitのブランチ間の差分から作成し、変更内容をレビューするために利用 |
| Discussion | 議論・質疑応答、アイデア/フィードバック収集。Issueに起こす前段階の議論・調査 |

### Issues
- Issueの作成、IssueからのBranch作成
- 検索・フィルタリング(プルダウンでフィルタ追加)
- 管理項目: 割り当て(担当者)、Label、Milestone、Project、Development(Branch/PullRequest連携)、ピン留め、`#`によるリンク、重複マーク(Duplicate of #xx)、クローズ/再オープン/転送/削除
- **Issue Template**(`.github/ISSUE_TEMPLATE/`に配置): Markdown形式(自由編集・制約なし)とYAML形式のIssue Form(構造化・必須項目設定・入力フィールド種類の指定が可能)の2種類

### Pull requests
- ベースブランチと比較ブランチを指定して作成。Reviewersを指定できる点がIssueと異なる
- **ステータス**: Draft(作業中) / Open(未マージ・未クローズ) / Closed(マージされず終了) / Merged(マージ済み)
- アクティビティのリンク: 別PRの参照、Issueの参照(連動クローズキーワードあり)、コメント/コミット/コード行のリンク
- **ナビゲーションタブ**: Conversation(会話・レビュー状況) / Commits(コミット一覧) / Checks(CI/CDステータス) / Files changed(差分・コードレビュー)
- **レビュー**: 単一コメント(Add single comment) or 複数コメントのレビュー(Start a review → Finish your review)。変更提案は\`\`\`suggestion\`\`\`で記述し、commit suggestionで反映可能
- レビュー結果: Comment(コメントのみ) / Approve(承認) / Request changes(変更要求)
- 既定レビュアーはCODEOWNERSファイルで指定しておくとよい

### Discussions
- コードに関連しない会話・質問・情報共有の場(2021年8月に正式版)
- 既定でOFF。リポジトリのSettingsから有効化
- Issueとの違い: Issueは具体的作業のトラッキング向け、Discussionsは形式にとらわれない自由な議論向けで、コミュニティ全体が参加できる
- 初期カテゴリ: General(全般)、Announcements(お知らせ)、Ideas(アイデア)、Q&A(質疑応答)、Show and tell(知見共有)
- コメントを回答としてマーク、DiscussionをIssueに変換(自動リンク)、ピン留め可能

### Notifications
- ソース: Repository、Issue & Pull Request、GitHub Actions、Dependabot
- サブスクリプション管理、Ignored repositories
- エンティティごとにメール通知先を変更可能

### GitHub Gist
- コードスニペットを共有する簡単な方法。Gistは実質Gitリポジトリで、作成・フォーク・クローンが可能
- Public または Secret に設定可能。**Public→Secretへの変更は不可**。SecretはGitHub検索にヒットしないだけでURL直アクセスは可能

### GitHub Wiki、Pages
- **Wiki**: リポジトリのドキュメントを作成。書き込みアクセス権を持つユーザーが編集可能
- **Pages**: 静的サイトホスティング。ドキュメントツールの出力の公開先。オプションでビルドプロセスを追加可能

---

## ドメイン4: モダンな開発

### GitHub Actions
- 20以上のプロジェクトイベントに対応する自動化トリガーで、CI/CDに限らずあらゆるAPI呼び出しの自動化が可能
- YAMLベースの設定、17,000以上のコミュニティ製オープンソースアクション

```yaml
name: CI Workflow

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main
  schedule:
    - cron: '0 12 * * 1'  # 毎週月曜日12:00 UTCに実行

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: リポジトリをチェックアウト
        uses: actions/checkout@v4
      - name: Node.js セットアップ
        uses: actions/setup-node@v4
        with:
          node-version: '20'
      - name: 依存関係のインストール
        run: npm install
      - name: テスト実行
        run: npm test
```

### GitHub Copilot
- コメントやコードから文脈を読み取り、次の入力や関数全体を提案するAIペアプログラマー
- 実装ロジックをコメントで記述させてコード生成、テストコードの提案なども可能

### GitHub Codespaces
- ブラウザ内で完結する開発環境(記述・ビルド・テスト・デバッグ・デプロイ)
- 開発環境セットアップ時間を短縮。dotfilesリポジトリやVS Code拡張機能で環境を統一
- Deep Links(環境のコピー)、Live Share(共同編集)、Actions統合(テスト)、シークレット管理、Remote-SSH/Containers接続に対応
- ライフサイクル: Creating(VM課金) → Rebuilding(VM課金) → Stopping(ストレージ課金) → Deleting

**github.dev vs Codespaces**:
| 項目 | github.dev | Codespaces |
|---|---|---|
| コスト | 無料 | 個人アカウントに無料枠あり |
| 起動 | すぐ使える | VM割り当て+devcontainer.jsonでコンテナ構成 |
| コンピュート | エディタのみ | VM上でデバッグ可能 |
| ターミナル | なし | ローカル同様に操作可能 |
| 拡張機能 | Web実行可能なサブセットのみ | VSCode Marketplaceのほとんどが利用可能 |

---

## ドメイン5: プロジェクト管理

### GitHub Projects
- Issue/Pull requestをインポートしてタスク管理。リポジトリ・組織・個人の3種類のプロジェクトがある
- **3種類のレイアウト**: テーブル / ボード(カンバン) / ロードマップ(ガントチャート)
- 自由なフィールド構成(テキスト・数値・選択肢・日付・イテレーション型でスプリント管理)
- 簡易ワークフロー(GitHub Actions連携): Item added / reopened / closed、Code changes requested、Code review approved、Pull request merged 等をトリガーにフィールド自動更新
- 旧来のProjects(classic)は2022年に刷新され、**2024年8月にサービス提供終了**(GraphQL API非対応・カスタマイズ性が低かった)
- その他: Repositoryから独立したアクセス管理、Draft IssueからIssueへの変換、Label/Milestoneの可視化、インサイト(グラフチャート)、プロジェクト/返信のテンプレート

---

## ドメイン6: プライバシー、セキュリティ、および管理

### Authentication and Security
- **2FA(二要素認証)**: TOTPアプリ、モバイル/デスクトップ、テキストメッセージによる保護
- **RBAC(Role-Based Access Control)**: ロールに対象エンティティ(Enterprise/Organization/Team/Repository)と操作を定義し、ユーザーに割り当てる。Enterprise/Organization/Teamはロール割当、Repositoryはアクセス許可(パーミッション)を割り当てる
- **EMU(Enterprise Managed Users)**: エンタープライズ管理者が所有・作成・管理・監査するGHECアカウント。IDPと連携したプロビジョニング/デプロビジョニングの自動化が可能

### GitHub Administration

**リポジトリの権限レベル**:
| 権限 | 説明 | 想定ロール |
|---|---|---|
| Read | コード・Actionsの読み取り、Issue/PR/Discussionへのコメント | 非コードコントリビューター |
| Triage | 読み取りに加えLabel/アサインメント管理(書き込み権限なし) | 管理コントリビューター |
| Write | Repository設定を除く全ての書き込み | コードコントリビューター |
| Maintain | リポジトリ管理(削除・セキュリティ関連操作は不可) | プロジェクトマネージャー |
| Admin | 全機能・設定への完全な管理アクセス | リポジトリの全体管理者 |

**リポジトリ可視性**:
| 可視性 | 説明 |
|---|---|
| Public | 世界中から閲覧可能。OSSプロジェクト向け |
| Internal | Enterprise所有のOrganization内でのみ作成可能。同Enterprise所属メンバー全員がアクセス可能(インナーソース向け) |
| Private | 明示的に追加されたユーザー/Teamのみアクセス可能 |

**ブランチ保護**: Settings → Code and automation → Rules → Rulesets → New ruleset で追加。バイパスリストで管理者による制限回避可否を制御。対象ブランチは `release-*` のようなパターン指定も可能。CODEOWNERSとの連携あり

**Security機能**: Security policy、Security advisories、Private vulnerability reporting、Dependabot alerts、Code scanning alerts、Secret scanning alerts

**Insights**: Pulse、Contributors、Community、Community Standards、Traffic、Commits、Code frequency、Dependency graph、Network、Forks、Actions Usage/Performance Metrics

**People/ロール(Organization)**:
| ロール | 説明 |
|---|---|
| Owner(所有者) | 組織の全操作+ユーザー追加削除。2人以上指定推奨 |
| Member(メンバー) | リポジトリ・チームの作成/管理 |
| Moderator(モデレーター) | 共同作成者のブロック/解除、相互作用制限、コメント非表示 |
| Billing manager | 課金情報の表示・管理 |
| Security managers | リポジトリのアクセス許可・セキュリティアラート管理 |
| Outside collaborator | 1つ以上のOrganizationリポジトリにアクセス可 |

Teamのロール: Member(Organizationメンバーと同等) / Maintainer(チームのメンテナンス操作も可能)。Teamは細分化・階層化が可能。

---

## ドメイン7: GitHubコミュニティの利点

### オープンソースコミュニティ
- オープンソースの定義とその利点
- 人・Organizationをフォローする方法(通知受信、コミュニティ内プロジェクトの発見)
- **GitHub Sponsors**: 金銭的支援のための機能
- **GitHub Marketplace**: 開発用ツールの販売サイト(Code quality、Code review、CI、Monitoring、Project management等のカテゴリ)
- オープンソースの利点の社内展開(インナーソース): コラボレーション強化、サイロ打破、開発者満足度向上

**発見・forkされやすいリポジトリの構成要素**:
- Topics(トピック)の設定
- READMEの適切な構成
- CONTRIBUTING.md等の整備
- 適切なラベル設定
- Issue/Pull requestテンプレートの用意

---

*本ノートは、GitHub Foundations認定資格の学習を目的に、GitHub製品の機能リファレンスとして個人の学習Wikiの内容を整理したもの。フォロワー獲得等の運用ノウハウは扱わず、`github-strategy-guide.pptx`(GitHub攻略ガイド)側で別途管理する。*

# Git / Github 使用手順

## Gitリポジトリの初期化
### Step 1 : まずはGitが使用できるか確認します。

① VSCode のターミナルを開く
② Gitのバージョンを確認
```Bash
git --version
```
現在のGit管理状態を確認
```Bash
git status
```
まだGit管理を開始していなければ、次のようなメッセージが表示されます。
```Text
fatal: not a git repository (or any of the parent directories): .git
```
これはエラー表示ですが、Gitの初期化前なら正常な状態です。

### Step 1 - 1 : Gitリポジトリの初期化
VS Codeのターミナルで以下を実行してください。
```Bash
git init -b main
```
このコマンドを分解すると、次の意味になります。
```Text
- git : Git を操作する
- init : リポジトリを初期化する
- -b main : 最初のブランチをmainにする
```
実行すると、プロジェクトフォルダ内に .git という隠しフォルダが作られます。
```Text
network-impact-analyzer/
├── .git/       ← Gitの管理情報
└── .venv/      ← Pythonの仮想環境
```

.git と .venv は別の役割を持っています。
- .git：ファイルの変更履歴やブランチを管理する
- .venv：Pythonの実行環境やライブラリを管理する

おすすめ : Gitの設定によって、日本語などの非ASCII文字がエスケープ表示される場合があります。

例えば、次のコマンドで日本語ファイル名を読みやすく表示できます。
```Bash 
git -c core.quotepath=false status
```
## .gitignore の作成
.gitignoreは、Git で管理しないファイルやフォルダを指定する設定フォルダ。
今回はPython の仮想環境の .venv を除外する。

① VS Codeでファイルを作成
VS CodeのExplorerで、network-impact-analyzer フォルダを右クリックし、**New File（新しいファイル）**を選択する
ファイル名
```Text
.gitignore
```

② 以下の内容を記載
```gitignore
# Python仮想環境
.venv/

# Pythonキャッシュ
__pycache__/
*.py[cod]

# pytestキャッシュ
.pytest_cache/

# macOSの管理ファイル
.DS_Store

# 環境変数・秘密情報
.env
.env.*
!.env.example
```
各設定の意味
- .venv/ : Python仮想環境
- __pycache__/ : Pythonのキャッシュ
- *.py[cod] : コンパイル済みPythonファイル
- .pytest_cache/ : pytestのキャッシュ
- .DS_Store : macOSが生成する管理ファイル
- .env / .env.* : 環境変数や秘密情報を含むファイル

③ 保存して確認
```Bash
git -c core.quotepath=false status
```
.venv が表示されていないことを確認する

## git add ( ステージング )
Gitでは、ファイルを変更しただけでは、その変更はコミットに含まれません。
まず、コミットしたいファイルをステージングします。

```Text
作業ディレクトリ ( ファイルを作成・編集する場所)
　　　　　　　　　↓　git add
ステージングエリア ( 次のコミットを含める更新を準備する場所 )
　　　　　　　　　↓　git commit
ローカルリポジトリ ( 変更履歴を記録する場所 )
```
練習
① .gitignoreをステージングする
```Bash
git add .gitignore
```
② ステージング状況を確認する
```Bash
git -c core.quotepath=false status
```
期待される結果：
```Text
On branch main

No commits yet

Changes to be committed:
    new file:   .gitignore

Untracked files:
    Git_Github使用方法.md
    詳細設計書.md
    開発環境構築手順書.md
```
ここで注目するのは、.gitignore が Untracked files から Changes to be committed に移動することです。
③ ステージングした内容を確認する
```Bash
git diff --cached
```
このコマンドで、次のコミットに含まれる変更内容を確認できます。

現状の段階
- .gitignore : ステージング成功
- そのほかのMarkdown ファイル : 未登録のまま
- git diff --cached : 登録予定の変更を表示
- コミット : まだ実行していない


## ローカルリポジトリへコミット
ステージングした .gitignore をコミットする

### コミットの実行
① コミットを実行する
```Bash
git commit -m "chore: add Git ignore rules"
```

コマンドの意味を整理
- git commit : ステージングした変更を履歴として記録
- -m : コミットメッセージを指定
- chore: : 設定変更などの作業であることを示す慣例的な接頭辞
- add Git ignore rules : 今回の変更内容

② コミット結果を確認する
```Bash 
git status
```
正常にコミットできていれば、.gitignore は Changes to be committed から消えます。

### git log で履歴を確認する

```Bash 
git log --oneline
```
期待される結果：
```Text
57dec53 chore: add Git ignore rules
```
git logとは？
```Text
git log は、過去のコミット履歴を確認するコマンドです。

--oneline を付けると、コミットIDとメッセージが1行にまとめて表示されます。

今後、設計書の変更やPythonの機能追加をコミットしていくと、例えば次のような履歴になります。

a3f91b2 feat: implement graph builder
c7d42e1 docs: add detailed design
57dec53 chore: add Git ignore rules
```

### 既存のMarkdownファイルをGitで管理する

① git addを実行する

全ての.md をステージングする
```Bash
git add -- "*.md"
```
このコマンドの意味は次のとおりです。
- git add：ファイルをステージングする
- --：これ以降をファイルパスとして扱う
- "*.md"：Markdownファイルを対象にする

② ステージング結果を確認する
```Bash
git -c core.quotepath=false status
```

③ ステージングしたファイルを確認する
```Bash
git diff --cached --stat
```
これは、ステージングされたファイルごとの変更行数を確認するコマンド

### Markdownファイルをコミットする
初回の設計書・手順書登録として、次のコミットメッセージを使用します。

① コミット前の確認
まず、ステージングしたファイルの中身を確認しましょう。
```Bash
git -c core.quotepath=false diff --cached
```

② コミットを実行
問題なければ、次のコマンドを実行してください。
```Bash
git commit -m "docs: add initial project documentation"
```
docs: は、ドキュメントの追加・変更を示す接頭辞

③ コミット履歴を確認
```Bash
git log --oneline
```

## Github リポジトリの作成
ローカルのGitリポジトリをGitHubと連携させます。

### GitHub上にリポジトリを作成する

1. GitHubの新規リポジトリ作成画面を開く
2. 既存のGitHubアカウントでログインする
3. 以下の内容を設定する

設定項目	設定内容
Repository name	network-impact-analyzer
Description	なし
Visibility	Private（public でもいい）
Add README	オフ
Add .gitignore	オフ
Choose a license	None

#### GitHubとの接続（SSH）

① GitHubのSSH URLを確認する
作成したGitHubリポジトリを開いてください。
1. リポジトリの Code ボタンをクリック
2. Local → SSH を選択
3. 表示されたURLをコピー

② Macのターミナルで接続する
GitHubリポジトリを origin という名前で登録
```Bash
git remote add origin git@github.com:USERNAME/network-impact-analyzer.git
```
注意：上記の USERNAME をそのまま実行せず、GitHubからコピーした実際のSSH URLを使用してください。

origin はリモートリポジトリにつける名前で、一般的に使用される慣例です。

③ 接続先を確認する
```Bash
git remote -v
```
成功すると、次のように表示されます。
```Text
origin  git@github.com:USERNAME/network-impact-analyzer.git (fetch)
origin  git@github.com:USERNAME/network-impact-analyzer.git (push)
```
これで、ローカルのGitにGitHubの接続先が登録されます。ただし、この時点ではまだファイルはアップロードされていません。

#### GitHubへの初回push
Mac上で管理していたNetwork Impact Analyzerのファイルとコミット履歴を、GitHubにアップロードします

① pushを実行する
```Bash
git push -u origin main
```

コマンドの意味の整理。
コマンド	意味
git push	ローカルのコミットをGitHubに送信する
-u	今後のpush先となる追跡ブランチを設定する
origin	登録済みのGitHubリポジトリ
main	アップロードするブランチ

SSHのパスフレーズを求められた場合は、以前設定したパスフレーズを入力する。

④ 成功したら確認すること
https://github.com/Golcas0301/network-impact-analyzer?utm_source=chatgpt.comを開いて、ファイルが表示されているか確認します。

今後は、基本的に次の操作でGithubへ変更を反映できる。
```Bash
git add .
git commit -m "変更内容"
git push
```
ただし、git add . は変更をまとめてステージングするため、実際の開発では git status や git diff で内容を確認してから使う習慣をつける。

#### GitHubとの同期状態を確認する
ローカルとGitHubが正しく同期されているか確認
```Bash
git status
git branch -vv
```

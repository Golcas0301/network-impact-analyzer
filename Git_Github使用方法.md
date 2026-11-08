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


## コミット
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
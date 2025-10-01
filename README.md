# Django Memo App | ユーザー認証機能 実装ガイド

このリポジトリは、Djangoで作成したシンプルなメモアプリに、ユーザー認証機能（新規登録・ログイン・ログアウト）を実装するためのロードマップです。

## 🚀 実装ロードマップ (Implementation Roadmap)

### STEP 1: 認証用アプリの作成 🏗️

ユーザー認証関連の機能を専門に扱うための新しいアプリ（例：`accounts`）を作成し、プロジェクトに登録します。

-   **コマンド**: `python manage.py startapp accounts`
-   **設定**: `settings.py` の `INSTALLED_APPS` に `'accounts'` を追加

<details>
<summary>▶︎ 実際のコードを見る (Click to see code)</summary>

<br>

**1. `accounts` アプリの作成**

ターミナルで以下のコマンドを実行します。

```bash
python manage.py startapp accounts

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'memos.apps.MemosConfig',
    'accounts.apps.AccountsConfig', # この行を追加
]```
> **Note:** `apps.py` 内のクラス名 (`AccountsConfig`) を指定する、よりモダンな書き方を採用しています。

</details>

### STEP 2: URLの設計と設定 🗺️

サインアップ、ログイン、ログアウト用のURLを設計し、プロジェクト全体のURL設定から読み込むようにします。

-   プロジェクトの `urls.py` で `accounts/` へのパスを設定
-   `accounts` アプリ内に `urls.py` を新規作成し、各ページのパスを定義

### STEP 3: サインアップ機能の実装 ✍️

ユーザーが新しいアカウントを作成できるようにします。

-   Django標準の `UserCreationForm` を活用してフォームを作成
-   ビューでフォーム処理を実装し、成功したらログインページへリダイレクト
-   サインアップページのHTMLテンプレートを作成

### STEP 4: ログイン・ログアウト機能の実装 🚪

作成済みのアカウントでログイン・ログアウトできるようにします。

-   Django組み込みの `LoginView`, `LogoutView` を活用
-   ログインページのテンプレートを作成
-   ログイン後・ログアウト後のリダイレクト先を `settings.py` に設定

### STEP 5: メモとユーザーの関連付け 🔗

どのユーザーがどのメモを作成したかを記録できるように、`Memo`モデルを更新します。

-   `Memo` モデルに `User` モデルへの `ForeignKey` を追加
-   モデルの変更をデータベースに反映 (`makemigrations` & `migrate`)

### STEP 6: 既存メモ機能の改修 🔒

メモの作成と表示が、ログインしているユーザーに限定されるように修正します。

-   `@login_required` デコレータをビューに追加してアクセスを制限
-   メモを保存する際に、リクエストから取得したユーザー情報を自動で紐付け
-   メモ一覧には、ログイン中のユーザーが作成したものだけを表示

### STEP 7: テンプレートの全体改修 ✨

ユーザーのログイン状態に応じて、ページの表示を動的に変更します。

-   ナビゲーションに「ようこそ、〇〇さん」「ログイン」「ログアウト」リンクを追加
-   各ページのデザインを微調整し、新しい機能と一貫性を持たせる

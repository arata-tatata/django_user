# Django Memo App | ユーザー認証機能 実装ガイド

このリポジトリは、Djangoで作成したシンプルなメモアプリに、ユーザー認証機能（新規登録・ログイン・ログアウト）を実装するためのロードマップです。

## 🚀 実装ロードマップ (Implementation Roadmap)

### STEP 1: 認証用アプリの作成 🏗️

ユーザー認証関連の機能を専門に扱うための新しいアプリ（例：`accounts`）を作成し、プロジェクトに登録します。

-   **コマンド**: `python manage.py startapp accounts`
-   **設定**: `settings.py` の `INSTALLED_APPS` に `'accounts'` を追加

<details>
<summary>▶︎ 実際のコードを見る (Click to see code)</summary>

> **1. `accounts` アプリの作成**
>
> ターミナルで以下のコマンドを実行します。
>
> ```bash
> python manage.py startapp accounts
> ```
>
> **2. プロジェクトへのアプリ登録**
>
> `memoproject/settings.py` ファイルを開き、`INSTALLED_APPS` リストに新しいアプリを追加します。
>
> ```python:memoproject/settings.py
> INSTALLED_APPS = [
>     'django.contrib.admin',
>     'django.contrib.auth',
>     'django.contrib.contenttypes',
>     'django.contrib.sessions',
>     'django.contrib.messages',
>     'django.contrib.staticfiles',
>     'memos.apps.MemosConfig',
>     'accounts.apps.AccountsConfig', # この行を追加
> ]
> ```
>
> > **Note:** `apps.py` 内のクラス名 (`AccountsConfig`) を指定する、よりモダンな書き方を採用しています。

</details>
<br>

### STEP 2: URLの設計と設定 🗺️

サインアップ、ログイン、ログアウト用のURLを設計し、プロジェクト全体のURL設定から読み込むようにします。

-   プロジェクトの `urls.py` で `accounts/` へのパスを設定
-   `accounts` アプリ内に `urls.py` を新規作成し、各ページのパスを定義
<br>

### STEP 3: サインアップ機能の実装 ✍️

ユーザーが新しいアカウントを作成できるようにします。

-   Django標準の `UserCreationForm` を活用してフォームを作成
-   ビューでフォーム処理を実装し、成功したらログインページへリダイレクト
-   サインアップページのHTMLテンプレートを作成

<br>


### STEP 4: ログイン・ログアウト機能の実装 🚪

作成済みのアカウントでログイン・ログアウトできるようにします。

-   Django組み込みの `LoginView`, `LogoutView` を活用
-   ログインページのテンプレートを作成
-   ログイン後・ログアウト後のリダイレクト先を `settings.py` に設定

<details>
<summary>▶︎ 実際のコードを見る (Click to see code)</summary>

**URL、テンプレート、設定ファイルの編集**

Djangoに標準で用意されている認証ビューを活用し、少ないコードでログイン・ログアウト機能を実装します。

```python
# --- accounts/urls.py ---
# ログインとログアウトのURLを追加します。

from django.urls import path
# 認証ビュー（LoginView, LogoutView）をインポート
from django.contrib.auth import views as auth_views
from . import views

app_name = 'accounts'

urlpatterns = [
    path('signup/', views.signup_view, name='signup'),
    
    # /accounts/login/ へのアクセスでLoginViewを呼び出す
    # テンプレートは 'accounts/login.html' を使用
    path('login/', auth_views.LoginView.as_view(template_name='accounts/login.html'), name='login'),
    
    # /accounts/logout/ へのアクセスでLogoutViewを呼び出す
    path('logout/', auth_views.LogoutView.as_view(), name='logout'),
]


# --- accounts/templates/accounts/login.html (新規作成) ---
# ログインフォーム用のテンプレートを新規作成します。

{% extends 'base.html' %}

{% block title %}ログイン{% endblock %}

{% block content %}
<h1 class="page-title">ログイン</h1>

<form method="post" class="memo-form">
    {% csrf_token %}
    
    {{ form.as_p }}

    <button type="submit" class="btn btn-primary">ログイン</button>
</form>
{% endblock %}


# --- memoproject/settings.py ---
# ログイン・ログアウト後のリダイレクト先を追記します。
# ファイルの末尾あたりに追加してください。

# ... (既存の設定) ...

# ログイン成功後にリダイレクトされるURL
LOGIN_REDIRECT_URL = '/' 

# ログアウト成功後にリダイレクトされるURL
# LOGOUT_REDIRECT_URL = '/' # デフォルトでログインページに飛ぶので、トップに戻したい場合はこの設定を追加```

> **Note:** `LoginView` と `LogoutView` を使うことで、ビューを自分で書く必要がなくなり、URLとテンプレートを用意するだけで機能が完成します。

</details>
<br>


### STEP 5: メモとユーザーの関連付け 🔗

どのユーザーがどのメモを作成したかを記録できるように、`Memo`モデルを更新します。

-   `Memo` モデルに `User` モデルへの `ForeignKey` を追加
-   モデルの変更をデータベースに反映 (`makemigrations` & `migrate`)
<br>

### STEP 6: 既存メモ機能の改修 🔒

メモの作成と表示が、ログインしているユーザーに限定されるように修正します。

-   `@login_required` デコレータをビューに追加してアクセスを制限
-   メモを保存する際に、リクエストから取得したユーザー情報を自動で紐付け
-   メモ一覧には、ログイン中のユーザーが作成したものだけを表示
<br>

### STEP 7: テンプレートの全体改修 ✨

ユーザーのログイン状態に応じて、ページの表示を動的に変更します。

-   ナビゲーションに「ようこそ、〇〇さん」「ログイン」「ログアウト」リンクを追加
-   各ページのデザインを微調整し、新しい機能と一貫性を持たせる
<br>


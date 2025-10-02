# Django Memo App | ユーザー認証機能 実装ガイド

このリポジトリは、Djangoで作成したシンプルなメモアプリに、ユーザー認証機能（新規登録・ログイン・ログアウト）を実装するためのロードマップです。

## 🚀 実装ロードマップ (Implementation Roadmap)

コードブロックスタート！！ーーーーーーーーーーーーーーーーーーーー

コードブロックスタート！！ーーーーーーーーーーーーーーーーーーーー

###コードブロックスタート！！ーーーーーーーーーーーーーーーーーーーー

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
>```python
> python manage.py startapp accounts
>```
>
> **2. プロジェクトへのアプリ登録**
>
> `memoproject/settings.py` ファイルを開き、`INSTALLED_APPS` リストに新しいアプリを追加します。
> 
>```
> # --- memoproject/settings.py ---
> INSTALLED_APPS = [
> 'django.contrib.admin',
> 'django.contrib.auth',
> 'django.contrib.contenttypes',
> 'django.contrib.sessions',
> 'django.contrib.messages',
> 'django.contrib.staticfiles',
> 'memos.apps.MemosConfig',
> 'accounts.apps.AccountsConfig', # この行を追加
> ]
>```
>
> > **Note:** `apps.py` 内のクラス名 (`AccountsConfig`) を指定する、よりモダンな書き方を採用しています。

</details>
<br>

### STEP 2: URLの設計と設定 🗺️

サインアップ、ログイン、ログアウト用のURLを設計し、プロジェクト全体のURL設定から読み込むようにします。

-   プロジェクトの `urls.py` で `accounts/` へのパスを設定
-   `accounts` アプリ内に `urls.py` を新規作成し、各ページのパスを定義

<details>
<summary>▶︎ 実際のコードを見る (Click to see code)</summary>

> **--- memoproject/urls.py ---**
>
> # プロジェクトのメインとなるURL設定ファイルを編集します。
>
> from django.contrib import admin
> from django.urls import path, include 
> from memos.views import memo_list_create
> 
> urlpatterns = [
> path('admin/', admin.site.urls),
> path('', memo_list_create, name='memo_list'),
> path('accounts/', include('accounts.urls')),
> ]
>
> **--- accounts/urls.py (新規作成) ---**
>
> # `accounts`アプリのディレクトリ内に、このファイルを新規作成します。
>
> from django.urls import path
> from . import views
> 
> app_name = 'accounts'
> 
> urlpatterns = [
> path('signup/', views.signup_view, name='signup'),
> ]

</details>
<br>

### STEP 3: サインアップ機能の実装 ✍️

ユーザーが新しいアカウントを作成できるようにします。

-   Django標準の `UserCreationForm` を活用してフォームを作成
-   ビューでフォーム処理を実装し、成功したらログインページへリダイレクト
-   サインアップページのHTMLテンプレートを作成

<details>
<summary>▶︎ 実際のコードを見る (Click to see code)</summary>

> **--- accounts/views.py ---**
>
> # サインアップ処理を行うビューを記述します。
>
> from django.shortcuts import render, redirect
> from django.contrib.auth.forms import UserCreationForm
> 
> def signup_view(request):
> if request.method == 'POST':
> form = UserCreationForm(request.POST)
> if form.is_valid():
> form.save()
> return redirect('accounts:login') 
> else:
> form = UserCreationForm()
> 
> return render(request, 'accounts/signup.html', {'form': form})
>
> **--- accounts/templates/accounts/signup.html (新規作成) ---**
>
> # `accounts/templates/accounts/` という階層でHTMLファイルを新規作成します。
>
> {% extends 'base.html' %}
> 
> {% block title %}サインアップ{% endblock %}
> 
> {% block content %}
> <h1 class="page-title">サインアップ</h1>
> 
> <form method="post" class="memo-form">
> {% csrf_token %}
> {{ form.as_p }}
> <button type="submit" class="btn btn-primary">登録する</button>
> </form>
> {% endblock %}

</details>
<br>

### STEP 4: ログイン・ログアウト機能の実装 🚪

作成済みのアカウントでログイン・ログアウトできるようにします。

-   Django組み込みの `LoginView`, `LogoutView` を活用
-   ログインページのテンプレートを作成
-   ログイン後・ログアウト後のリダイレクト先を `settings.py` に設定

<details>
<summary>▶︎ 実際のコードを見る (Click to see code)</summary>

> **--- accounts/urls.py ---**
>
> # ログインとログアウトのURLを追加します。
>
> from django.urls import path
> from django.contrib.auth import views as auth_views
> from . import views
> 
> app_name = 'accounts'
> 
> urlpatterns = [
> path('signup/', views.signup_view, name='signup'),
> path('login/', auth_views.LoginView.as_view(template_name='accounts/login.html'), name='login'),
> path('logout/', auth_views.LogoutView.as_view(), name='logout'),
> ]
>
> **--- accounts/templates/accounts/login.html (新規作成) ---**
>
> # ログインフォーム用のテンプレートを新規作成します。
>
> {% extends 'base.html' %}
> 
> {% block title %}ログイン{% endblock %}
> 
> {% block content %}
> <h1 class="page-title">ログイン</h1>
> 
> <form method="post" class="memo-form">
> {% csrf_token %}
> {{ form.as_p }}
> <button type="submit" class="btn btn-primary">ログイン</button>
> </form>
> {% endblock %}
>
> **--- memoproject/settings.py ---**
>
> # ログイン・ログアウト後のリダイレクト先を追記します。
>
> LOGIN_REDIRECT_URL = '/' 
> LOGOUT_REDIRECT_URL = '/'

</details>
<br>

コードブロック終了！！ーーーーーーーーーーーーーーーーーーーー






# Django Memo App | ユーザー認証機能 実装ガイド

このリポジトリは、Djangoで作成したシンプルなメモアプリに、ユーザー認証機能（新規登録・ログイン・ログアウト）を実装するためのロードマップです。ストーリー仕立てで進めることで、「なぜこれが必要なのか」を体感しながら学んでいきましょう。

## 🚀 実装ロードマップ (Implementation Roadmap)

### STEP 1: 認証アプリの作成と基礎設定

まずはユーザー認証機能の「置き場所」となる`accounts`アプリを作成し、プロジェクト全体で認識できるように設定します。これは家を建てる前の「地盤工事」のようなものです。

1.  **`accounts`アプリの作成**
    
    プロジェクトのルートディレクトリで、以下のコマンドを実行して`accounts`という名前の新しいアプリケーションを作成します。
    
    ```bash
    python manage.py startapp accounts
    ```
    
2.  **プロジェクトへの登録**
    
    作成したアプリをプロジェクトに認識させるため、`settings.py`に登録します。
    
    <details>
    <summary>▶︎ 実際のコードを見る (Click to see code)</summary>
    
    > **--- memoproject/settings.py ---**
    >
    > `INSTALLED_APPS`リストに、`'accounts.apps.AccountsConfig'`という行を追加します。
    >
    >```python
    >INSTALLED_APPS = [
    >    'django.contrib.admin',
    >    'django.contrib.auth',
    >    'django.contrib.contenttypes',
    >    'django.contrib.sessions',
    >    'django.contrib.messages',
    >    'django.contrib.staticfiles',
    >    'memos.apps.MemosConfig',
    >    'accounts.apps.AccountsConfig', # この行を追加
    >]
    >```
    >
    > > **Note:** `'accounts'`と書くより`'accounts.apps.AccountsConfig'`と書く方が、Djangoに対して設定ファイルの場所を直接的かつ正確に教えられるため、公式に推奨されるモダンな書き方です。
    
    </details>
    <br>
    
3.  **URLの振り分け設定**
    
    `accounts`アプリに関するURL（`/signup/`など）へのアクセスを、`accounts`アプリ自身に処理させるための設定を行います。
    
    <details>
    <summary>▶︎ 実際のコードを見る (Click to see code)</summary>
    
    > **--- memoproject/urls.py ---**
    >
    > プロジェクトの総合案内所であるこのファイルに、「`/accounts/`から始まるURLは`accounts`アプリに任せる」というルールを追加します。
    >
    >```python
    >from django.contrib import admin
    >from django.urls import path, include 
    >
    >urlpatterns = [
    >    path('admin/', admin.site.urls),
    >    path('', include('memos.urls')), # メモアプリのURLをインクルード
    >    path('accounts/', include('accounts.urls')), # この行を追加
    >]
    >```
    >
    > **--- accounts/urls.py (新規作成) ---**
    >
    > `accounts`アプリ用の案内所を作成します。
    >
    >```python
    >from django.urls import path
    >from . import views
    >
    >app_name = 'accounts'
    >urlpatterns = [
    >    # ここにサインアップ等のURLを後で追加していきます
    >]
    >```
    
    </details>
    <br>

### STEP 2: サイト共通の骨格（`base.html`）を作る

アプリケーションのデザインを統一するため、全てのページの土台となる`base.html`と、それに対応するCSSを先に完成させてしまいましょう。

1.  **プロジェクト共通`templates`ディレクトリの作成**
    
    プロジェクトルート (`manage.py`と同じ階層) に`templates`ディレクトリを新規作成し、Djangoがその場所を認識できるように`settings.py`を編集します。
    
    <details>
    <summary>▶︎ 実際のコードを見る (Click to see code)</summary>
    
    > **--- memoproject/settings.py ---**
    >
    > `TEMPLATES`設定の`'DIRS'`に、作成したディレクトリのパスを追加します。
    >
    >```python
    >import os # osをインポート
    >
    ># ...
    >TEMPLATES = [
    >    {
    >        # ...
    >        'DIRS': [os.path.join(BASE_DIR, 'templates')], # このように編集
    >        'APP_DIRS': True,
    >        # ...
    >    },
    >]
    >```
    >
    > > **なぜアプリの外に置くの？**: `base.html`は特定のアプリに属さず、プロジェクト全体の土台です。ここに置くことで、アプリ間の依存関係がなくなり、再利用性が高まります。
    
    </details>
    <br>
    
2.  **`base.html`とCSSの作成**
    
    サイトの骨格となるHTMLと、全てのデザインを定義するCSSを作成・追記します。
    
    <details>
    <summary>▶︎ 実際のコードを見る (Click to see code)</summary>
    
    > **--- templates/base.html (新規作成) ---**
    >
    >```html
    >{% load static %}
    ><!doctype html>
    ><html lang="ja">
    >  <head>
    >    <meta charset="utf-8">
    >    <title>{% block title %}メモアプリ{% endblock %}</title>
    >    <meta name="viewport" content="width=device-width,initial-scale=1">
    >    <link rel="stylesheet" href="{% static 'memos/css/memos.css' %}">
    >  </head>
    >  <body class="page">
    >    {% block content %}{% endblock %}
    >  </body>
    ></html>
    >```
    >
    > **--- memos/static/memos/css/memos.css (追記) ---**
    >
    > ファイルの末尾に、アカウント関連ページとフォームのデザインを追記します。
    >
    >```css
    >/* ===== Accounts Pages & Forms ===== */
    >.memo-form p { margin-bottom: 20px; }
    >.memo-form label { display: block; font-size: 13px; color: var(--ink-2); margin-bottom: 6px; }
    >.memo-form .helptext, .memo-form .helptext ul { font-size: 13px; color: var(--ink-2); list-style: none; padding: 0; margin: 8px 0 0; line-height: 1.6; }
    >.memo-form .helptext ul { padding-left: 18px; }
    >.memo-form .helptext li { margin-bottom: 4px; }
    >.errorlist { list-style: none; padding: 10px 14px; margin: 0 0 20px; background: rgba(229, 62, 62, .1); color: #e53e3e; border-radius: 8px; font-size: 14px; text-align: center; }
    >.errorlist p { margin: 0; }
    >.account-link { margin-top: 24px; text-align: center; font-size: 14px; }
    >.account-link a { color: var(--brand); text-decoration: none; font-weight: 600; }
    >.account-link a:hover { text-decoration: underline; }
    >/* Form Validation */
    >input:invalid { border-color: #e53e3e; box-shadow: 0 0 0 4px rgba(229, 62, 62, .18); }
    >.form-field { margin-bottom: 22px; }
    >.form-field input { margin-top: 6px; }
    >.errorlist-field { font-size: 13px; color: #e53e3e; margin-top: 6px; }
    >```
    
    </details>
    <br>

### STEP 3: サインアップ機能の実装（第一段階：まずは動かす）

最小限のコードでアカウント登録機能を実装します。しかし、この段階ではいくつかの「気持ち悪い」点が残ります。

<details>
<summary>▶︎ 実際のコードを見る (Click to see code)</summary>

> **--- accounts/views.py ---**
>
> Django標準の`UserCreationForm`を使い、サインアップのロジックを記述します。
>
>```python
>from django.shortcuts import render, redirect
>from django.contrib.auth.forms import UserCreationForm
>
>def signup_view(request):
>    if request.method == 'POST':
>        form = UserCreationForm(request.POST)
>        if form.is_valid():
>            form.save()
>            return redirect('accounts:login') # ログインページのURL名は後で作成
>    else:
>        form = UserCreationForm()
>    return render(request, 'accounts/signup.html', {'form': form})
>```
>
> **--- accounts/urls.py ---**
>
> 作成したビューへのパスを追加します。
>
>```python
># ...
>urlpatterns = [
>    path('signup/', views.signup_view, name='signup'),
>]
>```
>
> **--- accounts/templates/accounts/signup.html (新規作成) ---**
>
> `{{ form.as_p }}`を使い、フォームをシンプルに表示します。
>
>```html
>{% extends 'base.html' %}
>{% block title %}アカウント作成{% endblock %}
>{% block content %}
><h1 class="page-title">アカウント作成</h1>
><form method="post" class="memo-form">
>    {% csrf_token %}
>    {{ form.as_p }}
>    <button type="submit" class="btn btn-primary">登録する</button>
></form>
>{% endblock %}
>```

</details>
<br>

> **【問題提起】このままでは使えない！**
>
> この状態で`/accounts/signup/`にアクセスしてみてください。ラベルや説明が全て英語で、ユーザーフレンドリーとは言えませんね。入力エラーが起きても、英語で表示されてしまいます。
> この「気持ち悪い」状態を、次のステップでプロフェッショナルなフォームに改善していきましょう！

### STEP 4: フォームの日本語化と完全なカスタマイズ

「気持ち悪い」英語表示を解決し、フォームを完全にコントロールする方法を学びます。

1.  **`forms.py`の導入**
    
    `UserCreationForm`を継承したカスタムフォーム`SignUpForm`を定義し、表示内容を上書きします。
    
    <details>
    <summary>▶︎ 実際のコードを見る (Click to see code)</summary>
    
    > **--- accounts/forms.py (新規作成) ---**
    >
    >```python
    >from django.contrib.auth.forms import UserCreationForm
    >from django import forms
    >
    >class SignUpForm(UserCreationForm):
    >    def __init__(self, *args, **kwargs):
    >        super().__init__(*args, **kwargs)
    >        # ラベル、ヘルプテキスト、エラーメッセージなどを日本語化
    >        self.fields['username'].label = 'ユーザー名'
    >        self.fields['username'].help_text = '150文字以下の半角英数字、一部の記号（@/./+/-/_）が使用できます。'
    >        self.fields['password1'].label = 'パスワード'
    >        self.fields['password1'].help_text = '' # テンプレート側で表示
    >        self.fields['password2'].label = 'パスワード（確認用）'
    >        self.fields['password2'].help_text = '確認のため、もう一度同じパスワードを入力してください。'
    >        # 必須属性の追加
    >        for field in self.fields.values():
    >            field.widget.attrs['required'] = True
    >```
    >
    > **--- accounts/views.py (修正) ---**
    >
    > `UserCreationForm`を自作の`SignUpForm`に差し替えます。
    >
    >```python
    >from django.shortcuts import render, redirect
    >from .forms import SignUpForm # 変更
    >
    >def signup_view(request):
    >    if request.method == 'POST':
    >        form = SignUpForm(request.POST) # 変更
    >        if form.is_valid():
    >            form.save()
    >            return redirect('accounts:login')
    >    else:
    >        form = SignUpForm() # 変更
    >    return render(request, 'accounts/signup.html', {'form': form})
    >```
    
    </details>
    <br>
    
2.  **テンプレートの改良**
    
    `{{ form.as_p }}`をやめ、フィールドを個別にレンダリングすることで、HTML構造を完全に制御します。
    
    <details>
    <summary>▶︎ 実際のコードを見る (Click to see code)</summary>
    
    > **--- accounts/templates/accounts/signup.html (修正) ---**
    >
    >```html
    >{% extends 'base.html' %}
    >{% block title %}アカウント作成{% endblock %}
    >{% block content %}
    ><h1 class="page-title">アカウント作成</h1>
    ><form method="post" class="memo-form">
    >    {% csrf_token %}
    >    {% for field in form %}
    >      <div class="form-field">
    >        <label for="{{ field.id_for_label }}">{{ field.label }}</label>
    >        {% if field.name == 'password1' %}
    >          <div class="helptext">
    >            <ul>
    >              <li>パスワードは最低8文字以上で設定してください。</li>
    >              <li>ユーザー名や、ありふれたパスワードは使用できません。</li>
    >              <li>数字のみのパスワードは使用できません。</li>
    >            </ul>
    >          </div>
    >        {% endif %}
    >        {{ field }}
    >        {% if field.help_text and field.name != 'password1' %}
    >          <div class="helptext">{{ field.help_text }}</div>
    >        {% endif %}
    >        {% for error in field.errors %}
    >          <div class="errorlist-field">{{ error }}</div>
    >        {% endfor %}
    >      </div>
    >    {% endfor %}
    >    {% if form.non_field_errors %}
    >      <div class="errorlist-field" style="margin-bottom: 20px;">
    >        {% for error in form.non_field_errors %}{{ error }}{% endfor %}
    >      </div>
    >    {% endif %}
    >    <button type="submit" class="btn btn-primary">登録する</button>
    ></form>
    ><p class="account-link">すでにアカウントをお持ちですか？ <a href="{% url 'accounts:login' %}">ログイン</a></p>
    >{% endblock %}
    >```
    
    </details>
    <br>

> **【解決！】**
>
> これで、完全に日本語化され、デザインも整った美しいサインアップフォームが完成しました！

### STEP 5: ログイン・ログアウト機能の実装

Djangoの組み込みビューを活用し、効率的にログイン・ログアウト機能を実装します。

<details>
<summary>▶︎ 実際のコードを見る (Click to see code)</summary>

> **--- accounts/urls.py (修正) ---**
>
> `LoginView`, `LogoutView`をインポートし、URLを追加します。
>
>```python
>from django.urls import path
>from django.contrib.auth import views as auth_views
>from . import views
>
>app_name = 'accounts'
>urlpatterns = [
>    path('signup/', views.signup_view, name='signup'),
>    path('login/', auth_views.LoginView.as_view(template_name='accounts/login.html'), name='login'),
>    path('logout/', auth_views.LogoutView.as_view(), name='logout'),
>]
>```
>
> **--- accounts/templates/accounts/login.html (新規作成) ---**
>
>```html
>{% extends 'base.html' %}
>{% block title %}ログイン{% endblock %}
>{% block content %}
><h1 class="page-title">ログイン</h1>
>{% if form.non_field_errors %}
>  <div class="errorlist">
>    {% for error in form.non_field_errors %}<p>{{ error }}</p>{% endfor %}
>  </div>
>{% endif %}
><form method="post" class="memo-form">
>    {% csrf_token %}
>    {{ form.as_p }}
>    <button type="submit" class="btn btn-primary">ログイン</button>
></form>
><p class="account-link">アカウントをお持ちでないですか？ <a href="{% url 'accounts:signup' %}">アカウント作成</a></p>
>{% endblock %}
>```
>
> **--- memoproject/settings.py (追記) ---**
>
> ファイルの末尾に、リダイレクト先のURLを追記します。
>
>```python
># ログイン成功後にリダイレクトされるURL
>LOGIN_REDIRECT_URL = '/' 
># ログアウト成功後にリダイレクトされるURL
>LOGOUT_REDIRECT_URL = '/accounts/login/'
>```

</details>
<br>

### STEP 6: パスワード要件の緩和（任意）

開発をスムーズに進めるため、Djangoの厳格なパスワード要件を一時的に緩める方法を解説します。**本番環境での使用は非推奨です。**

<details>
<summary>▶︎ 実際のコードを見る (Click to see code)</summary>

> **--- memoproject/settings.py (修正) ---**
>
> `AUTH_PASSWORD_VALIDATORS`リストから、不要なバリデーターをコメントアウトします。ここでは「ユーザー名と似てはダメ」「よくあるパスワードはダメ」という2つを無効化する例を示します。
>
>```python
>AUTH_PASSWORD_VALIDATORS = [
>    # {
>    #     'NAME': 'django.contrib.auth.password_validation.UserAttributeSimilarityValidator',
>    # },
>    {
>        'NAME': 'django.contrib.auth.password_validation.MinimumLengthValidator',
>    },
>    # {
>    #     'NAME': 'django.contrib.auth.password_validation.CommonPasswordValidator',
>    # },
>    {
>        'NAME': 'django.contrib.auth.password_validation.NumericPasswordValidator',
>    },
>]
>```
>
> > **完全に無効化するには？**: `AUTH_PASSWORD_VALIDATORS = []`と空のリストにすれば、全てのチェックが無効になります。

</details>
<br>

これでユーザー認証機能の基本は完成です！お疲れ様でした。



# Django作成手順やエラー解決のためのチートシート

# よく使うコマンド集
* サーバーの起動<br>
  `python manage.py runserver`

* 仮想環境起動<br>
  `環境名/Scripts/activate`

* マイグレート関係<br>
`python manage.py makemigrations`<br>
`python manage.py migrate`
<br><br>



# 作成手順
## 1. 仮想環境の構築、起動
&emsp;　実行したい環境を整える。以下略
<br><br>

## 2. プロジェクトの作成・設定
1. プロジェクトファイルの作成<br>
   `mkdir プロジェクト名`<br>
   `cd プロジェクト名`<br>
   `django-admin startproject config .`

2. 設定の書き換え
   config/setting.pyの以下項目を編集
   * TEMPLATES<br>`'DIRS':[os.path.join(BASE_DIR,'格納フォルダ名')],`
   * DATABASE<br>使用するデータベースに対応する設定を
   * 言語・時間設定<br>`LANGUAGE_CODE = 'ja'`<br>`TIME_ZONE = 'Asia/Tokyo'`
   <br><br>
3. アプリケーション作成
   `python manage.py startapp アプリケーション名`
   * 作成したアプリをconfig/setting.pyのINSTALLED_APPSに追加<br>
    `'アプリ名'` 
    <br><br>

## 3. アプリ作成の手引き
   1. urlsを書こう<br>
      config/urls.pyについて<br>
      ホスト名以下のURLに対応するルーティングを行う<br>
      基本的に`path('xxx/',include('YYY.urls')),`の形でそれぞれのアプリ毎のurls.pyへ仲介する
      * アプリ名/urls.pyについて<br>
      config/urls.pyで拾った後のURLに対応するルーティングを行う<br>
      設定として`app_name = 'アプリ名'`の記述が必要
      基本的に`path('xxx/','views.クラス名.as_view()',name='紐づける名前')),`の形<br><br>

   2. viewsを書こう<br>
      urlsの記述に対応したアプリ名/views.pyのclassを作ります<br>
      与えられたURLから表示するテンプレートを見つけ、データを渡す役割を担う
         ```python
         class クラス名(継承ビュー):
               template_name = 'テンプレートの保存パス'
         ```
      * 汎用ビュー
         * `TemplateView` 一般的に
         * `FormView`　モデルを用いるわけではないフォームに
         * `CreateView` モデルを使うフォームに
         * `UpdateView`　モデルのアップデートに
         * `DetailView`　モデルの表示に
         * `DeleteView`　モデルの削除に
         * `ListView` 　モデルのlist表示に

      * 各汎用ビューのtips
         * 全体的に<br>
            * `template_name`は設定しないと決まった命名規則で設定される。<br>
            * `model = モデル名`で自動的に作られるレコードは`template`では、単数なら`object`、複数なら`object_list`に格納される。<br>
            一応view内で`context_object_name = '変数名'`で格納される変数名を指定できる<br>

            * GETまたはPOSTで得られるデータの取り出し<br>
            `request.GET.get('名前')`<br>
            `request.POST.get('名前')`


            * クエリ操作は以下の記述
               ```python
               def get_queryset(self):
                  records = 検索対象のモデル名.object.all()
                  records = records.filter(カラム名__icontains=検索に用いる値)

               ```


            * 値の受け渡しは以下のように`context`で渡す。
               ```python
               def get_context_data(self, **kwargs):
               context = super().get_context_data(**kwargs)
               context['xxx'] = "yyyy"
               return context
               ```
         * `CreateView`<br>
         入力成功後の移動先は`success_url`で指定<br>
         入力されたフォームがバリデーションを通過した（書き込み成功）ときの処理は
            ```python
            def form_valid(self,form):
            #処理を書く
            return super().form_valid(form)
            ```
            失敗した（合わないデータの入力時）は
            ```pyrhon
            def form_invalid(self, form):
            #処理を書く
            return super().form_invalid(form)
            ```

         * `DetailView`<br>
         urls.pyで`/<型名:pk>`で受け取った値で主キーを調べ対応するレコードを引き渡す

         * `ListView`<br>
            * 次の記述で指定したモデルのリストが作られる。
               ```python
               class クラス名(ListView):
                  model = モデル名
               ```
            * 次の変数を指定でページ分割ができる
            `paginate_by = 一ページのレコード数`
            

   3. modelsを書こう<br>
      アプリ名/models.pyにモデルを書きましょう。<br>このモデルはデータベースのテーブルと対応します<br>
         ```python
         class モデル名(models.Model):
            要素名 = models.フィールドタイプ('表示用の名前',オプション)
         ```
         また、リレーション用では次の記述方法です。
         ```python
         要素名 = models.フィールドタイプ(結びつけるモデル名,on_delete=xxx,...)
         ```
      フィールドタイプ並びにオプションの代表例は以下です。<br>
      * フィールドタイプ<br>
        * `BooleanField`
        * `CharField`
        * `EmailField`
        * `ImageField`
        * `IntegerField`
        * `TextField`<br><br>
      * リレーション用フィールドタイプ
         * `OneToOneField`1対1
         * `ForeignKey`外部キー
         * `ManyToManyField`多対多
      * フィールドオプション
        * `null`     trueでnull許容
        * `blank`    trueで空欄許容
        * `defalut`  デフォルト値
        * `error_messages` エラーメッセージを設定
        * `unique`   trueで一意制約<br><br>

   4. formsを書こう<br>
   アプリ名/forms.pyにフォームに用いるクラスを作りましょう。<br>
      * 定義されたmodelに関する場合
         ```python
         class クラス名(forms.ModelForm):
            class Meta:
               model = フォームとして入力したい定義されたモデル名
               fields = 使いたい要素名のタプル
         ```
      <br>
   5. templateを書こう
   views.pyによって指定されたパスに表示用のhtmlファイルを書きましょう。<br>
   基本はhtmlと変わりません。<br>
   Djangoの記述として`{{ 変数名 }}`と`{% タグ名 %}`があります。

   * タグ名について<br>
   Djangoが用意してくれる機能を使うことができます。代表的な例は以下です。

     * `{% block テンプレート名 %} {% endblock %}` 子テンプレートで置き換え
     * `{% comment %}　{% endcomment %}`コメントアウト
     * `{% csrf_token %}` CRSF防止、フォームには置くように
     * `{% for x in xxx %} {% endfor %}`for文
     * `{% if %} {% elif %} {% else %} {% endif %}`if文
     * `{% now %}`現在時刻

   6. アカウント昨日の実装について
      * Djangoには`User`モデルと`UserCreationForm`が用意されており、それを`CreationView`を継承したviewに設定すればよい。<br>また、データの格納先はデータベースのauth_userテーブル

      * `User`モデルには、ほかに`AbstractUser`と`AbstractBaseUser`があり、オリジナルのテーブルを作る場合はこちらを継承して作る。<br>
      その際には`setting.py`に`AUTH_USER_MODEL = 'アプリ名.作ったユーザー用クラス名'`を書く必要がある
         * `AbstractUser`の初期フィールド<br>
         `User`と近いとのこと（要検索）

         * `AbstractBaseUser`の初期フィールド<br>
         `password` 　パスワード<br>
         `last_login`　最終ログイン

         加えて、自分でユーザーモデルを作ったときには、`BaseUserManager`を継承したクラスも作る必要がある。これを継承して作ったユーザーモデルで呼び出す。

      * フォームは継承で書き換えることができる。

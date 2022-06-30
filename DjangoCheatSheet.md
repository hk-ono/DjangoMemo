# Django作成手順やエラー解決のためのチートシート

# よく使うコマンド集
* サーバーの起動<br>
  `python manage.py runserver`

* `環境名/Scripts/activate`

* マイグレート関係<br>`python manage.py makemigrations`<br>`python manage.py migrate`
<br><br>



# 作成手順
## 1. 仮想環境の構築、起動
&emsp;　実行したい環境を整える。以下略
<br><br>

## 2. プロジェクトの作成・設定
1. プロジェクトファイルの作成<br>
   `django-admin startproject プロジェクト名`

2. 設定の書き換え
   conf/setting.pyの以下項目を編集
   * TEMPLATES<br>`'DIRS':[os.path.join(BASE_DIR,'格納フォルダ名')],`
   * DATABASE<br>使用するデータベースに対応する設定を
   * 言語・時間設定<br>`LANGUAGE_CODE = 'ja'`<br>`TIME_ZONE = 'Asia/Tokyo'`
   <br><br>
3. アプリケーション作成
   `python manage.py startapp アプリケーション名`
   * 作成したアプリをconf/setting.pyのINSTALLED_APPSに追加<br>
    `'アプリ名'` 
    <br><br>

## 3. アプリ作成の手引き
   1. urlsを書こう<br>
      conf/urls.pyについて<br>
      ホスト名以下のURLに対応するルーティングを行う<br>
      基本的に`path('xxx/',include('YYY.urls')),`の形でそれぞれのアプリ毎のurls.pyへ仲介する
      * アプリ名/urls.pyについて<br>
      conf/urls.pyで拾った後のURLに対応するルーティングを行う<br>
      設定として`app_name = 'アプリ名'`の記述が必要
      基本的に`path('xxx/','views.クラス名.as_view()',name='紐づける名前')),`の形<br><br>

   2. viewsを書こう<br>
      urlsの記述に対応したアプリ名/views.pyのclassを作ります<br>
      与えられたURLから表示するテンプレートを見つけ、データを渡す役割を担う
         ```python
         class クラス名(継承ビュー):
               template_name = 'テンプレートの保存パス'
         ```
      代表的な各ビューでの記述は別欄で書きます。

   3. modelsを書こう<br>
      アプリ名/models.pyにモデルを書きましょう。<br>このモデルはデータベースのテーブルと対応します<br>
         ```python
         class モデル名(models.Model):
            要素名 = models.フィールドタイプ('表示用の名前',オプション)
         ```
      フィールドタイプ並びにオプションの代表例は以下です。<br>
      * フィールドタイプ<br>
        * `BooleanField`
        * `CharField`
        * `EmailField`
        * `ImageField`
        * `IntegerField`
        * `TextField`<br><br>
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


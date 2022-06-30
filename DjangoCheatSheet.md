Django作成手順やエラー解決のためのチートシート

# よく使うコマンド集
* サーバーの起動<br>
  `python manage.py runserver`

* 

# 作成手順
## 1. 仮想環境の構築、起動
&emsp;　実行したい環境を整える。以下略
<br><br>

## 2. プロジェクトの作成
1. プロジェクトファイルの作成<br>
   `django-admin startproject プロジェクト名`

2. 設定の書き換え
   conf/setting.pyの以下項目を編集
   * TEMPLATES<br>`'DIRS':[os.path.join(BASE_DIR,'格納フォルダ名')],`
   * DATABASE<br>使用するデータベースに対応する設定を
   * 言語・時間設定<br>`LANGUAGE_CODE = 'ja'`<br>`TIME_ZONE = 'Asia/Tokyo'`

3. アプリケーション作成
   `python manage.py startapp アプリケーション名`
   * 作成したアプリをconf/setting.pyのINSTALLED_APPSに追加<br>
    `'アプリ名だけでも良い'` 







&emsp; 
&emsp;　
&emsp;　
<br><br>　
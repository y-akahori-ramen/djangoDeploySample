# djangoDeploySample

`django-admin startproject プロジェクト名` で作成したDjangoのプロジェクト初期状態をDockerを使用してnginx+uwsgi+djangoという構成でデプロイするサンプルです。  

## デプロイする方法

infraフォルダで`docker-compose up`してください。  
`http://localhost/`でアクセスできます。

## Dockerの構成
２つのコンテナと１つのデータボリュームで構成しています。

- webappコンテナ
    - Djangoアプリケーション+uWSGI
    - webappコンテナに立てたuWSGIを介して、nginxコンテナ側と通信する。  
      ※uWSGIはnginxなどのWebサーバとDjango等のPython製Webアプリケーションとの通信を仲介する。  
      nginx ←→ uWSGI ←→ Django　という通信のがなれとなっている。
- nginxコンテナ
    - webサーバ
    - Djangoアプリの静的コンテンツのホスティング
- django_staticsデータボリューム
    - Djangoアプリケーションの静的コンテンツを入れるデータボリューム
    - dango_staticsデータボリュームはwebappコンテナとnginxコンテナで共有しており、webapp側で静的コンテンツの作成を行い、nginx側はそれを読み出す。  

## 特筆事項
### 大規模でパブリックな環境での運用は想定していません
- `python manage.py check --deploy` によるデプロイ検証への対処はいれていません。
- ssl対応は入っていません。
- データベースはSQLiteです。（startprojectで作成したまま）
- シークレットキーがそのままgit煮上がっている

等、本番で使用するには色々と問題があります。  
あくまでサンプルです。

### settings.pyに手を入れています

`django-admin startproject プロジェクト名` で作成した状態からsettings.pyだけ変更を加えています。  
静的コンテンツのルートを環境変数指定できるように以下を追加しています。

```py
STATIC_ROOT = os.getenv('STATIC_ROOT')
```


## 特に参考になったサイト
- [Setting up Django and your web server with uWSGI and nginx](https://uwsgi.readthedocs.io/en/latest/tutorials/Django_and_nginx.html)
- [DjangoアプリをDockerで運用する](https://roy-n-roy.github.io/Docker/%E3%82%B3%E3%83%B3%E3%83%86%E3%83%8A%E5%8C%96/Django/#django_1)
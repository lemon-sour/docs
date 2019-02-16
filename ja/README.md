## lemon-sour.js とは何か？

Web サイトは Web サーバーをデプロイすれば、更新されますが、設置型のコンテンツ（PC やサイネージ、展示系）はアプリケーションを PC に入れ直す必要があります。

私のゴールは、なるべくシンプルにアプリケーションを入れ替えられるようにすることです。

加えて、どんな OS でも動作し、 node.js だけが入っていれば動作するコマンドラインツールとして作っています。


## どのように動作するか？

必要なのは、コンテンツを配信したい PC などに node.js を入れ、 lemon-sour.js of npm module をインストールします。

あとは、設定ファイルである yml を lemon-sour コマンドツールに渡し、アップデート情報などを取得させます。

[lemon-sour.js/example/app_basic_archive at master · lemon-sour/lemon-sour.js](https://github.com/lemon-sour/lemon-sour.js/tree/master/example/app_basic_archive)


## 主な機能

* yml ファイルでシンプルに記述できること
* アップデートがあるないなどのイベントにフックできること
* コマンドラインツール


## 図解




## 設定ファイルの解説

### lemon-sour.js の実行

```bash
$ lemon-sour --yml ./index.yml
```

### index.yml

[lemon-sour.js/index.yml at master · lemon-sour/lemon-sour.js](https://github.com/lemon-sour/lemon-sour.js/blob/master/example/app_basic_archive/index.yml)

```yaml
version: 1.0
jobs:
  install_app_a:
    name: app_a
    latest_json_url: http://localhost:3000/app_basic_archive/latest.json
    is_archive: true
    output_path: C:/lemon-sour/app_basic_archive
    events:
      checking_for_update:
      update_not_available:
      update_available:
      download_progress:
      update_downloaded:
        steps:
          - run:
              name: Open index.txt of file
              command: tail C:/lemon-sour/app_basic_archive/index.txt
              sync: true
      error:

workflows:
  main:
    jobs:
      - install_app_a
```

### latest.json

[lemon-sour.js/latest.json at master · lemon-sour/lemon-sour.js](https://github.com/lemon-sour/lemon-sour.js/blob/master/example/app_basic_archive/latest.json)

```json
{
  "latestVersion": "1.0.0",
  "fileUrl": "http://localhost:3000/app_basic_archive/index.zip",
  "sha1": "3f786850e387550fdab836ed7e6dc881de23001b",
  "releaseDate": "2019-01-18 19:29:45"
}
```

### index.zip

[lemon-sour.js/index.zip at master · lemon-sour/lemon-sour.js](https://github.com/lemon-sour/lemon-sour.js/blob/master/example/app_basic_archive/index.zip)




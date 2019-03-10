## lemon-sour.js とは何か？

Web サイトは Web サーバーをデプロイすれば、更新されますが、設置型のコンテンツ（PC やサイネージ、展示系）はアプリケーションを PC に入れ直す必要があります。

私のゴールは、なるべくシンプルにアプリケーションを入れ替えられるようにすることです。

加えて、どんな OS でも動作し、 node.js だけが入っていれば動作するコマンドラインツールとして作っています。


## どのように動作するか？

必要なのは、コンテンツを配信したい PC などに node.js を入れ、 lemon-sour.js of npm module をインストールします。

あとは、設定ファイルである yml を lemon-sour コマンドツールに渡し、アップデート情報などを取得させます。


## 主な機能

* yml ファイルでシンプルに記述できること
* アップデートがあるないなどのイベントにフックできること
* コマンドラインツール


## 図解


## lemon-sour.js の実行

```bash
$ lemon-sour --yml config.yml
```


## Sample Configuration with Basic Jobs

### config.yml

[lemon-sour.js/config.yml at master · lemon-sour/lemon-sour.js](https://github.com/lemon-sour/lemon-sour.js/blob/master/example/app_basic_archive/config.yml)

```yaml
# バージョン
version: 1.0

# jobs にはデプロイしたいアプリケーション分の job を記載していきます
jobs:
  # `install_app_a` はどんな名前でも問題ありません
  # workflows → main → jobs → `install_app_a` の名前と合わせる必要はあります
  install_app_a:
    name: app_a
    # latest.json の置いてある absolute な URL を書きます
    latest_json_url: http://localhost:3000/app_basic_archive/latest.json
    # ダウンロード対象のファイルが `.zip` などのアーカイブファイルかどうかのフラグ
    is_archive: true
    # app_a のインストール先ディレクトリ
    output_path: C:/lemon-sour/app_basic_archive
    # 各種イベント
    events:
      # 更新があるかの確認イベント
      checking_for_update:
      # アップデートがなかったときに実行されるイベント
      update_not_available:
      # アップデートがあったときに実行されるイベント
      update_available:
      # アップデートファイルをダウンロードしている最中のプログレスイベント
      download_progress:
      # アップデートファイルのダウンロードが完了したときのイベント
      update_downloaded:
        # イベントが実行されるタイミングで、実行したい処理を steps の中に書きます
        steps:
          # ここを増やせばいろんなコマンドラインを実行できます
          - run:
              name: Open index.txt of file
              # コマンドは OS によって動く動かないがあるので、対象の OS で動くコマンドを実行してください
              command: tail C:/lemon-sour/app_basic_archive/index.txt
              # コマンドを同期モードで実行するには sync を true にします
              # これは複数の run を書いた場合に、順番を担保させるのに有効です
              sync: true
      # オフラインや sha1 の値の不一致など、完了できなかったときのイベント
      error:

# ワークフローは、上記で定義したアプリケーションを実際に実行します
workflows:
  main:
    # ここの jobs に書かれた順番のとおりに実行されるので、上記の jobs で書いたアプリケーションの順番には左右されません
    # order はここに書かれた順番です
    jobs:
      - install_app_a
```

### latest.json

`latest.json` はアプリケーションのバージョンじゃファイルが置いてある場所が記載されたファイルです。

[lemon-sour.js/latest.json at master · lemon-sour/lemon-sour.js](https://github.com/lemon-sour/lemon-sour.js/blob/master/example/app_basic_archive/latest.json)

```json
{
  "latestVersion": "1.0.0",
  "fileUrl": "http://localhost:3000/app_basic_archive/index.zip",
  "sha1": "3f786850e387550fdab836ed7e6dc881de23001b",
  "releaseDate": "2019-01-18 19:29:45"
}
```

もし、 sha1 のハッシュ値を計算する方法が準備できていない場合は以下のコードを参考に算出してください。

```javascript
'use strict'

const crypto = require('crypto');
const fs = require('fs');
const shasum = crypto.createHash('sha1');

const app_a = __dirname + '/path/to/file.txt';
fs.readFile(app_a, (err, data) => {
  shasum.update(data);
  console.log(app_a, shasum.digest('hex'));
});
```

[lemon-sour.js/sha1.js at master · lemon-sour/lemon-sour.js](https://github.com/lemon-sour/lemon-sour.js/blob/master/scripts/sha1.js)


## Sample Configuration with Parallel Jobs


## Sample Configuration with Sequential Jobs



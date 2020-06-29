## セットアップ手順

1.  docker/php/Dockerfile の DOCKER_UID をホストと合わせる

    1. どこでもよいのでコマンドラインで下記のコマンドを実行する

       ```
       id -u
       ```

    1. docker/php/Dockerfile の ARG DOCKER_UID=1000 の右辺を ↑ で調べた値にする

    - Linux ではこれをやらないとゲスト側で作成したファイルをホスト側で編集できなくなる
    - Mac ではこの手順は不要との説もある
    - Windows の人は WSL (Windows Subsystem for Linux) を使おう

1.  docker-compose.yml がある場所で下記のコマンドを実行する。初回起動には時間がかかる

    ```
    docker-compose up -d
    ```

    - 下記のようなメッセージが出たら成功

      ```
      Creating network "quelcode-cakephp_default" with the default driver
      Creating quelcode-cakephp_phpmyadmin_1 ... done
      Creating quelcode-cakephp_nginx_1      ... done
      Creating quelcode-cakephp_mysql_1      ... done
      Creating quelcode-cakephp_php_1        ... done
      ```

1.  起動中の php コンテナの bash を実行する

    ```
    docker-compose exec php bash
    ```

    - 下記のようなプロンプトに切り替われば成功

      ```
      docker@df8275e6f1f9:/var/www/html$
      ```

1.  php コンテナの bash で cakephp を install する

    1. php コンテナの bash で /var/www/html/mycakeapp に移動する

       ```
       docker@df8275e6f1f9:/var/www/html$ cd mycakeapp
       docker@df8275e6f1f9:/var/www/html/mycakeapp$
       ```

    1. 下記のコマンドを実行する

       ```
       docker@e6e656dc2f0d:/var/www/html/mycakeapp$ composer install
       ```

       - こちらも時間がかかる。質問プロンプトが出たら Y と回答する

         ```
         Set Folder Permissions ? (Default to Y) [Y,n]? Y
         ```

1.  cakephp アプリをブラウザで表示する
    - ブラウザで http://localhost:10080 にアクセスする
    - cakephp の赤いページが表示されたらセットアップ成功

## 起動中のコンテナの bash を終了する方法

- コンテナの bash で下記のショートカットキーを入力する

  ```
  ctrl + p + q
  ```

  - コンテナの bash で exit コマンドを打つとコンテナ自体が終了してしまう恐れがあるので非推奨

## migration を行う方法

- php コンテナの bash で /var/www/html/mycakeapp に移動して下記のコマンドを実行する

  ```
  docker@e6e656dc2f0d:/var/www/html/mycakeapp$ ./bin/cake migrations migrate
  ```

  - 同様に bake 等も実行可能

## ブラウザで テキストに記載されている url にアクセスする方法

- 下記のように読みかえてアクセスする。nginx コンテナ の port とドキュメントルートを設定しているため
  - http://localhost/mycakeapp/hello.html ⇒ http://localhost:10080/hello.html
  - http://localhost/mycakeapp/auction/add ⇒ http://localhost:10080/auction/add

## ブラウザで オークションアプリを表示する方法(課題用のブランチにおいて)

- http://localhost:10080/auction にアクセスする
  - http://localhost:10080/users/add からユーザを作成できる
  - clone 直後の master ブランチには存在しない。課題用のブランチにおいて migration を行う必要がある

## ブラウザで phpMyAdmin を表示する方法

- http://localhost:10081 にアクセスする
  - root 権限で操作可能

## nginx のドキュメントルートを変更する方法

- docker/nginx/default.conf を編集することで nginx のドキュメントルートを変更可能

  ```diff
  server {
  - root  /var/www/html/mycakeapp/webroot;
  + root  /var/www/html/mylaravelapp/public;
    index index.php index.html;
    ...
  ```

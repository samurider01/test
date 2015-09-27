# CentOS6へRedmine3.1インストール

  ## 概要

    ターゲット環境(条件)
    - OS: CentOS6.5 (on Vagrant)
    - メモリ: 2GB (1GBだとRubyのコンパイルで落ちる)
    - Redmineバージョン: 3.1.1 (2015/09/20時点の最新)
    - DB: MySQL

  ## Linuxユーザ追加(未検証)
    ```
    # visudo
    ------------
    (一番下に↓の行を追加する)
    %redmine ALL=(ALL) ALL
    ------------
    
    # passwd redmine
    # 
    # su - redmine
    # sudo ls -l /root
    ```

  ## 最新Ruby+Railsをインストール
  
      Redmineの最新版では、Ruby1.9以降のバージョンが必要になるためバージョンアップします。
  
    ### コンパイルに必要なツール群をインストール
    ```
    # su -
    # yum groupinstall -y 'Development Tools'
    # yum install -y vim wget
    # yum install -y readline-devel libyaml-devel
    # yum install -y gdbm-devel tcl-devel openssl-devel db4-devel
    ```

    ### rbenvのインストール
    ```
    # su - redmine
    $ cd 
    $ git clone git://github.com/sstephenson/rbenv.git ~/.rbenv
    $ mkdir -p ~/.rbenv/plugins
    $ git clone https://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build
    $ cd ~/.rbenv/plugins/ruby-build
    $ ./install.sh
    ```
  
    ### rbenvのパスを通す
    ```
    $ echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bash_profile
    $ echo 'eval "$(rbenv init -)"' >> ~/.bash_profile
    $ source ~/.bash_profile
    ```

    ### rbenvのバージョンチェック
    ```
    $ rbenv
    ```
  
    ### Ruby最新版のインストール
    ```
    $ rbenv install -l
    $ rbenv install 2.2.3
    $ rbenv rehash
    $ rbenv global 2.2.3
    $ ruby -v
    ```
  
    ### Railsのインストール
    ```
    $ gem list -r --all rails
    $ rbenv exec gem install rails
    $ rbenv rehash
    $ rails -v
    ```
    ⇒ Rails 4.2.4 が表示される
  
  
    * 参考サイト：
    http://qiita.com/icche/items/801ce357d41fd0137b79
    ※現在(15/09/25)の安定版は「2.2.3」


  ## Redmineのインストール
    ### Redmineソースのダウンロードと配置
    ```
    # su -
    # cd /usr/local/src
    # wget http://www.redmine.org/releases/redmine-3.1.1.tar.gz
    # tar zxvf redmine-3.1.1.tar.gz
    # mv redmine-3.1.1 /opt/redmine
    # chown -R redmine:redmine /opt/redmine
    ```

    ### MySQLインストール
    ```
    # yum -y install mysql-server mysql-devel
    # service mysqld start
    # chkconfig mysqld on
    ```

    ### 空のDBとそのDBに接続するためのユーザ作成
    ```
    # mysql
    mysql> create database redmine character set utf8;
    mysql> create user 'redmine'@'localhost' identified by 'my_password';
    mysql> grant all privileges on redmine.* to 'redmine'@'localhost';
    mysql> quit
    ```

    ### DB接続設定ファイルを作成
    ```
    $ su - redmine
    $ cd /opt/redmine
    $ cp config/database.example.yml. config/database.yml
    $ vim config/database.yml
    ※"production"環境用のデータベース設定を修正
    -----
    production:
      adapter: mysql2
      database: redmine
      host: localhost
      username: redmine
      password: my_password
    -----
    ```

    ### Bundlerのインストール
    ```
    $ cd /opt/redmine
    $ gem install bundler
    $ bundle install --without development test postgresql sqlite
    ```

    ### セッションストア秘密鍵を生成
    ```
    $ rake generate_secret_token
    ```

    ### データベース上にテーブルを作成
    ```
    $ RAILS_ENV=production rake db:migrate
    $ RAILS_ENV=production rake redmine:load_default_data
    ```

    ### パーミッションの設定
    ```
    $ mkdir tmp public/plugin_assets 
    $ sudo chown -R redmine:redmine files log tmp public/plugin_assets
    $ sudo chmod -R 755 files log tmp public/plugin_assets
    ```

    ### WEBrickによるwebサーバを起動
    ```
    $ ruby bin/rails server webrick -e production -b 192.168.33.10
    ```
    ※末尾はサーバのIP

    ### ブラウザからRedmineにログイン
    - URL : http://192.168.33.10:3000
    - login : admin
    - password : admin

    ### 公式サイトの手順を参考に下記設定も実施する
    - ログの設定
    - SMTPサーバの設定
    - バックアップ


  ## 参考サイト:
  - 公式
  http://redmine.jp/guide/RedmineInstall/

  - CentOS6にRedmineを導入したときの手順メモ
  http://www.aetherworks.org/article/112066375.html


  ## (以降は作成中手順)

  ## Apacheインストール
    ### Apacheインストール
    ```
    yum -y install httpd mod_ssl
    ```

    ### httpd.confに下記追加
    ```
    vim /etc/conf/httpd.conf
    ※末尾に追加
    LoadModule passenger_module /root/.rbenv/versions/2.2.3/lib/ruby/gems/2.2.0/gems/passenger-5.0.20/buildout/apache2/mod_passenger.so
    <IfModule mod_passenger.c>
      PassengerRoot /root/.rbenv/versions/2.2.3/lib/ruby/gems/2.2.0/gems/passenger-5.0.20
      PassengerDefaultRuby /root/.rbenv/versions/2.2.3/bin/ruby
    </IfModule>
    ```


  ## 遭遇したエラー
    ### エラー１
    ```
    $ bundle install --without development test postgresql sqlite
    
    [!] There was an error parsing `Gemfile`: compile error - syntax error, unexpected ':', expecting $end
    gem 'tzinfo-data', platforms: [:mingw, :x64_mingw, :mswin, :jruby]
                                 ^. Bundler cannot continue.

     #  from /opt/redmine/Gemfile:19
     #  -------------------------------------------
     #  # Windows does not include zoneinfo files, so bundle the tzinfo-data gem
     >  gem 'tzinfo-data', platforms: [:mingw, :x64_mingw, :mswin, :jruby]
     #  gem "rbpdf", "~> 1.18.6"
     #  -------------------------------------------
    
    
    # gem list -r --all | grep rbpdf
    rbpdf (1.18.6, 1.18.5, 1.18.4, 1.18.3, 1.18.2, 1.18.1, 1.18.0)
    
    # rbenv exec gem install rbpdf
    Fetching: rbpdf-1.18.6.gem (100%)
    Successfully installed rbpdf-1.18.6
    Parsing documentation for rbpdf-1.18.6
    ```

    ### エラー2
    ```
    [root@vagrant-centos65 redmine]# ruby script/rails server webrick -e production
    script/rails no longer exists, please use bin/rails instead.
    
    ruby bin/rails server webrick -e production
    ```


    ### エラー3
    ```
    passenger-install-apache2-module
    ～中略～
    --------------------------------------------
    
    Installation instructions for required software
    
     * To install Curl development headers with SSL support:
       Please install it with yum install libcurl-devel
    
     * To install Apache 2 development headers:
       Please install it with yum install httpd-devel
    
     * To install Apache Portable Runtime (APR) development headers:
       Please install it with yum install apr-devel
    
     * To install Apache Portable Runtime Utility (APU) development headers:
       Please install it with yum install apr-util-devel
        
    If the aforementioned instructions didn't solve your problem, then please take
    a look at our documentation for troubleshooting tips:
    ～中略～
    
    yum install libcurl-devel
    yum install httpd-devel
    yum install apr-devel
    yum install apr-util-devel
    ```


    ## エラー4
    ```
    c++: internal compiler error: Killed (program cc1plus)
    ```
    ⇒仮想環境のメモリを2GBにスケールアップ


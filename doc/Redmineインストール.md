# CentOS6��Redmine3.1�C���X�g�[��

  ## �T�v

    �^�[�Q�b�g��(����)
    - OS: CentOS6.5 (on Vagrant)
    - ������: 2GB (1GB����Ruby�̃R���p�C���ŗ�����)
    - Redmine�o�[�W����: 3.1.1 (2015/09/20���_�̍ŐV)
    - DB: MySQL

  ## Linux���[�U�ǉ�(������)
    ```
    # visudo
    ------------
    (��ԉ��Ɂ��̍s��ǉ�����)
    %redmine ALL=(ALL) ALL
    ------------
    
    # passwd redmine
    # 
    # su - redmine
    # sudo ls -l /root
    ```

  ## �ŐVRuby+Rails���C���X�g�[��
  
      Redmine�̍ŐV�łł́ARuby1.9�ȍ~�̃o�[�W�������K�v�ɂȂ邽�߃o�[�W�����A�b�v���܂��B
  
    ### �R���p�C���ɕK�v�ȃc�[���Q���C���X�g�[��
    ```
    # su -
    # yum groupinstall -y 'Development Tools'
    # yum install -y vim wget
    # yum install -y readline-devel libyaml-devel
    # yum install -y gdbm-devel tcl-devel openssl-devel db4-devel
    ```

    ### rbenv�̃C���X�g�[��
    ```
    # su - redmine
    $ cd 
    $ git clone git://github.com/sstephenson/rbenv.git ~/.rbenv
    $ mkdir -p ~/.rbenv/plugins
    $ git clone https://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build
    $ cd ~/.rbenv/plugins/ruby-build
    $ ./install.sh
    ```
  
    ### rbenv�̃p�X��ʂ�
    ```
    $ echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bash_profile
    $ echo 'eval "$(rbenv init -)"' >> ~/.bash_profile
    $ source ~/.bash_profile
    ```

    ### rbenv�̃o�[�W�����`�F�b�N
    ```
    $ rbenv
    ```
  
    ### Ruby�ŐV�ł̃C���X�g�[��
    ```
    $ rbenv install -l
    $ rbenv install 2.2.3
    $ rbenv rehash
    $ rbenv global 2.2.3
    $ ruby -v
    ```
  
    ### Rails�̃C���X�g�[��
    ```
    $ gem list -r --all rails
    $ rbenv exec gem install rails
    $ rbenv rehash
    $ rails -v
    ```
    �� Rails 4.2.4 ���\�������
  
  
    * �Q�l�T�C�g�F
    http://qiita.com/icche/items/801ce357d41fd0137b79
    ������(15/09/25)�̈���ł́u2.2.3�v


  ## Redmine�̃C���X�g�[��
    ### Redmine�\�[�X�̃_�E�����[�h�Ɣz�u
    ```
    # su -
    # cd /usr/local/src
    # wget http://www.redmine.org/releases/redmine-3.1.1.tar.gz
    # tar zxvf redmine-3.1.1.tar.gz
    # mv redmine-3.1.1 /opt/redmine
    # chown -R redmine:redmine /opt/redmine
    ```

    ### MySQL�C���X�g�[��
    ```
    # yum -y install mysql-server mysql-devel
    # service mysqld start
    # chkconfig mysqld on
    ```

    ### ���DB�Ƃ���DB�ɐڑ����邽�߂̃��[�U�쐬
    ```
    # mysql
    mysql> create database redmine character set utf8;
    mysql> create user 'redmine'@'localhost' identified by 'my_password';
    mysql> grant all privileges on redmine.* to 'redmine'@'localhost';
    mysql> quit
    ```

    ### DB�ڑ��ݒ�t�@�C�����쐬
    ```
    $ su - redmine
    $ cd /opt/redmine
    $ cp config/database.example.yml. config/database.yml
    $ vim config/database.yml
    ��"production"���p�̃f�[�^�x�[�X�ݒ���C��
    -----
    production:
      adapter: mysql2
      database: redmine
      host: localhost
      username: redmine
      password: my_password
    -----
    ```

    ### Bundler�̃C���X�g�[��
    ```
    $ cd /opt/redmine
    $ gem install bundler
    $ bundle install --without development test postgresql sqlite
    ```

    ### �Z�b�V�����X�g�A�閧���𐶐�
    ```
    $ rake generate_secret_token
    ```

    ### �f�[�^�x�[�X��Ƀe�[�u�����쐬
    ```
    $ RAILS_ENV=production rake db:migrate
    $ RAILS_ENV=production rake redmine:load_default_data
    ```

    ### �p�[�~�b�V�����̐ݒ�
    ```
    $ mkdir tmp public/plugin_assets 
    $ sudo chown -R redmine:redmine files log tmp public/plugin_assets
    $ sudo chmod -R 755 files log tmp public/plugin_assets
    ```

    ### WEBrick�ɂ��web�T�[�o���N��
    ```
    $ ruby bin/rails server webrick -e production -b 192.168.33.10
    ```
    �������̓T�[�o��IP

    ### �u���E�U����Redmine�Ƀ��O�C��
    - URL : http://192.168.33.10:3000
    - login : admin
    - password : admin

    ### �����T�C�g�̎菇���Q�l�ɉ��L�ݒ�����{����
    - ���O�̐ݒ�
    - SMTP�T�[�o�̐ݒ�
    - �o�b�N�A�b�v


  ## �Q�l�T�C�g:
  - ����
  http://redmine.jp/guide/RedmineInstall/

  - CentOS6��Redmine�𓱓������Ƃ��̎菇����
  http://www.aetherworks.org/article/112066375.html


  ## (�ȍ~�͍쐬���菇)

  ## Apache�C���X�g�[��
    ### Apache�C���X�g�[��
    ```
    yum -y install httpd mod_ssl
    ```

    ### httpd.conf�ɉ��L�ǉ�
    ```
    vim /etc/conf/httpd.conf
    �������ɒǉ�
    LoadModule passenger_module /root/.rbenv/versions/2.2.3/lib/ruby/gems/2.2.0/gems/passenger-5.0.20/buildout/apache2/mod_passenger.so
    <IfModule mod_passenger.c>
      PassengerRoot /root/.rbenv/versions/2.2.3/lib/ruby/gems/2.2.0/gems/passenger-5.0.20
      PassengerDefaultRuby /root/.rbenv/versions/2.2.3/bin/ruby
    </IfModule>
    ```


  ## ���������G���[
    ### �G���[�P
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

    ### �G���[2
    ```
    [root@vagrant-centos65 redmine]# ruby script/rails server webrick -e production
    script/rails no longer exists, please use bin/rails instead.
    
    ruby bin/rails server webrick -e production
    ```


    ### �G���[3
    ```
    passenger-install-apache2-module
    �`�����`
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
    �`�����`
    
    yum install libcurl-devel
    yum install httpd-devel
    yum install apr-devel
    yum install apr-util-devel
    ```


    ## �G���[4
    ```
    c++: internal compiler error: Killed (program cc1plus)
    ```
    �ˉ��z���̃�������2GB�ɃX�P�[���A�b�v




# �ŐVRuby+Rails���C���X�g�[��

Redmine�̍ŐV�łł́ARuby1.9�ȍ~�̃o�[�W�������K�v�ɂȂ邽��
�o�[�W�����A�b�v���܂��B

## (1)�R���p�C���ɕK�v�ȃc�[���Q���C���X�g�[��
```
# yum groupinstall -y 'Development Tools'
# yum install -y vim wget
# yum install -y readline-devel libyaml-devel
# yum install -y gdbm-devel tcl-devel openssl-devel db4-devel
```

## (2) rbenv�̃C���X�g�[��
```
# git clone git://github.com/sstephenson/rbenv.git ~/.rbenv
# mkdir -p ~/.rbenv/plugins
# git clone https://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build
# cd ~/.rbenv/plugins/ruby-build
# ./install.sh
```

rbenv�̃p�X��ʂ�
```
# echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bash_profile
# echo 'eval "$(rbenv init -)"' >> ~/.bash_profile
# source ~/.bash_profile
```
rbenv�̃o�[�W�����`�F�b�N
```
# rbenv
```

## (3) Ruby�ŐV�ł̃C���X�g�[��
```
# rbenv install -l
# rbenv install 2.2.3
# rbenv rehash
# rbenv global 2.2.3
# ruby -v
```

## (4) Rails�̃C���X�g�[��
```
# gem list -r --all rails
# rbenv exec gem install rails
# rbenv rehash
# rails -v
```
�� Rails 4.2.4 ���\�������


* �Q�l�T�C�g�F
http://qiita.com/icche/items/801ce357d41fd0137b79
������(15/09/25)�̈���ł́u2.2.3�v


## Apache�C���X�g�[��
```
yum -y install httpd mod_ssl
```

## MySQL�C���X�g�[��
```
# yum -y install mysql-server mysql-devel
# service mysqld start
# chkconfig mysqld on
```

## DB�Z�b�g�A�b�v
```
mysql> CREATE DATABASE redmine_db;
mysql> GRANT ALL PRIVILEGES ON redmine_db.* TO 'redmine'@'localhost' IDENTIFIED BY 'hogefuga';
mysql> exit
```

## (9)httpd.conf�ɉ��L�ǉ�
```
vim /etc/conf/httpd.conf
�������ɒǉ�
LoadModule passenger_module /root/.rbenv/versions/2.2.3/lib/ruby/gems/2.2.0/gems/passenger-5.0.20/buildout/apache2/mod_passenger.so
<IfModule mod_passenger.c>
  PassengerRoot /root/.rbenv/versions/2.2.3/lib/ruby/gems/2.2.0/gems/passenger-5.0.20
  PassengerDefaultRuby /root/.rbenv/versions/2.2.3/bin/ruby
</IfModule>
```

# Redmine�C���X�g�[��
# Redmine�ŐV
3.1.1 (2015-09-20):
redmine-3.1.1.tar.gz (md5: 24fd22a238c0236542a5c0ef730fc2bf)

�Q�lURL: http://www.redmine.org/projects/redmine/wiki/Download

```
tar zxvf redmine-3.1.1.tar.gz
```


# �G���[
## �G���[�P
```
[redmine@vagrant-centos65 redmine]$ bundle install --without development test postgresql sqlite

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

## �G���[2
```
[root@vagrant-centos65 redmine]# ruby script/rails server webrick -e production
script/rails no longer exists, please use bin/rails instead.

ruby bin/rails server webrick -e production
```


## �G���[3
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



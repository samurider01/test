# CentOS6 + GitLab環境構築
~~~
cd C:\vagrant_work
mkdir centos6_git_redmine
vagrant box add centos65 http://www.lyricalsoftware.com/downloads/centos65.box
vagrant init centos65
vagrant up
~~~

root/vagrantでSSH接続
~~~
yum -y install curl openssh-server postfix cronie
service postfix start
chkconfig postfix on
lokkit -s http -s ssh

curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh | sudo bash
yum -y install gitlab-ce

gitlab-ctl reconfigure
~~~

⇒実行前に仮想環境のメモリサイズを1GB以上に変更すべき



## リンク集
開発環境のGitlab
http://192.168.33.10/users/sign_in

Gitlab/Redmine連携
http://d.hatena.ne.jp/toritori0318/20140620/1403287430

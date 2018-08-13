---
title: Redmine 和项目管理
date: 2018-08-13 16:23:32
tags: 
- redmine
- 项目管理
---
[Redmine](https://www.redmine.org) 是基于 Ruby 开发的项目管理工具，代码开源免费，功能强大，适合公司内部使用。

## 安装教程
我主要是参考以下两篇文档，在 Mac 下安装的 Redmine，安装布署过程中碰到了些问题，记录在下面。

[https://www.redmine.org/projects/redmine/wiki/RedmineInstall](https://www.redmine.org/projects/redmine/wiki/RedmineInstall)
[https://www.cnblogs.com/edward2013/p/5533303.html](https://www.cnblogs.com/edward2013/p/5533303.html)

## 安装过程中碰到的些问题
### 安装 RVM
Mac 自带 Ruby，不需要安装 RVM 及 Ruby
``` bash
# \curl -sSL https://get.rvm.io | bash -s stable
# source /etc/profile.d/rvm.sh
```

### 安装 Ruby
下面以安装 Ruby 2.2 版本为例：
``` bash
# rvm install 2.2
```

### 列出 RVM 管理下的 Ruby 版本
``` bash
# rvm list known
```

### 查看 Ruby 版本
Redmine 的版本对 Ruby 版本有兼容要求，注意对照着上面 redmine 的 wiki 文档查看。
``` bash
# ruby -v
```

### Mac 下安装 installer
Mac 自带 Ruby，为了不影响系统安装库，需要设置用户库
``` bash
# export GEM_HOME=$HOME/.gem_home
# export PATH=$PATH:$HOME/.gem_home/bin
# gem install bundler
# bundle install --without development test rmagick
```

### 生成 Redmine 数据库
``` bash
# RAILS_ENV=production bundle exec rake db:migrate
# RAILS_ENV=production REDMINE_LANG=zh bundle exec rake redmine:load_default_data
```
如果生成数据库时遇到 `Error: Specified key was too long; max key length is 767 bytes` 问题，参考下面的解决方法。

### MySQL innodb 索引过长的问题
MySQL 必须设置
``` mysql
set global innodb_large_prefix=1;
set global innodb_file_format=BARRACUDA;
set global innodb_file_per_table=1;
```

需要修改 redmine 目录下 db/migrate 里的 create_table 语句，将创建表的语句加入 `options: ' ROW_FORMAT=DYNAMIC '`，解决索引长度问题。

### 目录权限
``` bash
# sudo chmod -R 777 files log tmp public/plugin_assets
```

### 运行
``` bash
# cd redmine
# rails server -b0.0.0.0 -p6853 webrick -e production -d
```

### 默认用户名密码
admin/admin

### 邮件发送帐号的配置
需要使用相应的 smtp 帐号，使用 sendmail 不能正常发送。SSL 邮件配置如下：

``` yml
  default:
    email_delivery:
      delivery_method: :smtp
      smtp_settings:
        ssl: true
        enable_starttls_auto: true
        address: "smtp.exmail.qq.com"
        port: 465
        domain: "smtp.exmail.qq.com"
        authentication: :login
        user_name: "user@domain"
        password: "password"
```

### 邮件通知
邮件发送人必须是 user@domain，和上面的配置一致。



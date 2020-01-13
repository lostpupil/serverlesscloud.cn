---
title: LARAVEL + SERVERLESS FRAMEWORK 快速创建CMS内容管理系统
description: 通过 Serverless Laravel 组件快速构建一个 Serverless CMS内容管理系统
date: 2020-01-13
thumbnail: https://img.serverlesscloud.cn/20191226/1577353083364-laravel.png
categories:
  - best-practice
authors:
  - Tabor
authorslink:
  - https://canmeng.net
---

今天，为大家带来一篇 Laravel  + Serverless Framework 的综合实战，里面信息量有点多，大家仔细看哦～

首先，我来介绍下主要的本地环境吧：

- Git： 不多说，只要会敲代码就应该知道
- Nodejs：由于 Serverless Framework主编程语言是nodejs，所以本地也请大家搞一个吧
- NPM：NPM是随同NodeJS一起安装的包管理工具，能解决NodeJS代码部署上的很多问题，我们的sls也是通过这玩意下载的
- PHP：不多说，最好的语言
- Composer：PHP 世界的包管理工具 Composer ，类似npm
- Laravel：Laravel是一套简洁、优雅的PHP Web开发框架(PHP Web Framework)

好像有点多，咱们一步步往下看。

## 1. 安装 Serverless

```
$ npm install -g serverless
```

我们最重要，而且最简单的一步就完成了。

![安装 Serverless](https://img.serverlesscloud.cn/2020113/1578915292994-89i51tt2m4%5B1%5D.png)

## 2. 配置Composer

有两种方式，我们在这里使用第一种为大家示范

第一种下载phar文件：

```
$ wget https://getcomposer.org/download/1.9.1/composer.phar
```

第二种直接安装：

```
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('sha384', 'composer-setup.php') === 'baf1608c33254d00611ac1705c1d9958c817a1a33bce370c0595974b342601bd80b92a3f46067da89e3b06bff421f182') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.phpphp -r "unlink('composer-setup.php');"
```

为了方便，我这里直接下载了 composer.phar 文件，具体用法如下：

```
$ php composer.phar
```

![安装 composer](https://img.serverlesscloud.cn/2020113/1578915318015-pa2uz2y1n4%5B1%5D.png)

## 3. 创建 coastercms

```
$php composer.phar create-project web-feet/coastercms
```

首先我们使用安装好的composer来创建我们的项目，如果直接在环境变量安装 composer 请自行将php composer.phar替换。然后，我们需要修改 Laravel 项目

由于云函数在执行时，只有 /tmp 可读写的，所以我们需要将 laravel 框架运行时的 storage 目录写到该目录下，为此需要修改 bootstrap/app.php 文件，在 $app = new Illuminate\Foundation\Application 后添加：

```
$app->useStoragePath($_ENV['APP_STORAGE'] ?? $app->storagePath());
```

完成样式：

```
/*
|--------------------------------------------------------------------------
| Create The Application
|--------------------------------------------------------------------------
|
| The first thing we will do is create a new Laravel application instance
| which serves as the "glue" for all the components of Laravel, and is
| the IoC container for the system binding all of the various parts.
|
*/
$app = new Illuminate\Foundation\Application(
realpath(__DIR__.'/../')
);
$app->useStoragePath($_ENV['APP_STORAGE'] ?? $app->storagePath());
/*
/*
|--------------------------------------------------------------------------
| Create The Application
|--------------------------------------------------------------------------
|
| The first thing we will do is create a new Laravel application instance
| which serves as the "glue" for all the components of Laravel, and is
| the IoC container for the system binding all of the various parts.
|
*/
$app = new Illuminate\Foundation\Application(
realpath(__DIR__.'/../')
);
$app->useStoragePath($_ENV['APP_STORAGE'] ?? $app->storagePath());
/*
```

```
# 视图文件编译路径
VIEW_COMPILED_PATH=/tmp/storage/framework/views

# 由于是无服务函数，所以没法存储 session 在硬盘上，如果不需要 sessions，可以使用 array
# 如果需要你可以将 session 存储到 cookie 或者数据库中
SESSION_DRIVER=array

# 建议将错误日志输出到控制台，方便云端去查看
LOG_CHANNEL=stderr

# 应用的 storage 目录必须为 /tmp
APP_STORAGE=/tmp

# 视图文件编译路径
VIEW_COMPILED_PATH=/tmp/storage/framework/views
 
# 由于是无服务函数，所以没法存储 session 在硬盘上，如果不需要 sessions，可以使用 array
# 如果需要你可以将 session 存储到 cookie 或者数据库中
SESSION_DRIVER=array
 
# 建议将错误日志输出到控制台，方便云端去查看
LOG_CHANNEL=stderr
 
# 应用的 storage 目录必须为 /tmp
APP_STORAGE=/tmp
```

## 4. 配置mysql数据库

这里我们建议使用腾讯云的CDB云数据库：

![数据库](https://img.serverlesscloud.cn/2020113/1578915337328-lu3mep4x02%5B1%5D.png)

```
DB_CONNECTION="mysql"
DB_HOST="gz-cdb-qla00XXX.sql.tencentcdb.com"
DB_PORT=639XX
DB_DATABASE="coaster_db"
DB_USERNAME="coaster_usr"
DB_PASSWORD="secret"
DB_PREFIX=
```

然后将.env.example重命名为.env。至此，我们的Laravel配置即可大功告成。

## 5. 部署 Serverless

接下来，我们在项目根目录，创建 serverless.yml 文件，在其中进行如下配置

```
$ touch serverless.yml
```

```
# serverless.yml

MyComponent:
component: "@serverless/tencent-laravel"
inputs:
region: ap-guangzhou 
functionName: laravel-function
code: ./
functionConf:
timeout: 10
memorySize: 128
environment:
variables:
TEST: vale
vpcConfig:
subnetId: ''
vpcId: ''
apigatewayConf:
protocol: https
environment: release
```

完成后，我们在当前目录下运行：

```
serverless --debug
```

引导安装后，便可进入管理后台：

![完成效果](https://img.serverlesscloud.cn/2020113/1578915365153-kru2vmr2j2%5B1%5D.png)

参考：
[Serverless Framework - 产品官网](https://cloud.tencent.com/product/sf) 
[Serverless Framework - GitHub](https://github.com/serverless/serverless/blob/master/README_CN.md) 

欢迎访问：[Serverless 中文技术社区](https://serverlesscloud.cn/)，您可以在 [最佳实践](https://serverlesscloud.cn/best-practice) 里体验更多关于 Serverless 应用的开发！
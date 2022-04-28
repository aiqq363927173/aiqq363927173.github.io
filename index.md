## devkeep的笔记

## composer
```
composer 手动加载语法：
"autoload":{
    "psr-4": {
          "Workerman\\":"vendor/workerman/workerman",
    	    "GatewayWorker\\": "vendor/workerman/gateway-worker/src"
    	    "命名空间\\": "目录地址"
    }
}
composer dump-autoload

composer下载地址和腾讯云镜像：
https://getcomposer.org/download/
composer config -g repos.packagist composer https://mirrors.cloud.tencent.com/composer/

composer常用包：
常用库大全：https://github.com/JingwenTian/awesome-php
常用函数：composer require devkeep/tools
微信支付：composer require zoujingli/wechat-developer
数 据 库：composer require catfan/medoo
数 据 库：composer require topthink/think-orm
邮件发送：composer require phpmailer/phpmailer
二 维 码：composer require endroid/qr-code
数据采集：composer require jaeger/querylist
文件添加ZIP格式并下载：composer require maennchen/zipstream-php
文字转拼音：composer require overtrue/pinyin

Thinkphp扩展插件包大全：
https://sites.thinkphp.cn/1556332

打开windows自启动目录：
start shell:startup
```

## 版本回退
```
1、未提交
未提交有以下两种情况：

1）已经在工作区修改了文件，但还未执行 git add 提交到暂存区。

2）已经执行了 git add 提交到暂存作，但还未执行 git commit 提交本地仓库。

这时候回退：

git reset --hard
这样等于清空了暂存区和工作区，本地仓库回退到了最新的提交状态。

2、已提交未推送
这种情况是指已经执行了 git add 提交到暂存区操作，又执行了 git commit 提交本地仓库，但还未 git push 推送到远程仓库。

2.1 上个版本回退：

git reset --hard HEAD^
2.2 多个版本回退：

git reset --hard HEAD~N
N：代表数字，要回退的次数。

2.3 指定版本回退：

git reset --hard <commit_id>
2.4 直接回退到远程最新版本：

git reset --hard origin/master
3、已推送
这种情况是指已经执行了 git add 提交到暂存区操作，又执行了 git commit 提交本地仓库，还执行 git push 推送到远程仓库。

参考上面的 2.1~2.4 的方法，先强制回退到本地仓库到上 N 个版本，再进行强制推送到远程仓库。

回退到上个版本示例：

git reset --hard HEAD^
git push -f
```

## Git钩子
```
sudo vim post-receive

#!/bin/sh
cd /www/wwwroot
unset GIT_DIR
eval ssh-agent
ssh-add /home/git/.ssh/id_rsa
git pull origin master
```

## svn钩子
```
sudo vim hooks/post-commit

#!/bin/sh
export LANG=en_US.UTF-8
svn update --username hanghang --password 123456 /data/project --no-auth-cache
```

## git pull 提示：no tty present and no askpass program specified
```
sudo vi /etc/sudoers 

在sudoers文件中加一行（www为所属用户，免密登录）：
www ALL=(ALL:ALL) NOPASSWD: ALL
```

## nginx配置
```
server {
        listen          443;
        server_name     www.skeep.cc;
        root            /data/web/repository/www/public;

        ssl     on;
        ssl_certificate         /etc/nginx/ssl/www/1_www.skeep.cc_bundle.crt;
        ssl_certificate_key     /etc/nginx/ssl/www/2_www.skeep.cc.key;
        ssl_session_timeout     5m;
        ssl_protocols           TLSv1 TLSv1.1 TLSv1.2;
        ssl_ciphers             ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
        ssl_prefer_server_ciphers on;

        index           	index.html index.htm index.php;
        location / {
                    #try_files $uri $uri/ /index.php/$uri;
                    if (!-e $request_filename){
                    rewrite  ^(.*)$  /index.php?s=$1  last;
                    }
        }

        location ~ \.php(.*)$ {
                fastcgi_pass  127.0.0.1:9000;
                    fastcgi_split_path_info  ^(.+\.php)(.*)$;
                    #fastcgi_pass   unix:/dev/shm/fpm-cgi.sock;
                    fastcgi_index  index.php;
                    fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
                    fastcgi_param  PATH_INFO $fastcgi_path_info;
                    include        fastcgi_params;
        }
}
```

## 公私钥
```
1.生成公私钥 ssh-keygen -t rsa（windows: C:\Users\Administrator\.ssh）
2.添加私钥到git（linux）： 
ssh-add ~/.ssh/id_rsa
ssh-agent bash

sudo vim /etc/sudoers （增加nginx或者fpm可以免密执行登录）
添加： www-data  ALL=(ALL:ALL) NOPASSWD: ALL
```

## git web钩子调用
```
<?php


    // --------------- pull.sh -----------------------
    // #!/bin/sh
    // cd /data/yunxiaoyi
    // eval `ssh-agent`
    // ssh-add /home/ubuntu/.ssh/id_rsa
    // git pull origin master
    // --------------- pull.sh -----------------------
    

    if($_SERVER['REQUEST_METHOD'] == 'POST')
    {
        exec("sudo /data/pull.sh 2>&1", $out, $return);
        print_r(implode("\n", $out));
        exit();
    }

?>

<!doctype html>
<html>
    <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
    <title>代码同步</title>
        <style>
            body
            {
                margin: 0;
                padding: 0;
            }   

            .syc
            {
                width: 100px;
                height: 32px;
                line-height: 32px;
            }

            .c
            {
                margin: 20px 0 0 30px;
            }
        </style>
    </head>

    <body>
        <button class="syc c">代码同步</button>
        <div class="box c"></div>

        <script src="https://libs.baidu.com/jquery/2.0.0/jquery.min.js"></script>
        <script>

            $('.syc').on('click', function(){

                $.ajax({
                    type: 'post',
                    url: './syc.php',
                    data: {},
                    beforeSend: function()
                    {
                        $('.box').html('正在同步，请稍后...');
                    },
                    success: function (data) 
                    {
                        console.log(data);
                        $('.box').html(data.replace(/\n/g,"<br/>"));
                    },
                    error: function(error)
                    {
                        console.log(error);
                    }
                });
            })

        </script>
    </body>
</html>

```




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
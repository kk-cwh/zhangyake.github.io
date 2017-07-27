title: PHP调试环境搭建(xdebug+PHPstorm +Chrome插件 Xdbug helper)
date: 2017-01-08 11:35:50
tags: PHP
original: false
comments: true
categories: 后端
thumbnail: http://7xqeyw.com1.z0.glb.clouddn.com/281539080765117436.jpg
photos:
 - http://7xqeyw.com1.z0.glb.clouddn.com/201611-ipad-605420_1920.jpg
---
1. [xdebug网站](https://xdebug.org/download.php)下载对应版本的扩展插件
由于windows7(64位系统)上的php版本是7.0.6，此处下载的xdebug也是64位7.0的版本如图：
![](http://7xqeyw.com1.z0.glb.clouddn.com/xdebug_dll_down.png)
2. 将下载的**php_xdebug-2.5.0-7.0-vc14-x86_64.dll**文件拷贝到php安装目录的ext文件夹中（我电脑是中D:\phpEnv\php7.0.6\ext）
3. 打开php.ini文件，在末尾添加下面代码片段:

```
zend_extension = "D:\phpEnv\php7.0.6\ext\php_xdebug-2.5.0-7.0-vc14-x86_64.dll"
xdebug.remote_enable = On
xdebug.remote_handler = dbgp
xdebug.remote_host = localhost
xdebug.remote_port = 9000
xdebug.idekey = PHPSTROM
```
<!-- more -->
4. 打开PHPstorm 这里用的版本是phpstorm2016.2.2 ，进入File--->Settings--->Languages & Frameworks--->PHP--->Servers 点击+号 添加 name： localhost
Host:  localhost Port: 80 Debugger: xdebug 之后点击Apply确认
![](./phpstorm_php_server.png)
5. 进入PHP下的Debug 将xdebug的 debug port:设置为9000 和 xdebug.remote_port = 9000一致 其他默认 确定 ok即可
![](./phpstorm_php_server.png)
6. 打开chrome浏览器 下载xdebug helper插件(可能要翻墙)，点击选项配置为phpstorm。如图
![](./chrome_xdebug_helper_install.png)
![](./chrome_xdebug_helper_conf1.png)
![](./chrome_xdebug_helper_conf2.png)
7. 设置插件xdebug helper 状态打开如图
![](./chrome_xdbughelper_able.png)
8. 在phpstorme 打开下图中的按钮，设置断点，启动服务器即可进入调试查看：
![](./php_debug.png)
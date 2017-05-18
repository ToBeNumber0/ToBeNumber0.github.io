
---
layout:     post
title:      "windows下配置npm"
subtitle:   "绿色出行"
date:       2017-05-18 16:49:00
author:     "Wyq"

tags:
   学习 
   npm
---
1. 下载node.js的windows版  
点击[这里](https://nodejs.org/en/download/)下载，按照默认配置安装。
2. 设置环境变量   
把node.exe所在目录加入到PATH环境变量中。
配置成功后可以在CMD中通过node --version 看到node.js对应的版本号

        C:\Users\fn>node --version
        v0.5.10

3. 安装git  
在[这里](https://git-for-windows.github.io/)下载Git windows版本，目前最新版是Git-2.13.0-64-bit.exe，按照提示一步步安装即可。安装完成后把git安装bin目录加入PATH环境变量

 CMD中运行 git --version 确认安装是否成功

    C:\Users\Administrator>git --version
    git version 2.13.0.windows.1
4. 安装 npm  
在确保node.exe和git都在PATH环境变量中后执行以下命令:

        git config --system http.sslcainfo /bin/curl-ca-bundle.crt
        
        git clone --recursive git://github.com/isaacs/npm.git
        
        cd npm
        
        node cli.js install npm -gf
        
5. 安装成功。测试一下。

        D:\develop\nodejs\hello>npm install -d
        npm info it worked if it ends with ok
        npm info using npm@1.0.103
        npm info using node@v0.5.10
        npm info preinstall application-name@0.0.1
        npm info addNamed [ 'jade', '>= 0.0.1' ]
        npm info addNamed [ 'express', '2.5.0' ]
        
        
        
推荐：

* [使用git拉取github上的项目](http://www.cnblogs.com/real-me/p/6528426.html)
* 


# spy-debugger-use
[spy-debugger github介绍链接](https://github.com/wuchangming/spy-debugger)

## 一站式远程页面调试工具spy-debugger使用方法简介

##### 日期：2018.1.23

本文介绍了本软件在不侵入代码的情况下解决了前端开发进入后期调试阶段时，因手机型号不同或浏览器兼容性差异而造成的样式不一致。避免了出现开发人员反复根据测试总汇文档检查代码，甚至重构部分代码的现象。不但节约了时间人力成本，同时还可以让开发人员在代码构
建的同时实时性的解决因兼容性问题导致的Bug。

- 目录
    1. Spy-Debugger简介
        1. 安装过程
        2. Spy-Debugger的特征
        2. weinre的特性及工作原理
        4. AnyProxy的特性及工作原理
    2.  Safari浏览器m9预上线上版本测试
        
##Spy-Debugger简介

Spy-Debugger是一站式页面调试、抓包工具。远程调试任何手机浏览器页面，任何手机移动
端webview（如：微信，HybirdApp等）。支持HTTP/HTTPS，无需USB连接设备。

####安装过程
这里要用到全局变量的一键安装，因为Spy-debugger是不侵入源代码的
 
    windows下
    
        npm install spy-debugger -g
        
    Mac下
   
        sudo npm install spy-debugger -g
####手机配置
        
第一步：手机和PC保持在同一网络下（比如同时连到一个Wi-Fi下）

第二步：命令行输入spy-debugger，按命令行提示用浏览器打开相应地址。

第三步：设置手机的HTTP代理，代理IP地址设置为PC的IP地址，端口为spy-debugger的启动端口(默认端口：9888)。

    Android设置代理步骤：设置 - WLAN - 长按选中网络 - 修改网络 - 高级 - 代理设置 - 手动

    iOS设置代理步骤：设置 - 无线局域网 - 选中网络 - HTTP代理手动

第四步：安装证书。注：手机必须先设置完代理后再通过(非微信)手机浏览器访问http://spydebugger.com/cert 安装证书
####自定义选项

**端口(默认端口：9888)**

    spy-debugger -p 8888

**设置外部代理（默认使用AnyProxy）**

    spy-debugger -e http://127.0.0.1:8888
spy-debugger内置AnyProxy提供抓包功能，但是也可通过设置外部代理和其它抓包代理工具一起使用，如：Charles、Fiddler。

**设置页面内容为可编辑模式**
该功能使页面内容修改更加直观方便。 (默认： false)

    spy-debugger -w true

内部实现原理：在需要调试的页面内注入代码：document.body.contentEditable=true。暂不支持使用了iscroll框架的页面。

**是否允许weinre监控iframe加载的页面(默认： false)**

    spy-debugger -i true

**是否只拦截浏览器发起的https请求(默认： true)**

    spy-debugger -b false

**有些浏览器发出的connect请求没有正确的携带userAgent，这个判断有时候会出错，如UC浏览器。这个时候需要设置为false。大多数情况建议启用默认配置：true，由于目前大量App应用自身（非WebView）发出的请求会使用到SSL pinning技术，自定义的证书将不能通过app的证书校验。
是否允许HTTP缓存(默认： false)**

    spy-debugger -c true

    
### Spy-Debugger的特征
1. 页面调试＋抓包
2. 操作简单，无需USB连接设备
3. 支持HTTPS
4.  **spy-debugger内部集成了weinre、node-mitmproxy、AnyProxy**
5. 自动忽略原生App发起的https请求，只拦截webview发起的https请求。对使用了SSL pinning技术的原生App不造成任何影响。
6. 可以配合其它代理工具一起使用(默认使用AnyProxy) (设置外部代理)

### Weinre的特性及工作原理

Weinre(WebInspector Remote)是一款基于Web Inspector(Webkit)的远程调试工具，借助于网络，可以在PC上直接调试运行在移动设备上的远程页面，中文意思是远程Web检查器，有了Weinre，在PC上可以即时修改目标网页的HTML/CSS/Javascript，调试过程可实时显示移动设备上页面的预览效果，并同步显示设备页面的错误和警告信息，可以查看网络资源的信息。

![](img/principle1.jpg)

![](img/principle2.jpg)

上述三层结构示意图的含义：
Debug客户端（client）：本地的WebInspector，远程调试客户端。
Debug服务端（agent）：本地的HTTPServer，为Debug目标页面与Debug客户端建立通信。
Debug目标页面（target）：被调试的页面，页面已嵌入weinre的远程js。

客户端、目标页面与Debug服务端之间使用XMLHttpRequest (XHR)进行HTTP通信，通常的使用情形是将Debug客户端与服务端搭建在桌面开发环境，Debug目标页面放在移动设备。

由于Weinre的debug客户端是基于Web Inspector开发，而Web Inspector只兼容WebKit核心的浏览器，所以只能在Chrome/Safari浏览器打开Weinre客户端进行调试。

### AnyProxy的特性及工作原理
1. 基于Node.js，开放二次开发能力，允许自定义请求处理逻辑
2. 支持Https的解析
3. 提供GUI界面，用以观察请求
![](img/anyproxy.gif)

> 小坑坑：微信PC版有设置代理的地方，但是设置了以后，抓取到的全部是mmslt的post协议，无法得到希望的Https的网页链接，这时需要设置系统的全局代理，即internet选项里，链接设置网络代理为anyproxy才行。

##使用实例
本次实例采用了唯医骨科预上线网站在iphone6S IOS 11.2.2系统下及Android 4.4.4系统下的调试。

####启动命令：spy-debugger
![](img/final.png)

在此我们可以清晰的看到我们现在处于唯医骨科医院预上线阶段，通过改边Spy-Debugger上的css元素和控制台操作，可以很直接的更改线上版本的样式。
与此同时我们还可以通过AnyProxy请求抓包。**不过要注意了，在本次抓包的请求中抓取的是全部的网络包，包括浏览器之外的网络活动，所以在抓包的时候要尽量关闭后台多余的网络程序。（红圈所在的区域就是后台无关刷新的网络抓包）**
![](img/final2.png)



##引用
AnyProxy开发文档:[http://anyproxy.io/cn/](http://anyproxy.io/cn/)

移动端Web开发调试之Weinre调试教程：[http://blog.csdn.net/freshlover/article/details/42640253](http://blog.csdn.net/freshlover/article/details/42640253)

weinre文档：[http://people.apache.org/~pmuellr/weinre/docs/latest/](http://people.apache.org/~pmuellr/weinre/docs/latest/)

Spy debugger开发者文档： [https://github.com/wuchangming/spy-debugger](https://github.com/wuchangming/spy-debugger)

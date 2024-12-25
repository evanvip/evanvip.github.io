---
title: 搭建自己的Docker 镜像源，解决群晖7.2 Container Manager（Docker）注册表不能用_网络存储_什么值得买
date: '2024-12-21 17:08:34'
updated: '2024-12-25 17:00:00'
tags:
  - docker
  - nas
  - 技术
permalink: >-
  /post/build-your-own-docker-mirror-source-to-solve-the-registry-of-synology-72-container-manager-docker-z1hv2go.html
comments: true
toc: true
---



---

* 搭建自己的Docker 镜像源，解决群晖7.2 Container Manager（Docker）注册表不能用_网络存储_什么值得买
* [https://post.smzdm.com/p/am3xx0w4/](https://post.smzdm.com/p/am3xx0w4/)
* 由于某些原因，群晖的Container Manager中的注册表突然出现了打不开，无法正常加载的情况，提示：无法执行此操作，可能是因为网络连接不稳定
* 2024-12-21 17:08:34

---

## 搭建自己的Docker 镜像源，解决群晖7.2 Container Manager（Docker）注册表不能用

2024-09-22 08:09:1518点赞146收藏48评论

由于某些原因，[群晖](https://pinpai.smzdm.com/2315/)的Container Manager中的注册表突然出现了打不开，无法正常加载的情况，提示：无法执行此操作，可能是因为网络连接不稳定或系统正忙。请稍后再试。![搭建自己的Docker 镜像源，解决群晖7.2 Container Manager（Docker）注册表不能用](https://raw.githubusercontent.com/evanvip/evanvip.github.io/master/images/66ef139b9a01d3588.png_e1080-20241221170834-ef5tr84.jpg)[https://post.smzdm.com/p/am3xx0w4/pic_2/](https://post.smzdm.com/p/am3xx0w4/pic_2/)事情发生大约在2024年6月，因此，如果出现这个问题再去网上找资料，很多老方法都是告诉你替换国内云厂商或高校网站的docker镜像源，但是由于这些国内的docker镜像加速源几乎全部关闭了，或者仅限内部使用，因此这种方法已经没用了。

那如果还要正常使用Container Manager(Docker)还有什么办法呢？方法有几种：

### 方法一、让群里通过代理直接访问docker官方镜像

家里有软路由/旁路由，懂科学，把这下面几个docker的官方地址加入代理列表，让群晖通过代理直接访问docker的官方镜像源进行镜像拉取，懂得都懂，今天重点讲第二个方法。

> [docker.com](http://docker.com/ "docker.com")
>
> [hub.docker.com](http://hub.docker.com/ "hub.docker.com")
>
> [registry-1.docker.io](http://registry-1.docker.io/ "registry-1.docker.io")
>
> [index.docker.io](http://index.docker.io/ "index.docker.io")
>
> [registry.hub.docker.com](http://registry.hub.docker.com/ "registry.hub.docker.com")

### 方法二、基于 Cloudflare Workers 部署Docker 镜像代理工具

### 第1步：注册个自己的域名（现在阿里云或[腾讯关注](https://pinpai.smzdm.com/19011/)![]()[品牌]()粉丝：

* 商品百科
* 好价
* 社区文章

云便宜的很），此步骤省略。

### 第2步：将域名的托管到cloudflare

1、注册个cloudflare账号（如果有的话直接登录），访问CloudFlare面板（[https://dash.cloudflare.com/），点击右上角添加站点，在下面红框中输入你注册的域名，点击继续](https://dash.cloudflare.com/%EF%BC%89%EF%BC%8C%E7%82%B9%E5%87%BB%E5%8F%B3%E4%B8%8A%E8%A7%92%E6%B7%BB%E5%8A%A0%E7%AB%99%E7%82%B9%EF%BC%8C%E5%9C%A8%E4%B8%8B%E9%9D%A2%E7%BA%A2%E6%A1%86%E4%B8%AD%E8%BE%93%E5%85%A5%E4%BD%A0%E6%B3%A8%E5%86%8C%E7%9A%84%E5%9F%9F%E5%90%8D%EF%BC%8C%E7%82%B9%E5%87%BB%E7%BB%A7%E7%BB%AD "https://dash.cloudflare.com/），点击右上角添加站点，在下面红框中输入你注册的域名，点击继续")

​![搭建自己的Docker 镜像源，解决群晖7.2 Container Manager（Docker）注册表不能用](https://raw.githubusercontent.com/evanvip/evanvip.github.io/master/images/66ef139bd4db83588.png_e1080-20241221170834-1j474g1.jpg)[https://post.smzdm.com/p/am3xx0w4/pic_3/](https://post.smzdm.com/p/am3xx0w4/pic_3/)2、选free免费版套餐，继续

​![搭建自己的Docker 镜像源，解决群晖7.2 Container Manager（Docker）注册表不能用](https://raw.githubusercontent.com/evanvip/evanvip.github.io/master/images/66ef139b577ba3588.png_e1080-20241221170834-7oip8r5.jpg)[https://post.smzdm.com/p/am3xx0w4/pic_4/](https://post.smzdm.com/p/am3xx0w4/pic_4/)3、得到cloudflare的DNS[服务器](https://www.smzdm.com/fenlei/fuwuqi/)地址

​![搭建自己的Docker 镜像源，解决群晖7.2 Container Manager（Docker）注册表不能用](https://raw.githubusercontent.com/evanvip/evanvip.github.io/master/images/66ef139bf35853588.png_e1080-20241221170834-p0tr6fj.jpg)[https://post.smzdm.com/p/am3xx0w4/pic_5/](https://post.smzdm.com/p/am3xx0w4/pic_5/)4、去到云厂商域名管理后台进行替换，以阿里云为例，进入域名控制台-域名列表，点击操作里的“管理”

​![搭建自己的Docker 镜像源，解决群晖7.2 Container Manager（Docker）注册表不能用](https://raw.githubusercontent.com/evanvip/evanvip.github.io/master/images/66ef139b4bfc93588.png_e1080-20241221170834-em9sp93.jpg)[https://post.smzdm.com/p/am3xx0w4/pic_6/](https://post.smzdm.com/p/am3xx0w4/pic_6/)5、选择DNS管理-DNS修改，点击修改DNS服务器，修改为上面cloudflare给的2个名称服务器地址（下图已经是修改以后的），页面上写需要24-48小时生效，其实不用，等个十几分钟就行了。

​![搭建自己的Docker 镜像源，解决群晖7.2 Container Manager（Docker）注册表不能用](https://raw.githubusercontent.com/evanvip/evanvip.github.io/master/images/66ef139c6ddf0809.png_e1080-20241221170834-j3z4hv4.jpg)[https://post.smzdm.com/p/am3xx0w4/pic_7/](https://post.smzdm.com/p/am3xx0w4/pic_7/)6、十几分钟以后，刷新cloudflare的主页，看你的域名状态是不是由“待处理的名称服务器更新”变成了“活动”，这时候你的域名就托管到了cloudflare。

​![搭建自己的Docker 镜像源，解决群晖7.2 Container Manager（Docker）注册表不能用](https://raw.githubusercontent.com/evanvip/evanvip.github.io/master/images/66ef139ca9d00809.png_e1080-20241221170834-29lu8x5.jpg)[https://post.smzdm.com/p/am3xx0w4/pic_8/](https://post.smzdm.com/p/am3xx0w4/pic_8/)

### 第3步：在cloudflare中新建一个worker，私有化部署docker镜像源

1、左侧选择Workers和Pages-概述，点击右侧创建按钮。

​![搭建自己的Docker 镜像源，解决群晖7.2 Container Manager（Docker）注册表不能用](https://raw.githubusercontent.com/evanvip/evanvip.github.io/master/images/66ef139c9390c809.png_e1080-20241221170834-zp0mtma.jpg)[https://post.smzdm.com/p/am3xx0w4/pic_9/](https://post.smzdm.com/p/am3xx0w4/pic_9/)2、点击创建Worker

​![搭建自己的Docker 镜像源，解决群晖7.2 Container Manager（Docker）注册表不能用](https://raw.githubusercontent.com/evanvip/evanvip.github.io/master/images/66ef139c44bb2809.png_e1080-20241221170834-xisz3vx.jpg)[https://post.smzdm.com/p/am3xx0w4/pic_10/](https://post.smzdm.com/p/am3xx0w4/pic_10/)3、取一个名字，点击部署

​![搭建自己的Docker 镜像源，解决群晖7.2 Container Manager（Docker）注册表不能用](https://raw.githubusercontent.com/evanvip/evanvip.github.io/master/images/66ef139c839f4809.png_e1080-20241221170834-55w3pl3.jpg)[https://post.smzdm.com/p/am3xx0w4/pic_11/](https://post.smzdm.com/p/am3xx0w4/pic_11/)4、点击右上角编辑代码

​![搭建自己的Docker 镜像源，解决群晖7.2 Container Manager（Docker）注册表不能用](https://raw.githubusercontent.com/evanvip/evanvip.github.io/master/images/66ef139c5f135809.png_e1080-20241221170834-xhaci4s.jpg)[https://post.smzdm.com/p/am3xx0w4/pic_12/](https://post.smzdm.com/p/am3xx0w4/pic_12/)5、删除worker.js文件中的全部默认代码，替换为下面链接中的代码，点击ctrl+s保存，点击右上角部署

代码内容：[CF-Workers-docker.io/\_worker.js](https://github.com/cmliu/CF-Workers-docker.io/blob/main/_worker.js "CF-Workers-docker.io/_worker.js")

​![搭建自己的Docker 镜像源，解决群晖7.2 Container Manager（Docker）注册表不能用](https://raw.githubusercontent.com/evanvip/evanvip.github.io/master/images/66ef139c21a7b809.png_e1080-20241221170834-uuacvcu.jpg)[https://post.smzdm.com/p/am3xx0w4/pic_13/](https://post.smzdm.com/p/am3xx0w4/pic_13/)6、点击设置-添加路由-添加，选择自定义域

​![搭建自己的Docker 镜像源，解决群晖7.2 Container Manager（Docker）注册表不能用](https://raw.githubusercontent.com/evanvip/evanvip.github.io/master/images/66ef139c36258809.png_e1080-20241221170834-p6v7auy.jpg)[https://post.smzdm.com/p/am3xx0w4/pic_14/](https://post.smzdm.com/p/am3xx0w4/pic_14/)7、在域中输入一个你属于你域名的二级域名，[比如你托管在cloudflare的域名是abc.com]( "比如你托管在cloudflare的域名是abc.com")，你想添加的二级域名是dockerhub，则在输入框里输入：[dockerhub.abc.com](http://dockerhub.abc.com/ "dockerhub.abc.com")，点击右下角“添加域”按钮。

​![搭建自己的Docker 镜像源，解决群晖7.2 Container Manager（Docker）注册表不能用](https://raw.githubusercontent.com/evanvip/evanvip.github.io/master/images/66ef139cb6ff9809.png_e1080-20241221170834-mbg9jv4.jpg)[https://post.smzdm.com/p/am3xx0w4/pic_15/](https://post.smzdm.com/p/am3xx0w4/pic_15/)8、添加完以后在设置-域和路由中可以看到你添加的自定义域和网址，需要等几分钟进行解析生效

​![搭建自己的Docker 镜像源，解决群晖7.2 Container Manager（Docker）注册表不能用](https://raw.githubusercontent.com/evanvip/evanvip.github.io/master/images/66ef139cdc290809.png_e1080-20241221170834-kkibomi.jpg)[https://post.smzdm.com/p/am3xx0w4/pic_16/](https://post.smzdm.com/p/am3xx0w4/pic_16/)9、过几分钟以后，你通过https://加上你的自定义域的网址，即可成功访问你自己的docker镜像源网站。

​![搭建自己的Docker 镜像源，解决群晖7.2 Container Manager（Docker）注册表不能用](https://raw.githubusercontent.com/evanvip/evanvip.github.io/master/images/66ef139cdbe7a809.png_e1080-20241221170834-bcqs5dx.jpg)[https://post.smzdm.com/p/am3xx0w4/pic_17/](https://post.smzdm.com/p/am3xx0w4/pic_17/)

### 第4步：在群中添加自己的镜像源

1、打开Container Manager，点击注册表，点击设置，选择新增

​![搭建自己的Docker 镜像源，解决群晖7.2 Container Manager（Docker）注册表不能用](https://raw.githubusercontent.com/evanvip/evanvip.github.io/master/images/66ef139c9f80a809.png_e1080-20241221170834-k67rn5y.jpg)[https://post.smzdm.com/p/am3xx0w4/pic_18/](https://post.smzdm.com/p/am3xx0w4/pic_18/)2、注册表名称随便写，注册表URL填写https://加上你的自定义域的网址，点击应用

​![搭建自己的Docker 镜像源，解决群晖7.2 Container Manager（Docker）注册表不能用](https://raw.githubusercontent.com/evanvip/evanvip.github.io/master/images/66ef139c9ca4f809.png_e1080-20241221170834-lqkgozu.jpg)[https://post.smzdm.com/p/am3xx0w4/pic_19/](https://post.smzdm.com/p/am3xx0w4/pic_19/)3、选中自己新增的镜像，点击使用，这时候注册表

​![搭建自己的Docker 镜像源，解决群晖7.2 Container Manager（Docker）注册表不能用](https://raw.githubusercontent.com/evanvip/evanvip.github.io/master/images/66ef139c3902f809.png_e1080-20241221170834-k2bewc6.jpg)[https://post.smzdm.com/p/am3xx0w4/pic_20/](https://post.smzdm.com/p/am3xx0w4/pic_20/)4、这时候注册表就能正常访问和下载镜像了，不过有个问题，搜索的镜像往往会出现在靠后甚至第二第三页

​![搭建自己的Docker 镜像源，解决群晖7.2 Container Manager（Docker）注册表不能用](https://raw.githubusercontent.com/evanvip/evanvip.github.io/master/images/66ef139c2dcf5809.png_e1080-20241221170834-oeh3mup.jpg)[https://post.smzdm.com/p/am3xx0w4/pic_21/](https://post.smzdm.com/p/am3xx0w4/pic_21/)5、如果总是找不到自己要的镜像，也可以通过命令行的方式去下载，需要通过ssh登录群晖的后台，然后用命令的方式进行下载。

比如：你的Workers项目自定义域名为：[docker.abc.com](http://docker.abc.com/ "docker.abc.com")，在拉取镜像的时候，在官方镜像路径前面加你的自定义域名即可，如：docker pull [docker.abc.com/stilleshan/frpc:latest](http://docker.abc.com/stilleshan/frpc:latest "docker.abc.com/stilleshan/frpc:latest")

​![搭建自己的Docker 镜像源，解决群晖7.2 Container Manager（Docker）注册表不能用](https://raw.githubusercontent.com/evanvip/evanvip.github.io/master/images/66ef139dbb5076279.png_e1080-20241221170834-mrgy5p1.jpg)[https://post.smzdm.com/p/am3xx0w4/pic_22/](https://post.smzdm.com/p/am3xx0w4/pic_22/)至此，群晖7.2无法使用Docker/Container Manager的问题就算是解决了，码字不易，如有帮助请帮忙点赞、收藏、分享，如果有其他问题，欢迎在留言区讨论。

项目代码来源：

感谢作者cmliu的github项目：[CF-Workers-docker.io](http://cf-workers-docker.io/ "CF-Workers-docker.io")

[https://github.com/cmliu/CF-Workers-docker.io](https://github.com/cmliu/CF-Workers-docker.io "https://github.com/cmliu/CF-Workers-docker.io")

作者声明本文无利益相关，欢迎值友理性交流，和谐讨论～

​![](https://raw.githubusercontent.com/evanvip/evanvip.github.io/master/images/network-asset-the-end-20241221170835-ft2i8gu.png)​

‍

---
layout: post
title: windows10挂载webdav
date: '2024-11-29 13:50:01'
permalink: /post/windows10-mount-webdav-z29d7y6.html
published: true
---



---

* windows10挂载webdav_win10 webdav-CSDN博客
* [https://blog.csdn.net/qq_38894585/article/details/128818512](https://blog.csdn.net/qq_38894585/article/details/128818512)
* 文章浏览阅读3.6w次，点赞27次，收藏67次。windows下挂载本地基于http的webdav服务，会出现网络错误和文件夹无效的错误。本文将介绍使windows支持http协议的webdav服务挂载的方法。_win10 webdav
* 2024-11-29 13:50:01

---

### 一、简介

当前市面上大部分的网盘，可以挂载到 “​**Alist**​” 中。Alist  
 又支持 webdav 协议。这就意味着通过 Alist 的 webdav 服务，我们可以直接将网盘挂载到本地，类似于本地磁盘一样读写网盘中文件。

但是在本地挂载的过程中，无论是添加网络位置，还是映射网络驱动器，都会出现文件路径不对、网络错误无法访问等错误，如下所示：

​![](http://127.0.0.1:49185/assets/network-asset-c52d03d5300232034b0a8d2bb2a99096-20241129135003-99m6n1y.png)​

​![](http://127.0.0.1:49185/assets/network-asset-ae9b9009b8be3b4d39b7fa08b5fcb4d6-20241129135003-tmnaj1q.png)​

本文将简单介绍如何在 Windows  
 环境下挂载本地 webdav。

### 二、问题原因

导致上述问题的原因其实非常简单。​ **windows 默认的 WebClient 服务仅支持 https 协议** ​，而本地搭建的 webdav  
 服务和链接都是基于 http 协议的，因此才造成了挂载失败的情况。

对于部分高手来说，将 webdav 服务转换为 https 协议必然是更安全，更好的选择。但是对于部分仅内网挂载访问，安全性需求较低的朋友来说，升级 https 的代价可能有些高昂，因此使 windows 自带的 [WebClient](https://so.csdn.net/so/search?q=WebClient&spm=1001.2101.3001.7020) 支持 http，可能是更快捷方便的选择。

### 三、设置 WebClient，允许 http 链接挂载

步骤 1：  
 按下 “​**windows 徽标键**​” + “​**R**​”，打开运行窗口，输入`regedit`​，点击确定后，打开注册表编辑器窗口。

步骤 2：  
 将路径定位到以下路径：`计算机\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WebClient\Parameters`​。双击右侧界面中的 `BasicAuthLevel`​ 条目，将数值数据修改为 “2”，点击确定后关闭注册表编辑器。

​![](http://127.0.0.1:49185/assets/network-asset-2d3c834279ebf08134d830cb46149194-20241129135003-8193gg5.png)​

步骤 3：  
 按下 “​**windows 徽标键**​” + “​**R**​”，打开运行窗口，输入`services.msc`​，点击确定后，打开 “服务” 界面。找到 “​**WebClient**​”  
 服务，右键点击打开选项菜单，选择重新启动，稍等几秒，待完成后，关闭 “服务” 界面。

​![](http://127.0.0.1:49185/assets/network-asset-2a1848149a5921c840649175e1ef917d-20241129135003-sl2wosw.png)​

完成上述三个步骤后，WebClient 服务已经允许使用 http 协议进行挂载。

### 四、挂载测试

1. 映射网络驱动器  
    ​![](http://127.0.0.1:49185/assets/network-asset-faa7583e2fbfd2ece8e9daf5416ee5be-20241129135003-avbeghq.png)  
    ​![](http://127.0.0.1:49185/assets/network-asset-e32d7b23b16f13a5a0f7f6a7732e6d09-20241129135003-qfvh9vt.png)​
2. 添加网络位置  
    ​![](http://127.0.0.1:49185/assets/network-asset-fe648958f110901affdf9517f4fdf066-20241129135003-oprb9bp.png)  
    ​![](http://127.0.0.1:49185/assets/network-asset-fe7d0a78cba48376fe313ae6520499a9-20241129135003-hge756r.png)​

可以看到，在修改注册表后，映射网络驱动器和添加网络位置，都能正确访问 webdav 服务了。

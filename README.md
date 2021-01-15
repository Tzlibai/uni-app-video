# uni-app-Video

> 一个优秀的uni-app案例，旨在帮助大家更快的上手uni-app，共同进步！



## Tip

> 由于一些不可抗力，不得不下线该应用的在线播放及电影详情服务,但是程序内逻辑可以借鉴参考，小程序版本目前效果最佳，大家可以扫码体验

## Features

- 代码编写简洁，注释清晰，快速入门必备；
- 支持在线模糊搜索；
- 程序类目懒加载，支持在线播放预告片；
- 更好的App跨平台框架、更方便的H5开发框架，加载新页面速度更快；
- 一套代码，可发布到iOS、Android、H5、以及各种小程序（微信/支付宝/百度/头条/QQ/钉钉/淘宝）、快应用等多个平台。

## 项目从零开始搭建到开发解读：

- [手牵手，使用uni-app从零开发一款视频小程序 (系列上 准备工作篇)](https://juejin.im/post/6861595248104046600)
- [手牵手，使用uni-app从零开发一款视频小程序 (系列下 开发实战篇)](https://juejin.im/post/6861994621417979918)

## 扫码体验

![H5](https://gitee.com/tzlibai/tzImg/raw/master/2021-1-15/1610676167736-222.png)

## 使用手册

>  **本仓库为了帮助更多初学者或是爱好者，仅供学习交流，严禁用于商业用途，仓库中使用Api均来自于网络，如涉及侵犯个人或者团体利益，请与我取得联系，我将主动删除一切相关资料，谢谢**！

### 启动准备

 小程序账号及微信开发者工具： [https://mp.weixin.qq.com](https://mp.weixin.qq.com/)

 建议编辑器：[HBuilderX](https://www.dcloud.io/hbuilderx.html)

### 手摸手启动项目 ( 以小程序为例 )：

1.打开 **HBuilderX**导入项目：

![H5](https://gitee.com/tzlibai/tzImg/raw/master/2021-1-15/1610676513530-33png.png)

2.进入**manifest.json**文件中修改成自己的相关ID（如遇无法加载配置文件，重启编辑器即可）；

![H5](https://gitee.com/tzlibai/tzImg/raw/master/2021-1-15/1610676580789-1111.png)

接下来就可以正常使用啦~

#### 小程序启动可能会遇到问题：	

+ **预编译器错误：代码使用了scss/sass语言，但未安装相应的编译器插件，请前往插件市场安装该插件**

  答：**HbuildX**工具上方工具栏 -- 工具 -- 插件安装 -- 前往插件市场安装 --  搜索**[scss/sass编译](https://ext.dcloud.net.cn/plugin?id=2046)**安装后重新运行即可。

- **HBuilderX报错：微信开发者工具拒绝HBuilderX访问端口** || **[error] 工具的服务端口已关闭。**

  答：微信开发者工具 -- 设置 -- 安全设置，点击开启服务端口即可解决。

- **小程序报错：不在以下 request 合法域名列表中**

  答：这是因为在小程序中发起了wx.request请求,但是请求的域名没有在微信公众平台后台设置，管理员将需要使用的域名添加到小程序后台，（调试时可以点击微信开发者工具右上角 **详情 -- 本地设置 -- 勾选不校验合法域名 **,可暂时取消报错）。

- **小程序报错：Failed to load media [http://xxx.xx](http://xxx.xx/) server responded with a status of 403**

  答：这是小程序电影详情页面的预告片视频报错（不影响可忽略此错误），并不是加载视频错误，而是微信开发者工具中加载视频会提示这个错误，所以在调试带有视频的控件时，可以点击真机预览小程序。

- **H5页面显示一直在加载中**

  答：打开F12控制台，查看报错信息，理论上应该是提示跨域，如果是跨域问题，可以使用**使用HBuilderX内置浏览器来解决**，或者查看这里的[解决方案](https://juejin.cn/post/6844904063855755271).

------

#### 本程序用到的接口API如下 ( 添加至合法域名列表 )：

```
https://douban.uieee.com
https://api.douban.com
```

## 打包发布教程

 **点击此处查看 [打包发布教程](https://uniapp.dcloud.net.cn/quickstart?id=发布uni-app)**

## ★Star

如果你有好的意见或建议，欢迎给我们提 [issue](https://github.com/Tzlibai/uni-app-video/issues) ，为优化此项目贡献力量。

**开源不易，致敬不断奋斗在开源路上的人。如果本仓库对您有帮助，请在最上方点亮Star，谢谢您！**

## 免责声明

 本仓库仅供学习了解, 请于下载后 24 小时内删除, 不得用作任何商业用途, 文字、数据及图片均有所属版权, 如转载须注明来源，如涉及侵犯个人或者团体利益，请与我取得联系，我将主动删除一切相关资料，谢谢！

使用本程序必循遵守部署服务器所在地、所在国家和用户所在国家的法律法规, 程序作者不对使用者任何不当行为负责。

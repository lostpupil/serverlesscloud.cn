---
title: Serverless 中文社区有奖征稿
description: 欢迎将您的技术实践、开发经验分享更多人借鉴学习！
keywords: Serverless 全局变量组件,Serverless 征稿,Serverless Component
date: 2020-04-15
time: future
thumbnail: https://img.serverlesscloud.cn/2020327/1585306882223-9.jpg
categories:
  - meetup
authors:
  - Serverless 中文网
authorslink:
  - https://china.serverless.cn/
location: 
  - 线上投稿 | Serverless 中文社区
---

亲爱的小伙伴们，Serverless 在线课堂已经开展了三期直播，我们分享了 Serverless 的概念、技术架构、典型应用场景、开发实战以及工程化应用等技术干货。在这里，要感谢大家抽出时间来学习和使用 Serverless，在此期间，你们提的需求产品都已记录在小本本上了，研发同学也正在加急开发中……

为了鼓励大家保持持续学习 Serverless 技术的动力。我们特地发起了一个让开发者表达真实声音的活动，你可以将在 Serverless 在线课堂学习到的知识、碰到的问题以及基于 Serverless Framework 做过的实践进行整理，形成一篇文章或者一段文字。

如果您错过了直播，我们也邀请你分享你了解的 Serverless 知识。

我们将使用 Serverless 社区平台，给大家提供一个展示自己学习和掌握 Serverless 过程的机会，同时，也将为分享文章的同学提供 Serverless 社区专属纪念礼品包，包括腾讯云限量版 T恤 以及 Serverless 社区贴纸等。

## 投稿福利

1. 社区荣誉：我们将会对稿件内容进行评选，入选的高质量文章，除了实物奖励之外，我们还会：

- 在 Serverless 公众号和 Serverless 技术社区的首页，展示您的文章及作者介绍模块，让更多的开发者了解你；
- 有机会成为 Serverless Framework 社区的贡献者，参与社区的日常运营，并作为社区讲师参与 Serverless 线上/线下活动分享。

2. 精美礼品：凡是提交有效内容的同学，都可以获得我们提供的纪念礼品包一份。礼品包包含：

- 腾讯云云函数 100元无门槛代金券一张；
- 腾讯云专属定制限量版 T 恤一件；
- Serverless Framework 社区贴纸（独家）。

## 内容方向建议

- **我的 Serverless 学习之路。** 将 Serverless 在线课程直播学习到的内容，整理成笔记，并附上作品链接（四期在线直播的课堂作业链接任选其一即可）。

- **我的 Serverless 创意。** 如果在 Serverless 的使用上有好的创意，你也可以将基于 Serverless Framework 开发的其他作品如开发一个全栈应用等，整理好操作步骤，附上项目链接，发布在你的个人平台上。

- **我的第一个 component 开发。** 如果你已经完成了 component 的开发，你可以整理好开发过程，分享你的开发经验，帮助更多的开发者学习实践。

- **我的 Serverless 开发经验谈。** 如果你希望别人可以借鉴你的学习成果，你也可以将在使用 Serverless Framework 开发部署过程中，遇到的问题、排查思路、解决方案或产品建议整理成文。

- **不限。**其他与 Serverless 相关内容。例如你是如何知道 servereless 的？准备用 Serverless 做些什么项目？你接下来准备怎么学 Serverless？如果要你推荐 Serverless，你会推荐给谁？等等

**敲黑板：**您可以将您整理的经验和文字，通过您的个人云+社区专栏、知乎、博客、简书、头条发布，让更多的人跟我们一起学习和使用 Serverless 技术。同时，将相关的链接提交至收集表：http://u6uvxlniyxhd4qgj.mikecrm.com/noIVWnY。我们将为你送出专属于 Serverless 粉丝的纪念礼品包！

您也可以在文章评论区写下你对 Serverless 的理解、建议，或者即将撰写发布的内容主题、方向，为其他人提供思路和灵感，留言点赞前三名，我们额外赠送计算器笔记本一个。

征稿截止时间：4 月 15 日 24:00 截止提交。

## Serverless Framework 免费试用计划

Serverless Framework 免费试用名额已开放，我们诚邀您来试用和体验最便捷的 Serverless 开发和部署方式。包括服务中使用到云函数 SCF、API 网关、对象存储 COS 等产品，均在试用期内提供免费资源，并伴有专业的技术支持，帮助您的业务快速、便捷实现 Serverless ！

Serverless Framework 落地 Serverless 架构的全云端开发闭环体验，覆盖编码、运维、调试、部署等开发全生命周期。使用 Serverless Framework 即可在几秒钟内将业务部署至云端。

![具体免费详情可查阅：https://cloud.tencent.com/document/product/1154/38792](https://img.serverlesscloud.cn/2020312/1584006765599-IMG_0123.PNG)


## 立即使用 Serverless，只需三步

Serverless Framework 是构建和运维 Serverless 应用的框架，简单三步，即可通过 Serverless Framework 快速实现服务部署。

**1、创建本地应用**

- 通过 npm 安装 Serverless

```
$ npm install -g serverless
```

- 基于 tencent_nodejs 模板创建 hello_world

```
$ serverless create --template tencent-nodejs --path my-service
```

**2、安装相关依赖**

- 执行 npm install 安装相关依赖

```
$ cd my-service$ npm install
```

**3. 部署**

- 扫描微信二维码一键登录腾讯云账号，部署函数到云端

```
$ serverless deploy
```

- 触发云函数

```
$ serverless invoke -f hello_world
```

部署完成后，即可在命令行中看到部署情况，也可以在腾讯云控制台看到对应资源。

![](https://img.serverlesscloud.cn/2020312/1584006765436-IMG_0123.PNG)
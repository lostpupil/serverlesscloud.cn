---
title: 腾讯云 Serverless 云函数并发管理能力解读
description: 提供多层次的并发配额管理能力，用户可获得更强的函数并发管理控制的权限，无需等待即可快速调整业务并发
date: 2020-12-30
thumbnail: https://main.qcloudimg.com/raw/f1c0252cbf776c2639c323348cced6f1.jpg
categories:
  - best-practice
authors:
  - Alfred
tags:
  - Serverless
  - 并发管理
---

近期，腾讯云 Serverless 云函数发布了并发管理能力升级版，提供了 3 个维度的并发额度管理的功能。该功能究竟提供了哪些能力，有哪些使用场景？本文将为您全方位解读并发管理功能，并对多种使用场景提供配置建议。

## 背景介绍

原先，创建一个函数，默认具有300的并发数量上限。针对小的低频业务，300 的并发值足够使用。但是遇到业务量上涨、支撑大型运营活动等大并发的情况，开发者就需要通过提工单联系平台方，申请提升函数并发额度。这样可能导致：

1. 每遇到一次大并发，就需要联系一次平台方来提升配额，时效性弱。 
2. 申请等待周期时会导致上涨的业务部分有损。
3. 在评估量级时，也可能出现评估不足，导致需要再次申请，低效。

## 并发能力升级

相对于原有的函数默认固定的并发值，本次上线的并发管理能力，有以下方面的优化：

1. 将单一函数的并发调整放开给了出来，用户可以自行来控制并发数。
2. 并发额度由单一的函数维度，移到了账号维度。
3. 账号下默认具有一定的并发额度，由账号下的函数所共享的，不需要单独为其中一个大并发的函数申请额度。


因此，在当前的模式下，默认账号具有的 128000 MB的额度，可以供 128 MB配置内存函数启动运行1000个并发；在这种情况下，用户无需去寻找平台申请，就可以获得比调整前更高的一个并发额度，用于支撑上涨的业务。

![](https://main.qcloudimg.com/raw/4c936731e2c6f1588c683e982156571b.png)


## 并发的处理

由于账号级别的额度是在账号下的多个函数间共享的，在多个函数同时运行的情况下，因为流量突增、业务上涨导致并发增高的函数在占用完全部空闲额度后，可能会和平稳运行的函数之间产生冲突。

**这种情况下，有两种解决方案：**

一方面可以通过平台申请提升更高的账号额度，来满足上涨业务带来的并发上涨；

另外一方面，也可以通过将部分额度分配给具体函数，来保障具体函数的运行可靠性。

下面我将具体说一下第二种方式。举个例子，在同样的账号下，函数 A 提供 H5 页面用于秒杀的运营活动，函数 B 在进行后台的流式数据处理。在 B 函数启动了 300 并发进行业务处理时，运营活动会受限于 A 函数，最大仅能跑到 700 并发；而函数 A 的业务压力下，如果 B 函数也有业务量上涨，将无配额可用导致无法启动更多实例。

![](https://main.qcloudimg.com/raw/6a04aa0825e8df125f45262962327bba.png)


在上面的例子中，如果要保证 B 函数数据处理流程的可靠性，可以为这个函数设置到 350 的保留并发配额；此时，这个额度将从账号维度划给这个函数单独使用，而运营活动所使用的 A 函数将仅仅可以最大使用的 650 并发的额度。函数 B 在设置到 350 的保留并发额度后，在业务持续上升后，最大也仅仅可以使用到配置的这个额度，此时，就算是账号维度的额度仍然有剩余时，B 函数也无法去使用。

![](https://main.qcloudimg.com/raw/165d3ea454d4b8dca942a5b3e31abbfc.png)


通过保留额度的设置，一方面，我们可以对函数的运行进行保障，避免多个函数共享额度时，由于其他业务的函数占用导致本函数无法运行产生损失，另外一方面，保留配额也定义了函数的运行额度上限。

因此，针对函数的并发管理控制，可以基于业务来进行更精细的控制，有以下三点建议：

1. 针对开发测试阶段的函数，由于请求量小，无业务压力，并发也极少，可以不配置保留额度而仅仅使用账号维度的共享额度；
2. 针对稳定运行的函数，并发通常是确定的，浮动范围也不会很大，这个时候可以给这个函数设置稍微有一点余量的保留额度，来保障函数的额度不受共享的影响；
3. 针对运营活动、有可能性突增并发的函数，可以利用账号维度的高额度，来充分利用和支撑业务爆发。


![](https://main.qcloudimg.com/raw/78c8be70f964bc7e40fccf774f5c0261.png)


当前预置额度的设置，是设置在函数的版本上，也是从账号维度的并发配额或函数上的保留额度中扣减下来的。

通过设置预置额度可以预想启动所需量的并发实例，完成实例的初始化并等待事件的到来。针对函数的请求将不会有冷启动时间，直接就可以在已经完成准备、初始化完成的实例中得到运行。

在时延敏感的业务，例如前端的 SSR 页面响应；或者是初始化时间较长的业务，例如 AI 推理的模型加载过程；这些场景下，通过给函数设置上一定的预置可以保障业务更好的运行。

同时，预置的配额也不是实例并发的上限，在业务量上涨到超过已经预置的实例可以承载的时候，函数平台仍然会根据函数的保留配额或者是账号的配额，拉起更多的实例来支持业务运行。

![](https://main.qcloudimg.com/raw/38d19dd859b8c1ecfb98e8d0130c2c34.png)


## 并发使用场景设置建议

一个账号下有多个业务都在同时使用云函数进行支撑时，函数的并发配额就需要进行按需调度。根据不同的业务特性来进行合理合适的设置。

例如有波峰波谷的前端业务，有平稳运行波动不大的数据处理业务，有偶尔才运行一次的定时运维任务，也有并发不大但是计算量重、计算时间长的视频处理业务；

在这些业务中，根据重要性、是否接受一定损失来说，又有不同的区分：前端业务保障用户体验，要求加载速度快，但是可以有一定的错误容忍度；数据处理的要求高，不能接收延迟、波动或失败；定时运维任务偶尔运行，不用投入过多的关注，运行正常即可；而视频处理业务，可以接受按需调度，失败时能自动重试就行。

根据不同的业务特性、容错额度、业务波动情况、时延要求，我们就可以按照不同的情况来进行不同的设置。上述几项业务中，并发设置有以下几点建议：

- 对前端业务来说，要求加载速度快，但是有波峰波谷，这种情况我们就可以为函数配置一定量的预置额度，例如按最大使用量的60%来设置，但是同时不设置函数的保留额度，确保在高峰到来时能充分利用总配额；
- 针对数据处理业务，波动不大但是容错低，我们可以为函数配置一定量的保留额度，确保不会有其他业务使用的共享额度导致数据处理业务的问题；
- 运维任务定时运行，没有高要求，可以不做任何配置，使用账号维度的配额来处理就行；
- 针对视频处理业务，计算量大，而且可以按需排队处理任务，我们设定好一定的保留额度，让业务跑满并发额度，充分利用好且控制好计算资源。

![](https://main.qcloudimg.com/raw/7c4c8576c0c69949f795df472f163d86.png)

![](https://main.qcloudimg.com/raw/165d3ea454d4b8dca942a5b3e31abbfc.png)

![](https://main.qcloudimg.com/raw/38d19dd859b8c1ecfb98e8d0130c2c34.png)

## 保留配额另一种用法

保留配额还有另外一种用法——对业务的限制或关停。在有紧急事项发生，例如漏洞攻击、循环调用失控等情况出现时，为了避免有重大损失，可以通过设置保留配额，将额度控制在极小的值上来避免运行失控，甚至可以设置为 0 来关闭函数的运行。

![](https://main.qcloudimg.com/raw/224d3a2459d297800b41beea65bee6a4.png)

## 内存和并发额度的关系

当前额度按内存进行计算，配置内存大的函数，并发运行时占用的额度多，而配置内存小的函数在多并发运行时占用的额度小；

由于函数服务的资源用量计费项和函数的配置内存强相关，通过内存进行额度控制，一方面可以让我们尽量采用合适的内存来实现业务，另外一方面，针对整体的支出也可以进行有效的控制。

![](https://main.qcloudimg.com/raw/6a9ca742d216db39f8519a4b0c103905.png)


## 总结

通过提供多层次的并发配额管理能力，目前我们可以获得更强的函数并发管理控制的权限，无需再等待平台方的调整就可以自行根据业务需求快速调整。

目前并发管理功能已经上线，函数可以配置保留并发，同时预置并发功能也处于内测阶段，欢迎大家申请试用。针对并发管理的更多场景、用法，也欢迎大家留言探讨和反馈。

**试用申请链接：** https://cloud.tencent.com/apply/p/j1fl01i6f2i

---

欢迎访问：[Serverless 中文网](https://serverlesscloud.cn/)，您可以在 [最佳实践](https://serverlesscloud.cn/best-practice) 里体验更多关于 Serverless 应用的开发！
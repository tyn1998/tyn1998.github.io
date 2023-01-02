---
layout: post
title: ASoC2022：实现一个用于查看OpenSumi通信消息的Chrome插件
categories: [Event]
---

# 申请阶段

这是2022年6月26日的日记开头：

> 昨天晚上带着ipad上床，看了10分钟讲js的Proxy的视频后接着上GitHub看opensumi/core/packages/connection的代码，看到两点多，想到三点，有收获，有激动。

## 失之东隅

这人真的挺肝的，因为他不想再次错失机会。为什么是再次？Well，在5月21日那天，他被OpenSumi社区的蛋总邮件温柔地婉拒了，那是OpenSumi在OSPP2022中的项目，而那次落选也是意料之中。

5月太忙了，长了带状疱疹还传染给室友（第一次得叫水痘），室友因水痘发热于是在特殊时期一起都去了国交中心隔离。隔离可不是休息，数据中台课程3人小组每天从早到晚从0到1肝我们的课程项目——SPECHelper。课程项目的结果是很好的，得到了老师的肯定还拿了一个高分，但我为此几乎放弃了OSPP Summer2022的申请季。尽管如此，我肯定会参与到今年的OSPP中，因为Hypercrx作为社区方在今年有两个学生名额，作为导师之一，我要负责出题、学生的选择以及后续的指导和交流。

几乎放弃不意味着完全放弃，我还是匆匆浏览了今年上线的项目。OpenSumi是为数不多的前端项目，还是一个与IDE和编辑器有关的项目，我可能没考虑其他项目就提了申请，不过积极的互动没有跟上，因此落选也是毫不意外。但是蛋总在邮件中还给了另一个希望：“由于‘开源之夏’活动的限制，我们有部分题目还未开放，后续也会以‘阿里开源’相关的活动开放给学生。”

## 收之桑榆

蛋总说的“阿里开源”指的是阿里巴巴编程之夏（Alibaba Summer of Code），起初我还以为是阿里今年第一次办的活动，但后来了解到已经办了两三年了。ASoC没有自己的专属网站，只是作为阿里开源网站导航栏的一个链接。报名也是填问卷的形式，不像OSPP那样有一个平台系统。5月25日，相关Issue开放，我直接A了上去：

![image.png](https://cdn.nlark.com/yuque/0/2023/png/21625412/1672648128265-712ad81a-93bf-48d6-9b76-4780bba07f82.png#averageHue=%23fefefe&clientId=ueb49d6e1-f36d-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=379&id=ua4f5e5e9&margin=%5Bobject%20Object%5D&name=image.png&originHeight=758&originWidth=1500&originalType=binary&ratio=1&rotation=0&showTitle=false&size=400435&status=done&style=none&taskId=u1c29b32b-dcef-40f5-813c-81051b411d9&title=&width=750)

Chrome插件呀，多么契合我为数不多比较了解的领域！我想都没想就留言示意了。

而且这次我要有所行动，行动从起开发环境开始。根据日记的描述，光是起环境我就尝试并失败了四次。6月1日拿到了个人在opensumi/core中的第一个PR[#1157](https://github.com/opensumi/core/pull/1157)，这个PR修复了一个小bug，但它的合并足以使我激动！6月9日，项目导师Yantze在钉钉中加我好友，让我下周一前提交一个简单的申请，他下周一就把人确定下来。6月9日和6月10日在opensumi组织下的其他仓库又收获了两个合入的PR，因此在写申请书的时候有了底气。6月13日，导师Yantze说：“我和活动方说了，这个题目就选你了。恭喜你获得这个题目！”

6月25日收到邮件，竟然还有申请答辩这个环境，将和导师进行视频面基，神经又得绷紧了。为了在答辩那天给导师留下更好的印象，我得开始理解相关源码才行，于是就有了晚上抱着iPad看到3点的故事。答辩那天印象深刻，寝室三个人去河东食堂吃饭，我选择不加入另外两位的鸡公煲局，而是随便吃点，吃完就走，魂不守舍。6点就是答辩，对于这次答辩，日记写着“感觉挺好的~”。

# 开发阶段

偷个懒，直接把活动后的“项目经验分享”贴到这里。不过需要注意的是，下面都是事后的总结，在真正的开发过程中，有太多烦恼和焦虑，没有任何一个小目标是一蹴而就的。

## 项目介绍

**项目名称：**实现一个用于查看 OpenSumi 通信消息的 Chrome 插件

**项目描述：**目前 OpenSumi 的架构是前后端分离的，桌面端前端要和多个进程进行通信，或者 Web 端要和后端服务通信。目前通信的方式有 IPC、RPC 等，但通信的问题会比较难以直观的看到或者调试。希望提供一个可视化的显示通信数据的 Chrome 插件，方便的查看通信流量。

**项目要求：**为了在 Web 和 桌面端都可以通用，期望实现的最终产物是一个 Chrome Extension，通信数据展示在 Devtools 上，具有可视化显示每一条通信数据。

- 产出：Chrome DevTools 插件
- 通信列表已经每条信息的详情，过滤能力
- 通信信息可视化，支持当前延迟、流量（KB/s）
- 进阶（控制通信速度、通信调试辅助工具）

**项目难度：**难

**项目导师：**[@yantze](https://github.com/yantze) yutian.yz#alibaba-inc#com

**项目链接：**[https://github.com/opensumi/core/issues/1098](https://github.com/opensumi/core/issues/1098)

## 开发详情

本项目的产物是一个Chrome插件，该插件需要先捕获OpenSumi的通信消息，继而才能呈现它们。于是，本项目的开发就围绕“消息捕获”和“消息呈现”两大主题进行。

### 消息捕获

#### OpenSumi与DevTools之间的桥梁

OpenSumi前端和OpenSumi DevTools本身运行在两个独立的环境中，若我们要将从OpenSumi前端捕获到的消息传递给DevTools，必须设法在两者间建立联系。

如下图灰色虚线框和蓝色虚线框重叠处所示，凭借`chrome.devtools.inspectedWindow.eval()`这个Chrome插件API，我们可以在OpenSumi前端运行环境和OpenSumi DevTools运行环境之间架起一座桥梁，这座桥梁就是全局变量`window.__OPENSUMI_DEVTOOLS_GLOBAL_HOOK__`，以下简称`HOOK`。

![image.png](https://cdn.nlark.com/yuque/0/2022/png/21625412/1662212318884-139eddd3-b835-4438-baec-8a36cf995723.png#averageHue=%23f8f5f2&clientId=uaf926ce7-09cf-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=560&id=u0b4e7574&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1610&originWidth=1758&originalType=binary&ratio=1&rotation=0&showTitle=false&size=549959&status=done&style=none&taskId=uf7e7b10d-90f5-4794-b53d-eca275c4f14&title=&width=611)

`HOOK`由OpenSumi在初始化的时候注入到`window`中，并赋初值为`{}`；当Chrome DevTools被打开时，OpenSumi DevTools会首先判断当前页面是否存在该`HOOK`，以此来判断当前应用是否为基于OpenSumi开发的IDE产品，进而决定是否继续加载。若加载成功，则向`HOOK`注入一些函数和变量，经过[改造](https://github.com/opensumi/core/pull/1560)后的OpenSumi会调用`HOOK`中的捕获函数，并将捕获到的RPC和IPC消息放到`HOOK`中的数组变量中。

#### RPC和IPC消息的捕获

解决了消息在OpenSumi和DevTools间的传递问题后，我们来关注消息捕获本身。本项目要求捕获两类消息，参见上图，它们分别是OpenSumi前后端之间的RPC消息（仅以前端侧视角）以及Electron客户端特有的IPC消息。在实际操作中，两类消息的捕获思路不相同。简单来讲，前者在消息发生的地方插入捕获函数，后者事先对通信API进行了代理，将捕获函数集成在经过改造的通信API中。

为了**捕获RPC消息**，我们需要改造`ide-connection`模块，主要是该模块的`packages/connection/src/common/proxy.ts`文件。在RPC通信方式中，前后端通过调用双方提供的服务的方法来通信，而不需要显式地发送网络请求，这得益于RPC通信的实现方式。虽然开发者不需要显式地发送网络请求，但脏活累活总得有人干，在OpenSumi的RPC通信实现中，`proxy.ts`就是那位负重前行者。

`proxy.ts`会将调用的服务方法转换为通信请求，当通信请求到达对方时，又会被对方的`proxy.ts`转换为方法调用——这就是远程过程调用的核心。如下图中的diff内容所示，我们利用“服务方法转换为通信请求”这个时间点，调用`HOOK`中的RPC捕获函数，方能捕获到通信消息内容。

![image.png](https://cdn.nlark.com/yuque/0/2022/png/21625412/1662274347232-08db6c98-18ca-4370-9e9c-a6920009d51f.png#averageHue=%23eafdec&clientId=ue0cbd501-00db-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=561&id=uf7090921&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1122&originWidth=2246&originalType=binary&ratio=1&rotation=0&showTitle=false&size=734482&status=done&style=stroke&taskId=uc24bb673-3836-4247-b8ef-7b3a0e48b9d&title=&width=1123)

图中只出现了两个时间点，只要在`proxy.ts`所有这样的时间点中都插入RPC消息捕获逻辑，就能实现RPC消息的全覆盖捕获。

**捕获IPC消息**采用另一种思路。IPC指的是Electron的[IPC API](https://www.electronjs.org/docs/latest/tutorial/ipc)，不像`proxy.ts`集中了所有RPC消息的收发，IPC API可以出现在非常多的文件中，所以我们不能在所有使用IPC API的地方都插入一段捕获IPC消息的代码。转而，通过模仿[DevTron](https://github.com/electron-userland/devtron/blob/c2d06028bb75c5cc5567b272a866e6d6e75223df/lib/ipc-helpers.js#L43-L64)采用的方式，我们对IPC API本身进行代理，在其中插入`HOOK`中的IPC捕获函数，参见下图中的diff内容。如此一来，IPC消息捕获对开发者来说是透明的，因为他们不知道自己调用的IPC API是经过改造后的API——除非他们看到了相关代码。

![image.png](https://cdn.nlark.com/yuque/0/2022/png/21625412/1662282042628-f774488c-0151-4f71-873f-30f6f521f246.png#averageHue=%23e7fde9&clientId=ue0cbd501-00db-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=563&id=ud57e3580&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1126&originWidth=2242&originalType=binary&ratio=1&rotation=0&showTitle=false&size=249732&status=done&style=stroke&taskId=uea7ac501-26a1-43f3-b7cd-a555ea61b8b&title=&width=1121)

无论是RPC捕获函数还是IPC捕获函数，它们都会把捕获到的消息push到`HOOK`中对应的数组变量，供OpenSumi DevTools提取并呈现。

#### 通信延迟和流量的获取

对于通信延迟，利用已有的`[connection-rtt-service.ts](https://github.com/opensumi/core/blob/main/packages/addons/src/browser/connection-rtt-service.ts)`，我们可以获取客户端和服务端之间的RTT，并将该值不断写入到`HOOK`的`latency`变量中；对于通信流量，只要对一个时间单位内所有消息的Buffer Size求和并除以时间间隔，即可获取。

#### 控制捕获的开启和关闭

到目前为止，我们只涉及了从OpenSumi到DevTools的数据流，而没有提及从DevTools到OpenSumi的控制流。现有的控制方式有两种：其一是改变`HOOK`值，其二是基于全局的`Custom Event`。前者通过在OpenSumi中加入判断逻辑，可以根据`HOOK`的值做出不同的反应，RPC和IPC消息捕获的开关都使用这种方式；后者利用事件机制，由DevTools对OpenSumi进行单向通知，通信延迟获取的开关基于这种方式。理论上，前一种方式也可以重构为基于事件的方式，未来也许会进行统一。

### 消息呈现

Chrome DevTools自带的[Protocol monitor](https://developer.chrome.com/en/blog/new-in-devtools-92/#protocol-monitor)是最好的UI指导。OpenSumi DevTools借助[react-data-grid](https://github.com/adazzle/react-data-grid)和[react-json-view](https://github.com/mac-s-g/react-json-view)两大核心组件基本复现了Protocol monitor的主要功能和交互方式，还提供了一些额外的功能，如滚动更新、复合过滤、紧凑模式等。UI的开发过程中有非常多繁琐的细节，在此不宜展开，有兴趣的读者可以前往已归档仓库[tyn1998/opensumi-devtools](https://github.com/tyn1998/opensumi-devtools)浏览相关的Issue和PR。

### 成果展示

![2022-09-04 18.33.28.gif](https://cdn.nlark.com/yuque/0/2022/gif/21625412/1662287777597-58044a54-1200-46b9-91f4-8fe8318012b6.gif#averageHue=%231d2222&clientId=u5b5b7417-f813-4&crop=0&crop=0&crop=1&crop=1&from=drop&id=u84c19ee3&margin=%5Bobject%20Object%5D&name=2022-09-04%2018.33.28.gif&originHeight=882&originWidth=1250&originalType=binary&ratio=1&rotation=0&showTitle=true&size=7242971&status=done&style=none&taskId=ud20d1906-1d07-4b9a-9e3c-c5c70e5ec4e&title=Electron%E5%AE%A2%E6%88%B7%E7%AB%AF%E4%B8%AD%E4%BD%BF%E7%94%A8OpenSumi%20DevTools "Electron客户端中使用OpenSumi DevTools")

# 成功结项

电子证书、实体水晶证书、OpenSumi社区周边、大赛周边、奖金一个都不少！此外，Dan老师还将我拉入了OpenSumi的GitHub组织中，这是最棒的认证。

![ASoC2022结项证书.jpg](https://cdn.nlark.com/yuque/0/2023/jpeg/21625412/1672654671462-9da661ec-ea02-4030-8225-5742ca774623.jpeg#averageHue=%23f9fdf7&clientId=ueb49d6e1-f36d-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=888&id=u42b3c781&margin=%5Bobject%20Object%5D&name=ASoC2022%E7%BB%93%E9%A1%B9%E8%AF%81%E4%B9%A6.jpg&originHeight=1775&originWidth=2365&originalType=binary&ratio=1&rotation=0&showTitle=false&size=2175430&status=done&style=stroke&taskId=ubdc853c3-27ed-4f66-86d9-6f5d0fac27b&title=&width=1182.5)

![ASoC2022收官宣传视频60名结项.png](https://cdn.nlark.com/yuque/0/2023/png/21625412/1672655404740-884ed722-cb87-422c-b196-7c55fb4b5684.png#averageHue=%23df5816&clientId=ueb49d6e1-f36d-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=945&id=u5529a132&margin=%5Bobject%20Object%5D&name=ASoC2022%E6%94%B6%E5%AE%98%E5%AE%A3%E4%BC%A0%E8%A7%86%E9%A2%9160%E5%90%8D%E7%BB%93%E9%A1%B9.png&originHeight=1890&originWidth=3024&originalType=binary&ratio=1&rotation=0&showTitle=false&size=3984956&status=done&style=stroke&taskId=u8472f501-3bed-402b-9339-f5507c67e2e&title=&width=1512)

# 体验与收获

通过在OpenSumi社区三个月来的见闻和参与，无论大到项目的发版，还是小到CSS的样式细节，我从核心开发者身上学到了太多书上没有的知识与技巧。从6月份的试探，到7月份的摸索，再到8月份的发力，我在开源洋葱模型中慢慢从边缘向内靠拢。许许多多的困惑、思索、灵感、激动、兴奋都化成文字和代码，散落在2个仓库、40个Issue和31个PR中。

![image.png](https://cdn.nlark.com/yuque/0/2022/png/21625412/1662188439898-fcc91c4e-fed4-43dc-be62-37363e1dee22.png#averageHue=%23fcfcfc&clientId=uaf926ce7-09cf-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=823&id=u48957995&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1646&originWidth=2964&originalType=binary&ratio=1&rotation=0&showTitle=false&size=988153&status=done&style=stroke&taskId=u975931e1-0c0b-4092-beaf-ced2364abd5&title=&width=1482)

开源就是最好的老师！

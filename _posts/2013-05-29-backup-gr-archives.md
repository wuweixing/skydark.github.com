---
layout: post
title: "备份 Google Reader 保存下来的网站历史存档"
category: programming
excerpt: "RT，题目好长……"
tags: [python, programming]
---
{% include JB/setup %}

离 Google 宣布 Google Reader 要关闭的日子已经不远了。
在各种 Google Reader 替代品都逐渐稳定的现在，我已经过渡到[feedly](http://www.feedly.com)上了。
老实说，虽然现在 Reader 满天飞，能真正像 Google Reader 那样舒适的着实不多：

* 能订阅长城外的博客；
* 访问迅速；
* 稳定可靠；
* 界面简洁实用；
* 多终端支持；
* 保存网站历史存档；
* ……

其中第一个是起码的(长城喜欢技术博不是一天两天了)，而最后那个是最难能可贵的。
对于那些已经消失了的博客站点，它们的痕迹只能残留在 Google Reader 的 RSS 列表中了。
而随着 Google Reader 的关闭，这些痕迹恐怕也会消影无踪吧。
就算有些博客没有消失，很多新的 Reader 们也只能抓到最近的文章，一些老的文章只能自己去博客上查而不能在 Reader 里看到。
对于那些发帖间隔很长但是都很值得一看的博客来说是很麻烦的。

目前的替代品中，能像 Google Reader 这样保留站点历史存档的几乎没有。
Feedly 目前是基于 Google Reader 的后端所以还能看到。
他们自称有从 Google Reader 平滑迁移的计划(名字很不错，叫“诺曼底计划”)。
但是我不确定这个迁移是否包括所有历史存档——感觉如果包括历史存档的话，这个工程确实太大了。
而且考虑到像 Google 这样的公司也能作出这样的决定，还是把数据掌握在自己的手里比较放心一些。
于是还是自己随便写个爬虫抓来比较好吧。

昨晚查了些资料，今早起床后写完后就运行然后去上课了，回来发现跑完了`_(:з」∠)_`
代码在[github](https://github.com/skydark/grarchive)上，
有需要的话自己改吧，我已经够用了(不负责摊手)……

## 使用方法(虽然我觉得除了我自己没人会用)

1. 基于`python2`,需要如下扩展：`requests`和`lxml`;

2. 克隆仓库后，修改开始处的`USERNAME`和`PASSWORD`为自己的 Google 帐号；

3. 在[Google](https://www.google.com/takeout/#custom:reader)导出 Google Reader 的数据，解压其中的`subscriptions.xml`到仓库中；

4. 运行`grarchive.py`，然后睡一觉，如果没有出错，醒来时备份的xml会按分类和标题出现在`outdir`目录中。

## 感谢

* [libgreader](https://github.com/askedrelic/libgreader) 只是借用了下 Google 登录；

* [这篇文章](https://adityanag.com/journal/2012/02/20/using-the-google-reader-api-to-build-a-historical-archive-of-a-site/)中介绍了关于抓取 Google Reader 历史记录的方法，我的脚本里没有处理超过1000条帖子的情况(因为不属于我关心的需要备份的部分)，这里也有介绍。

* [Linuxtoy](http://linuxtoy.org/archives/inoreader.html) 见评论区`_(:з」∠)_`我承认我从那时起一直在头疼(生理意义上)，而且到现在还没有完全恢复(摔)……

## 已知问题

显然，就是因为我一次运行成功了就懒得做的东西，所以别指望我做……`_(:з」∠)_`

* 单线程，我备份下来的文件有400M+，别问我具体用了多少时间，我说了我去上课了……；

* 无错误恢复过程；

* 只支持单层分类，多层分类会被展开——显然因为我只有单层分类……；

* 按标题保存文件名，在遇到特殊标题会有麻烦——如果不是`unix`系用户，可能需要更改`grarchive.py`中的`normalize`函数，用于将标题转换为文件名；

* OAuth登录(虽然`libgreader`中是有的)；

* 每个博客只输出1000篇文章，如需要实现见感谢里的第二个链接；

* 输出修饰后的网页(而不是原始的xml——从备份角度我足够了所以懒得弄)；

* ……

总之，这篇日志的目的就是记录下自己解决这个问题的要点和注意……啥，真有人以为那github仓库里的代码值得拿来用？(金馆长扶额脸)

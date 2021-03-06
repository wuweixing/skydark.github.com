---
layout: post
title: "将终端嵌入桌面"
category: linux
excerpt: 
tags: [linux, shell]
---
{% include JB/setup %}

如题，效果如图:

![嵌入终端截图]({{BASE_PATH}}/images/misc/embedterm.jpg)

类似的文章其实很多，无非是:

1. 隐藏标题栏/菜单栏/滚动条/……;
2. 利用终端自己/窗口管理器/其他工具(Devil's Pie, transset-df, etc.)等将终端设置上最大化透明置底不在任务栏显示等等属性;
3. 加入自启动。

思路还是很清晰的。

不过这些文章几乎都没有提及一个问题——当使用“*切换桌面显示*”的时候，这个所谓嵌入桌面的终端也会被最小化。
而事实上，这是嵌入桌面的终端最有可能被使用的情景。

可能的解决方案有两个：

1. 将“隐藏桌面”的快捷键绑定到另一个脚本上，这个脚本在完成原本的隐藏桌面的任务后随即还原桌面上的终端。
  可以使用`wmctrl`完成这系列任务，难度不大。
  主要的问题是，原先“显示/隐藏桌面”的快捷键只要按两次就会恢复窗口原来的布局，而现在这个效果就无法保持了。

2. 使得终端真正“显示在桌面”。类似`conky`和`wbar`等软件的效果。需要提示窗口管理器这个窗口的type是desktop.
  但使用`xprop/wmctrl/devilspie`等完全没有效果。
  [这个帖子](https://bbs.archlinux.org/viewtopic.php?pid=865844)的7楼说明了原因：
  这个hint要在窗口实际映射前设定好，如果等终端程序已经就绪窗口已经完成了再去提示窗口管理器，窗口管理器是可以无视的——openbox就是这么做的。

也就是说，如果希望终端程序和`conky`一样真正嵌入桌面，只能通过修改终端的源代码实现了。
该帖子里给了修改`urxvt`的方法，我照葫芦画瓢也修改了`terminator`，选择这个终端的原因是它不是我平时用的终端，而且功能非常丰富。最终效果如文首图，还是不错的。

修改的时候就是将`terminatorlib/window.py`里`Window`类的`show`方法开头添加如下代码：
{% highlight python %}
if startup:
    self.present()
    self.window.property_change(
            gtk.gdk.atom_intern("_NET_WM_WINDOW_TYPE", False),
            gtk.gdk.atom_intern("ATOM", False), 32,
            gtk.gdk.PROP_MODE_REPLACE,
            [gtk.gdk.atom_intern("_NET_WM_WINDOW_TYPE_DESKTOP", False)]
            )
{% endhighlight python %}

当然要注意的是这样修改后就要求终端运行时就已经设定好了geometry，最大化那些hint也没有效果了。

---

最后得说的是，很多推荐嵌入桌面终端的文章推荐`tilda/guake/yaguake`等。但其实这几个终端最适合的使用方式是用快捷键随叫随到。
终端嵌入桌面更多是求一个气氛和极客味道(黑色壁纸更佳)，相比之下，快捷键唤出终端的实用意义才要更大一些。

---
layout: post
title: 试了下cython包装epdf，还有SDL
category: python
tags: [e17, python, ons]
---
{% include JB/setup %}

cython真是个好东西，用来包装c库确实省时省力……

epdf似乎说法是被E17项目遗弃改成eyesight了？
忘了在哪里似乎扫到了这么眼，看了下eyesight，
现阶段似乎是把pdf,dvi,ps的后端整合到一起了……
嘛，先不管了，完全照着python-evas的源码照葫芦画瓢地写了个货，
it works我就已经泪流满面了，
虽然只去包装了smart_pdf,document,page，而且包装完了仍然是C风格，
完全不pythonic啊……

至于SDL嵌入窗口(呃，显然，是打算ONS嵌入窗口，虽然其实完全没有实际意义啊嗷嗷！= =)，一开始以为比较容易:

    SDL_WINDOWID=the_xid_of_embbed_xwindow ./onscripter

然后发现input完全传不过去……

最后解决办法是改ONS的源码，原理如下:

{% highlight cpp %}
if (xid != 0) {
	SDL_SysWMinfo info;

	SDL_VERSION(&info.version); // this is important!
	SDL_GetWMInfo(&info);

	info.info.x11.lock_func();
	//XUnmapWindow(info.info.x11.display, info.info.x11.window);
	//XReparentWindow(info.info.x11.display, info.info.x11.window, xid, 0, 0);
	XReparentWindow(info.info.x11.display, info.info.x11.wmwindow, xid, 0, 0);
	info.info.x11.unlock_func();
}
{% endhighlight cpp %}

虽然一开始会突然显示一个ONS窗口……

ref:

- [http://sdl.beuc.net/sdl.wiki/FAQ_GUI](http://sdl.beuc.net/sdl.wiki/FAQ_GUI)

- [http://old.nabble.com/getting-X11-%28or-windows%29-events-when-SDL_WINDOWID-is-set-td17059020.html](http://old.nabble.com/getting-X11-%28or-windows%29-events-when-SDL_WINDOWID-is-set-td17059020.html)

- [http://www.hu.freepascal.org/lists/fpc-pascal/2007-November/015709.html](http://www.hu.freepascal.org/lists/fpc-pascal/2007-November/015709.html)

呃，P.S.:google的时候不小心看到了这个：
[http://www.rpgchina.net/read-htm-tid-25470.html](http://www.rpgchina.net/read-htm-tid-25470.html)

泪流满面地近距离围观john_he大……


---
layout: post
title: "Aqualang Notes"
category: programming
excerpt: Aqua lang Notes
tags: [programming, aqua]
---
{% include JB/setup %}

好吧，大概两个月没发东西了……从现状来看，我大概活着……
其实要是以前的话大概会没事发个现状总结给死党看，然则现在她在这儿没存在感……

最近发生的比较 RP 的事情是今天知道自己在[松本行弘访谈问题征集](http://www.ituring.com.cn/activity/details/15396)里中奖了!
大概是因为这辈子的 RP Point 全都积攒到了一些特殊事件上，从小到大从来没有中过什么奖，包括各种再来一瓶、再来一根、再来一袋、30% 送卤蛋什么的都是各种没中过……所以这次真心是这辈子第一次中奖2333
奖品是松本行弘签名的《松本行弘的程序世界》，虽然这书我早有一本读完了的XD

图等收到了晒上来XD

我提问的问题是这样的:

> 设计语言的时候往往需要在很多语言特性中进行取舍，比如多继承和 Mixin，比如基于类还是基于原型等等。对 Matz 先生来说，在设计 Ruby 的时候感到最难以取舍的、考察最深入的语言特性是什么？有没有哪个特性会让您觉得“啊，当年若是选择那么做而不是这么做，虽然不会是现在的 Ruby，但应该也不错”？
{:.well}

之所以会问这个问题，是因为自己的确正在想实现一个语言练练手。
在考察各种语言特性的时候(当然程序语言并不是特性的堆积)，总是觉得大多数时候这些特性没有绝对的对与错，而更多的是根据语言自身的特点和目的来进行的取舍和妥协。
显然我在很多这类的地方纠结了很久很久，所以想趁机请教下 Matz 大神，看他当年在这种事情上是怎么纠结的 :)
本来觉得这问题挺个人兴趣的，被选上的确出乎意料，可惜不能当场去听回答了XD

其实本来最近就想就自己对想实现的 Toy language 的各种考量写个 Note 的(记性奇烂无比，已发生过几个月前考虑过的问题不得不再考虑一次的窘境，所以干脆记下来的好)，既然挨上好事了，就先占个位子慢慢来……
当然读者必然假定是未来的自己了，如果在读这段文字的恰好是未来的我，麻烦从 金馆长脸/姚明脸/兔斯基以头抢砖 表情中选一个，现在的我推荐姚明脸……

最后一次更新是 2012/11/14.
显然最近这事的优先级不高，下次更新不排除隔周啥的2333

---

{% include sd/toc %}

展开所有项目
{:.btn .btn-primary #show_all}

---

## Meta Questions

### 为什么大多数问题的回答都是“因为我懒……”?

* 因为我现在懒……
* 事实是我急着睡觉，而大部分问题我嫌敲起来麻烦还没整理到这里……

### 为什么问题明显不足，有些问题明显标题都没起好?

因为我懒……

## 总体框架

### 为何选择 VM 而不是解释或编译?

因为我懒……

### 为何选择使用 Python 实现?

因为我懒……

## Syntax

### 为何选择接近 Python 的语法?

因为我懒……

### 为何选择 Layout 布局?

因为我懒……

## Function & Block

### 为何函数参数可以接收各种形式?

因为我懒……

## Exception

### 为何对 finally 中的流程控制选择了当前的做法?

关于 finally 代码块的含义，一般被认为是“保证某段代码执行结束时一定会调用的代码”。
其作用一般是清洁工作，保证资源在任何情况下都被正常释放等。
但当 finally 代码块中包含流程跳转时——特别地，可能抛出异常时——其含义变得不甚符合直觉了。

1. Python 在 finally 中可以使用 return 和 break, 也可以抛出异常。

    如果使用了 return, 进入 finally 前试图返回的值会被这里替换。

    如果进入 finally 前正在抛出异常，该异常抛出的行为会因为 finally 中的 return/break 而中断，也会被 finally 中抛出的异常所覆盖，这些通常不是想要的结果(Java 也有相同的问题)。

    特殊地，Python 不允许在 finally 块中使用 continue, 称为是 CPython 的实现问题。
    猜测大概是因为 CPython 中 continue 在字节码层面上被直接实现为 GOTO 的缘故(对应地，break 有单独的字节码，并且所需信息在 SETUP_LOOP 中已经存储)。

2. Golang 中倾向使用错误返回值，但也可以使用 panic 和 recover 实现非局部跳转。
    Golang 中的 defer 从某些意义上部分近似于 finally.

    Golang 中的 defer 可以直接修改返回值。
    defer 后跟着的是函数，因此不需要考虑 return/break/continue 等。

    Golang 总是保证所有 defer 执行完毕，就算是 defer 中产生 panic 也不例外——此时 golang 会选择中断这个 defer 去执行下一个 defer，并且将原先打算抛出的 panic 信息替换为此次 defer 发生 panic 的信息(尽管 golang 应该是记得之前 panic 的信息的，因为如果一直不 recover, 最后会打印所有 panic 的信息)。

3. Dlang 是比较特别的语言，它同时包括了 finally 和 defer(scope).

    Dlang 要求 finally 和 defer 中不能有 return/break/continue 等，也不允许使用 goto 再入，defer 中甚至要求不得 throw——尽管如此，它实际上不可能保证不在 defer 中抛出异常。

    Dlang 的 defer 中抛出异常和 golang 一样，要保证所有 defer 按 LIFO 的顺序执行完毕，但是 defer 中出现的异常会靠 Throwable.next 串起来，最后抛出的异常仍然是第一个发生的异常——尽管有忘记处理 .next 的风险。
    Finally 中抛出的异常也会这样串在后面。

    (P.S.: Dlang 比较奇特的是 1/0 运行时直接浮点数例外，1.0/0.0 运行时是 inf，都不是异常……)

## Misc

### 为何起名为 Aqua?

因为我懒……

　
{:.last}

---

<script type="text/javascript">
(function(){
  $('h3').addClass('js-toggle-next dropdown');
  $('h3').each(function(i){
    $(this).nextUntil('h2,h3,p.last').wrapAll('<div class="well">');
  });
  var is_show = false;
  $('#show_all').click(function(){
    if (is_show) {
      $('h3.js-toggle-next').next().hide();
      is_show = false;
      $('#show_all').text('展开所有项目');
    } else {
      $('.js-toggle-next').next().show();
      is_show = true;
      $('#show_all').text('闭合所有项目');
    }
  });
})();
</script>

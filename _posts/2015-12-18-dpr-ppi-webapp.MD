---
layout: post
title: 移动端H5针对不同高清屏适配的一些思考
---

***  


![ 移动端H5针对不同高清屏适配的一些思考 ](/blog/img/2015/2015-12-18-dpr-ppi-webapp.png)

### 说在前头

本文不细聊一些概念，主要说一下我是如何解决在写H5的移动页面时遇到的一些问题，以及我的思考和解决方案，比如如何在屏幕上画出1px（物理像素）的border,其实解决了这个方案也差不多就解决的如何适配不同ppi屏幕的问题。因为我是处女座就是想要这1个物理像素的border，当然你完全可以通过使用一下代码粗暴的解决这个问题。

{% highlight html %}
  <meta name="viewport" content="width=divice-width, initial-scale=1, maximum-scale=1, minimum-scale=1, user-scalable=no"/>
{% endhighlight %}

### ppi(像素密度)、dpr(设备像素缩放比)、逻辑像素、物理像素

ppi是设备屏幕上每英寸上的物理像素的个数，注意！这里说的是`物理像素`，就是你趴在屏幕上看到的一个个的小亮点。但是，对于一些高清屏，贴上去也无法看到像素点。  
这里插播一下`Retina屏`和`4K屏`，前者是苹果公司从iPhone4开始推出的概念，也就是`视网膜屏`，苹果公司说人眼在距离屏幕25cm左右，当屏幕的ppi达到300时 ，人眼就无法分辨屏幕上的像素点，这样的屏幕被称为`Retina屏`。下面的表格中可以看到iPhone系列产品的ppi。

通俗的说一些dpr，理解这个很关键。通常情况下，我们在写css的1px的时候认为就是屏幕上的1个物理像素，但是在dpr!=1的时候就不是一个物理像素了。如果dpr==4，那么我们操作的就是一个4*4个物理像素，就是把原来1个物理像素所占的面积内填充了更多的像素点。
呵呵，我知道这么讲太不专业，因为我真的不想把这些说的在详细了，推荐一篇关于设计的文章，这里讲的很详细：[手机APP UI设计尺寸基础知识](http://www.jianshu.com/p/d1c6b78fe9e3)


### 1像素的border
本文的思考就是源于我想在不同dpr的屏幕上画出1个物理像素的border。

### 解决方案

还是直接说解决方案吧......大致思路是这样的：
{% highlight html %}
  <meta name="viewport" content="width=divice-width, initial-scale=1, maximum-scale=1, minimum-scale=1, user-scalable=no"/>
{% endhighlight %}
>根据屏幕的dpr设置不同的initial-scale，同时使用rem，并动态改变根元素的font-size值。

比如对于那些dpr==1（iphone3g和一些低端Android机）的机型这么设置：
{% highlight html %}
  <meta name="viewport" content="width=divice-width, initial-scale=0.5, maximum-scale=0.5, minimum-scale=0.5, user-scalable=no"/>
{% endhighlight %}
{% highlight css %}
  html{
    font-size:10px;
  }
{% endhighlight %}

对于那些dpr==2（iphone4,5,6等），我们这么设置：
{% highlight html %}
  <meta name="viewport" content="width=divice-width, initial-scale=0.5, maximum-scale=0.5, minimum-scale=0.5, user-scalable=no"/>
{% endhighlight %}

{% highlight css %}
  html{
    font-size:20px;
  }
{% endhighlight %}

对于那些dpr==3（iphone6+,6s+等），我们这么设置：
{% highlight html %}
  <meta name="viewport" content="width=divice-width, initial-scale=0.3333333333333333, maximum-scale=0.3333333333333333, minimum-scale=0.3333333333333333, user-scalable=no"/>
{% endhighlight %}

{% highlight css %}
  html{
    font-size:30px;
  }
{% endhighlight %}

现实很残酷，综合考虑市场上用户使用的移动设备，苹果的产品也就只用考虑dpr为1，2和3三种情况，而混乱的安卓市场存在各种dpr的屏幕：1.5,1.7,2.4,3.7......这可怎么解决啊！！！

要是我肯定会挑出来出来常见的几种做适配开发，其他的一律按dpr为1处理。但是后来我找到了淘宝的解决方案，有些简单粗暴，淘宝只对IOS设备进行了细分，Android全部按照dpr=1处理。美团同样也是这么解决的（只区分了dpr为1和2）。我只能提Android用户感到惋惜。可能是因为webapp还没有真正的占领市场，他们都把主要精力放在原生APP上，但是webapp迟早会占领市场。

### 最终解决方案

不用再写这些：
{% highlight html %}
  <meta name="viewport" content="width=divice-width, initial-scale=0.5, maximum-scale=0.5, minimum-scale=0.5, user-scalable=no"/>
{% endhighlight %}

{% highlight css %}
  html{
    font-size:20px;
  }
{% endhighlight %}

使用淘宝的解决方案：
{% highlight html %}
<script src="http://g.tbcdn.cn/mtb/lib-flexible/{{version}}/??flexible_css.js,flexible.js"></script>
{% endhighlight %}

引用在页面的最前面，js将根据当前设备使用合适的initial-scale，并设置合适的根元素font-size。

[font-size设置多少合适，这里有一部分在聊](http://isux.tencent.com/web-app-rem.html)

[flexible on github](https://github.com/amfe/lib-flexible) 




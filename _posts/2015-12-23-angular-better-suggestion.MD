---
layout: post
title:  对AngularJS项目进行性能调优的几个总结
---

***  


![ 对AngularJS项目进行性能调优的几个总结 ](/blog/img/2015/2015-12-22-angular-better-suggestion-001.jpg)

### 感觉

我本是java出身，在做了几个angular的项目后，发现angular身上有java框架的影子，在在网上看看有没有人跟我有同感，发现确实如此。同为Google的子嗣，看来基因真的很强大，不过女大18变的NG2.0是不是脱胎换骨，我会去跟这位女子更亲密的接触看看；

言归正传。

当绑定对象超过 2000 时性能会明显下降，当然这不是一个精确的数字。很明显，针对这一点，我们应该减少双向绑定的数据，比如使用`once-bound`，像这样`{{::userName}}`(v1.3+)。


在网上见到过一段计算render时间的代码，既是分享也是记录：

{% highlight javascript %}
// Post repeat directive for logging the rendering time  
angular.module('siApp.services').directive('postRepeatDirective',   
  ['$timeout', '$log',  'TimeTracker',   
  function($timeout, $log, TimeTracker) {  
    return function(scope, element, attrs) {  
      if (scope.$last){  
         $timeout(function(){  
             var timeFinishedLoadingList = TimeTracker.reviewListLoaded();  
             var ref = new Date(timeFinishedLoadingList);  
             var end = new Date();  
         });  
       }  
    };  
  }  
]);  
{% endhighlight %}

{% highlight html %}
// Use in HTML:  
<tr ng-repeat="item in items" post-repeat-directive>…</tr> 
{% endhighlight %}

当然也可以使用Batarang（chrome的插件）来进行性能测试；

### 尽量不要使用内联的方法计算数据

{% highlight html %}
// bad  
<tr ng-show="item('args').length>0">…</tr> 
{% endhighlight %}
{% highlight html %}
// better 
<tr ng-show="item('args').length>0">…</tr> 
{% endhighlight %}

### ng-if来代替ng-show

ng-if会阻止渲染。允许是可以使用ng-if来代替ng-show。

### 防抖动输入

在较早的版本中可能还要自己写代码，延迟$digest()循环，但是v1.3后，可以直接使用`$debounce`(详细的options可以查看API):
{% highlight html %}
<input ng-model="userName" ng-model-options="{ debounce: 300 }" />
{% endhighlight %}

### 合理使用$destroy

AngularJS在销毁一个scope和把一个scope从它的父级移除之前会广播一个$destroy事件。监听这个事件对清理任务和资源是至关重要的,否则可能会继续消耗内存或CPU。

{% highlight javascript %}
$scope.$on("$destroy", function() {
    //清理内存的代码......
    if (timer) {
        $timeout.cancel(timer);
    }
    //......
});
{% endhighlight %}





---
layout: post
title: Angular Directive 深入浅出
---

***  

![Angular Directive 深入浅出 ](/blog/img/2015/2015-12-10-angular-directive-1-001.jpg)

### 要点
  
+ compile 与 link (操作元素、添加 CSS 样式、绑定事件)
+ 指令与控制器之间的交互
+ 指令间的交互
+ scope 的类型与独立 scope
+ scope 的绑定策略

### 通过代码学习

+ 简单的指令

{% highlight javascript %}
    app.directive('hello', function() {
        return {
            // ECMC分别代表: Element, Attribute, Comment, Class
            // Default: A
            restrict: "E/A/M/C",
            // 模板
            // template: '<h1>也可以直接在这里写模板</h1>',
            templateUrl: 'templ.html',
            // 是否替换节点内的内容
            // <hello><span>我是原来的内容</span></hello>
            replace: true
        }
    });
{% endhighlight %}

+ transculde 转置
transclude 作用在于将指令内部原有的内容放置到该指令模板的指定位置（注意：replace: true是不行的！ ），这样非常有用于指令嵌套。

{% highlight javascript %}
    app.directive('ele', function() {
    return {
        // 注意：不能用 replace: true
        restrict: 'AE',
        transclude: true,
        // 标签内部内容将会被转置到 div[ng-transclude] 内部
        template: "<h1>Hello 我是原来的内容</h1><div ng-transclude></div>"
    }
});
{% endhighlight %}

+ 嵌套指令

{% highlight javascript %}
    app.directive('hello', function() {
        return {
            restrict: "E",
            require:^
            replace: true,
            templateUrl: 'templ.html',
        }
    });
{% endhighlight %}

+ 模板缓存

> run 方法会在注册器加载完所有模块之‘后’被执行一次;
> $templateCache 可以缓存模板以供多个指令使用;
>put & get 类似面向对象的 setter & getter 方法。

{% highlight javascript %}
    app.run(function($templateCache) {
        $templateCache.put('hello.html', '<div>Hello AngularJS!</div>');
    });
    // use get method to get cache
    app.directive('ele', function($templateCache) {
        return {
            template: $templateCache.get('hello.html')
        }
    });
{% endhighlight %}

+ compile 与 link

>加载阶段

加载 angular.js，找到 ng-app 指令，确定应用的边界。

>编译阶段

遍历 DOM，找到所有指令,根据指令代码中的 template、replace、transclude 转换 DOM 结构,如果存在 compile 函数则调用。

>链接阶段

对每一条指令运行 link 函数,link 函数一般用来操作 DOM、绑定事件监听器,
指令调用控制器的方法，使用 link 。

{% highlight javascript %}
    <loader howToLoad="loadData()">Hover to load</loader>
    <loader howToLoad="loadData2()">Hover to load</loader>
{% endhighlight %}

{% highlight javascript %}
    myModule.controller('MyCtrl', ['$scope',
        function($scope) {
            $scope.loadData = function() {
                console.log('Loading...');
            };
            $scope.loadData2 = function() {
                console.log('Loading2...');
            }
        }
    ]);
    myModule.directive('loader', function() {
        return {
            resetrict: 'AE',
            template: '',
            replace: true,
            link: function(scope, element, attr) {
                // 绑定事件
                element.bind('mouseenter', function() {
                    // 以下两种形式都可以，推荐下面的
                    scope.loadData();
                    scope.$apply('loadData()');
                    // 获取属性值
                    // 根据指令特定属性的不同应用不同方法
                    // 方法应小写
                    scope.$apply(attrs.howtoload);
                });
            }
        }
    });

{% endhighlight %}

+ 指令之间的交互

重点是创建独立 scope，使得指令之间不互相影响

{% highlight javascript %}
    <superman strength>Strength</superman>
    <superman strength speed>Strength &amp; Speed</superman>
    <superman strength speed light>Stength &amp; Speed &amp; Light</superman>
{% endhighlight %}

{% highlight javascript %}
    myModule.directive('superman', function() {
        return {
            // 创建独立 scope
            scope: {},
            restrict: 'AE',
            // 希望指令暴露出一些方法编写在 controller 里面供其他指令调用
            // 同时使用 this 指代 $scope，这样交互的指令才能引用
            controller: function($scope) {
                $scope.abilities = [];
                this.addStrength = function () {
                    $scope.abilities.push('Strength');
                };
                this.addSpeed = function () {
                    $scope.abilities.push('Speed');
                };
                this.addLight = function () {
                    $scope.abilities.push('Light');
                };
            },
            // link 处理指令内部事件
            link: function (scope, element, attrs) {
                element.addClass('btn btn-primary btn-lg');
                element.bind('mouseenter', function() {
                    console.log(scope.abilities);
                });
            }
        };
    });
    myModule.directive('strength', function() {
        return {
            // 依赖于 superman 指令，这样 link 函数才可以调用 supermanCtrl 参数
            require: '^superman',
            link: function(scope, element, attrs, supermanCtrl) {
                supermanCtrl.addStrength();
            }
        };
    });
    myModule.directive('speed', function() {
        return {
            require: '^superman',
            link: function(scope, element, attrs, supermanCtrl) {
                supermanCtrl.addSpeed();
            }
        };
    });
    myModule.directive('light', function() {
        return {
            require: '^superman',
            link: function(scope, element, attrs, supermanCtrl) {
                supermanCtrl.addLight();
            }
        };
    });

{% endhighlight %}

scope 的可选项

|    方式    |        作用       | 数据绑定  |
| ---------- |:-----------------:| -----:|
|     @      |       字符串（@username）      | controller  => directive |
|     =      |       表达式（=password）      | controller <=> directive  |
|     &      |        函数（&age）       |     |

|    scope    |        作用       |
| ----------  |:-----------------:|
|    false    |       该指令同父级使用同一个作用域      |
|    true     |  new一个新的作用域，并继承父级作用域（directive可以访问父级的作用域，反过来不能）  |
|     {}      |      创建一个新的，独立的作用域       |

directive 在使用隔离scope候，提供了三种方法同隔离之外的地方交互。这三种分别是:
`@` 绑定一个局部scope属性到当前dom节点的属性值。结果总是一个字符串，因为dom属性是字符串。
`&` 提供一种方式执行一个表达式在父scope的上下文中。如果没有指定attr名称，则属性名称为相同的本地名称。
`=` 通过d的attr属性的值在局部scope的属性和父scope 属性名之间建立双向绑定。

@ 局部 scope 属性
@ 方式局部属性用来访问 directive 外部环境定义的字符串值，主要是通过directive所在的标签属性绑定外部字符串值。这种绑定是单向的，即父scope的绑定变化，directive 中的 scope 的属性会同步变化，而隔离 scope 中的绑定变化，父 scope 是不知道的。

+ `require`选项的细节

require参数可以被设置为字符串或数组，字符串代表另外一个指令的名字。require会将控制器注入到其值所指定的指令中，并作为当前指令的链接函数的第四个参数。
字符串或数组元素的值是会在当前指令的作用域中使用的指令名称。scope会影响指令作用域的指向，是一个隔离作用域，一个有依赖的作用域或者完全没有作用域。在任何情况下，AngularJS编译器在查找子控制器时都会参考当前指令的模板。如果不使用^前缀，指令只会在自身的元素上查找控制器。

{% highlight javascript %}
    //... 
    restrict: 'EA', 
    require: 'ngModel' 
//... 
{% endhighlight %}

指令定义只会查找定义在指令作当前用域中的ng-model=""。

{% highlight javascript %}
    <!-- 指令会在本地作用域查找ng-model --> 
    <div my-directive ng-model="object"></div> 
{% endhighlight %}

require参数的值可以用下面的前缀进行修饰，这会改变查找控制器时的行为：
`?`  在当前指令中没有找到所需要的控制器，会将null作为传给link函数的第四个参数。
`^`  如果添加了^前缀，指令会在上游的指令链中查找require参数所指定的控制器。
`?^` 将前面两个选项的行为组合起来，我们可选择地加载需要的指令并在父指令链中进行查找。没有前缀如果没有前缀，指令将会在自身所提供的控制器中进行查找，如果没有找到任何控制器（或具有指定名字的指令）就抛出一个错误。

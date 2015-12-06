---
layout: post
title: 使用ionic,cordova,angularJS 搭建webApp
---
***  
![web native hybird app ](/blog/img/2015/2015-12-02-ionic-cordova-angular-webapp-01.jpg)

关于Native App,Hybird App,Web App可自行脑补，从上图总也可以看出来个所以然来。还有最新的技术React Native（我会另开[博文](/blog/2015/12/06/React-1.html)做详细介绍）。  

虽然有`React Native`这神器 Ionic 多少显得有些无力，但是在`React Native`的填坑阶段，ionic还是很好的选择。  

### Ionic 是什么？  
> Ionic(ionicframework)一款接近原生的Html5移动App开发框架，使用html css js就可以开发app。
IONIC 是目前最有潜力的一款 HTML5 手机应用开发框架。通过 SASS 构建应用程序，它提供了很多 UI 组件来帮助开发者开发强大的应用。 它使用 JavaScript MVVM 框架和 AngularJS 来增强应用。提供数据的双向绑定，使用它成为 Web 和移动开发者的共同选择。Ionic是一个专注于用WEB开发技术，基于HTML5创建类似于手机平台原生应用的一个开发框架。Ionic框架的目的是从web的角度开发手机应用，基于PhoneGap(已更名为 Cordova )的编译平台，可以实现编译成各个平台的应用程序。  

### Cordova 是什么？

> Cordova提供了一组设备相关的API，通过这组API，移动应用能够以JavaScript访问原生的设备功能，如摄像头、麦克风等。Cordova还提供了一组统一的JavaScript类库，以及为这些类库所用的设备相关的原生后台代码。

### AngularJS 是什么？
> AngularJS是一款优秀的前端JS框架，有着诸多特性，最为核心的是：MVVM、模块化、自动化双向数据绑定、语义化标签、依赖注入等等。

简单点说，Ionic是移动端的前端框架，封装了移动端的操作行为和UI组件等；而Cordova，由于JavaScript不能直接调用移动设备的硬件，比如摄像头等，所以Cordova可以使你通过JS调用这些，当然你也可以自己写调用这些设备的API，再通过JS调用;Cordovar还有一个作用就是将写好的项目编译打包成相应的Android或IOS应用；AngularJS就是个前端JS框架，不多说。

### 1.准备工作

开发环境  

+ [下载node](https://nodejs.org/en/)  并配置本地的node环境
+ `npm install cordova -g` 安装Cordova
+ `npm install ionic -g`  安装Ionic  

以上三个是否安装成功都可以通过`--vsersion`来检验，这里也不做过多介绍。

### 插播

写到这里我突然不想再写下去，因为最近一直在使用React Native，之前在写Ionic的项目的时候就听闻Ionic做来的APP确实不太好，这里的不好主要集中在性能上，当页面状态发生变化时，需要对页面进行必要的重绘，而重绘是十分损耗性能的一件事，会出现页面跟新是的卡顿，尤其是在复杂业务中，导致用户体验真的会降几个档次（这里说的是在Android平台，IOS表现的还算可以，但也不排除复杂业务带来的性能降低），如果你你之前用过ng，那用这一套写一个APP会觉得如鱼得水，开发效率比起原生应用有大幅提升，产品的更新迭代也会变得轻松许多。但是，是由于我现在对React很是着迷，只能现将Ionic暂时放下，如果后续有需要，我会再补上。





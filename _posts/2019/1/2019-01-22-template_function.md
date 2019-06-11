---  
layout:     post
title:      设计模式之模板方法模式
subtitle:   模板方法
date:       2019-01-22
author:     lhbasura
header-img: 
keywords_post:  "设计模式,模板方法"
catalog: true
tags:
    -   设计模式
    -   模板方法
---  
## 1 简介 
模板方法模式定义了一个算法的`步骤`，并允许子类别为一个或多个步骤提供其实践方式。让子类别在不改变算法架构的情况下，重新定义算法中的某些步骤,这是百度百科的描述,但是我觉得结合具体的例子更有助于理解。
## 2 举个🌰
#### 2.1 图书出版
书的出版我们可以认为需要经过创作、装订、审核等过程，这个可以称为`步骤`下面我们用一个Book类来进行描述

{% highlight java lineanchors %}

class Book{
    String name;

    public void publish()
    {
        System.out.println("write " + name); //写书
        System.out.println("binding " + name); //装订
        System.out.println("examine " + name); //审核
        System.out.println("publish " + name);
    }
}
{% endhighlight %}
我们在使用时，调用Book对象的publish方法即可以完成书的出版，但是我们知道书也可以有很多种类，比如漫画、小说，我们可以认为
它们是Book的子类就像下面这样

{% highlight java lineanchors %}
class Commic extends Book{

}
class Novel extends Book{

}
{% endhighlight %}
这样我们需要进行出版慢画或小说时可以直接通过调用Commic或Novel对象从Book继承的publish方法。

#### 2.2 老板有话说 
然后这时候老板看了你的代码以后说，漫画的创作怎么能是"write"呢？漫画是paint(画)出来的呀，所以这时候你就会考虑在Commic类中重写
publish方法如下

{% highlight java lineanchors %}
class Commic extends Book{
    @override
    public void publish()
    {
        System.out.println("paint " + name); //写改为画
        System.out.println("binding " + name); //装订
        System.out.println("examine " + name); //审核
        System.out.println("publish " + name);
    }
}
{% endhighlight %}

#### 2.3 publish重写得心好累
但是这样我们会发现一个问题，我们只是改变了书的创作但是却把整个publish重写了，而且以后如果需要加入电子书是不是需要
把paint改为input然后重写整个publish方法呢？
因此这种方式使得代码的重用性很低，同时也违背了`开闭原则`
>`开闭原则`：一个类应该对修改关闭对扩展开放，对于上述这个例子，publish作为通用的方法，不应该进行频繁改动或重写   

#### 2.4 考虑变化与通用
这时候我们就需要考虑变化的部分和通用的部分。
在这个例子中，只有创作这一步是变化的，装订和审核都是通用的，所以我们只需要把创作这步操作进行重写,因此我们可以在父类中把创作声明为抽象

{% highlight java lineanchors %}
abstract class Book{
    String name;

    abstract public void create();    

    public void publish()
    {
        create();
        System.out.println("binding " + name); //装订
        System.out.println("examine " + name); //审核
        System.out.println("publish " + name);
    }
}
{% endhighlight %}

在Commic类中我们应该重写create而不是publish

{% highlight java lineanchors %}
class Commic extends Book{
    @override
    public void create(){
        System.out.print("paint "+ name);
    }
}
{% endhighlight %}

此时我们扩展了create并没有改变publish，但是我们还是可以调用Commic的publish方法完成出版操作。

## 3 最后  
最后老板说，最近市场上出来了一批录音的书，我需要你完成出版，这时你可以非常轻松的说，没问题，很快搞定！！！😁

{% highlight java lineanchors %}
class VoiceBook extends Book{
    @override
    public void create()
    {
        System.out.print("record "+ name);
    }
}
{% endhighlight %}

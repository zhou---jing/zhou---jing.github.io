---
layout:     post
title:      "JAVA多线程"
subtitle:   "生产者——消费者模型"
date:       2014-09-1 9:23:00
author:     "zhou---jing"
header-img: "img/post-bg-07.jpg"
---

<p>这种模型在现实生活中比较常见的模型，下面我们可以来看看这种模型的实现；此模型的需求是：</p>

<blockquote>
<p>a）：消费者必须在仓库中有产品的时候才能进行消费</p>
<p>b）：生产者在仓库中没有产品的时候才能被告知进行生产</p>
<p>c）：生产者在仓库满的时候则停止生产</p>
</blockquote>

<p>分析之后，我们知道生产者和消费者是两个独立的线程，它们分别进行消费和生产操作，对于仓库提供出来的接口对产品进行生产或者消费的api是需要进行同步的，因为这是共享资源。示例代码如下：</p>

<a href="#">
    <img src="{{ site.baseurl }}/img/thread_producerandconsumer/thread-producerandconsumer1.png" alt="生产者——消费者模型">
</a>
<a href="#">
    <img src="{{ site.baseurl }}/img/thread_producerandconsumer/thread-producerandconsumer2.png" alt="生产者——消费者模型">
</a>
<a href="#">
    <img src="{{ site.baseurl }}/img/thread_producerandconsumer/thread-producerandconsumer3.png" alt="生产者——消费者模型">
</a>
<a href="#">
    <img src="{{ site.baseurl }}/img/thread_producerandconsumer/thread-producerandconsumer4.png" alt="生产者——消费者模型">
</a>

<p>我们可以看到这个的实现方式是通过wait和notifyAll或者notify来实现的，它们给实现了一种可以类似通知的效果，直到某一条件满足才通知线程资源有了，可以来进行争夺了。但是又会存在一个问题，我们对于通知的接收方是所有在该对象上加锁的线程或者是其中一个，但是我们对于线程的“精确”通知是无法做到的，不过从java5之后条件变量可以轻松做到。我们先来看下条件变量的定义：</p>

<blockquote>条件变量就是表示条件的一种变量。但是必须说明，这里的条件是没有实际含义的，仅仅是个标记而已，并且条件的含义往往通过代码来赋予其含义</blockquote>

<p>下面来看下例子吧，就拿上面的生产者-消费者来说，我们需要做的是在满仓的情况下只通知消费者消费，而在空仓的情况下只通知生产者生产，而不是上面例子中的所有线程。示例代码如下：</p>

<a href="#">
    <img src="{{ site.baseurl }}/img/thread_producerandconsumer/thread-producerandconsumer5.png" alt="生产者——消费者模型">
</a>

<p>总结来说一句：await、wait、notify、notifyll、signal、singnalll都必须在获取了对象锁之后才能使用，不然会报错。就如下面这样来写就会出现问题。</p>

<a href="#">
    <img src="{{ site.baseurl }}/img/thread_producerandconsumer/thread-producerandconsumer6.png" alt="生产者——消费者模型">
</a>

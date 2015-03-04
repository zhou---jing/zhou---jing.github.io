---
layout:     post
title:      "JAVA多线程"
subtitle:   "多线程的线程状态"
date:       2014-08-24 12:00:00
author:     "zhou---jing"
header-img: "img/post-bg-04.jpg"
---

<p>多线程调度分为分时调度和抢占式调度，JVM线程调度采用抢占式调度方式，即根据线程的优先级来获取CPU的使用权（优先级只是概率上的区别）。</p>

<a href="#">
    <img src="{{ site.baseurl }}/img/thread_state/thread-state1.png" alt="线程状态">
</a>

<p>yield：暂停当前的线程，即让出cpu并且此时当前线程变为可执行状态，它会让jvm线程调度器再次调度一次（而不是网上有些人所说的）。</p>

<a href="#">
    <img src="{{ site.baseurl }}/img/thread_state/thread-state2.png" alt="线程状态">
</a>

<p>下面来通过实例来进行证明：</p>

<a href="#">
    <img src="{{ site.baseurl }}/img/thread_state/thread-state3.png" alt="线程状态">
</a>

<p>我们设置t2的优先级比t1的优先级低，那么如果按照上述所说的，在调用了yield方法之后，只允许相同优先级的线程执行，那么在t2调用一次yield之后并不能调度到t1（因为优先级的问题），那么不应该存在线程2到线程1的转换（除了线程2执行完成后），但是看结果：</p>

<a href="#">
    <img src="{{ site.baseurl }}/img/thread_state/thread-state4.png" alt="线程状态">
</a>

<p>则验证上述所说的是错误的。</p>

<p>join：在当前调用该方法的线程执行完成之后，才能继续下面代码的执行。这样的方式可以实现线程的简单同步。</p>

<a href="#">
    <img src="{{ site.baseurl }}/img/thread_state/thread-state5.png" alt="线程状态">
</a>

<a href="#">
    <img src="{{ site.baseurl }}/img/thread_state/thread-state6.png" alt="线程状态">
</a>


<p>当然我们还看到了join超时，其实意思就是“过了多少时间，你线程没有执行完，我就不等你了”，调用的api是join(long time)。</p>

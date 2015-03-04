---
layout:     post
title:      "JAVA多线程"
subtitle:   "多线程同步"
date:       2014-09-18 12:00:00
author:     "zhou---jing"
header-img: "img/post-bg-05.jpg"
---

<p>线程同步的作用就是为了共享资源，在java中实现线程同步的方式有两种。</p>

<h2 class="section-heading">1):线程的同步方式</h2>

<blockquote>
<p>a)：加锁机制</p>
<p>b）：Volatile关键字</p>
<p>c）：信号量</p>
</blockquote>

<h4 class="section-heading">1.1):加锁机制</h4>
<h5 class="section-heading">1.1.1):synchronized</h5>
<p>对于同步方法来说，不论是实例方法还是静态方法，加的锁都是在对象上加的，其他访问该方法的线程必须获取得到该对象的锁，而同步代码块的作用范围更小，在执行完同步代码块之后将锁释放，也就是说在同步代码块之前的代码不是同步的，多个线程可以访问。</p>

<h5 class="section-heading">1.1.2):java5之后提供的锁对象</h5>
<a href="#">
    <img src="{{ site.baseurl }}/img/thread_synchronization/thread-synchronization1.png" alt="线程同步">
</a>
<a href="#">
    <img src="{{ site.baseurl }}/img/thread_synchronization/thread-synchronization2.png" alt="线程同步">
</a>

<p>而ReadLock以及WriteLock则是Reentranteadriteock的内部类；</p>
<p>ReentrantLock：可重入锁，简单来说就是同一个线程在获取该锁之后如果线程再次获取该锁这是可以的，但是如果是不可重入锁的话，则会造成死锁（不过现在没有找到不可重入的锁示例），可重入锁的实现方式就是为每个锁记录当前锁的持有线程以及计数器，当某一线程请求成功后，JVM会记下锁的持有线程，并且将计数器置为1；此时其它线程请求该锁，则必须等待；而该持有锁的线程如果再次请求这个锁，就可以再次拿到这个锁，同时计数器会递增；当线程退出同步代码块时，计数器会递减，如果计数器为0，则释放该锁。 而相对的则有不可重入锁，它在再次申请锁的时候会导致死锁，因为它以及占着资源而且还需要申请资源，但是申请的资源已经被自己占着了，下面来看下示例：</p>

<a href="#">
    <img src="{{ site.baseurl }}/img/thread_synchronization/thread-synchronization3.png" alt="线程同步">
</a>
<a href="#">
    <img src="{{ site.baseurl }}/img/thread_synchronization/thread-synchronization4.png" alt="线程同步">
</a>

<p>注意其中的while(isLocked)循环，它又被叫做“自旋锁”。当isLocked为true时，调用lock()的线程在wait()调用上阻塞等待。为防止该线程没有收到notify()调用也从wait()中返回（也称作虚假唤醒）， 这个线程会重新去检查isLocked条件以决定当前是否可以安全地继续执行还是需要重新保持等待，而不是认为线程被唤醒了就可以安全地继续执行了。如果 isLocked为false，当前线程会退出while(isLocked)循环，并将isLocked设回true，让其它正在调用lock()方法 的线程能够在Lock实例上加锁。</p>
<p>那么我们如何修改该锁，而可以把它变为可重用性呢？分析上面可以知道，主要的问题在于“自旋锁”让申请资源的线程等待，并且在上面我们已经知道了可重入锁的实现原理，那么简单更改锁就可以实现此效果了。</p>

<a href="#">
    <img src="{{ site.baseurl }}/img/thread_synchronization/thread-synchronization5.png" alt="线程同步">
</a>

<p>ReadLock：读锁是共享锁，即几个线程可以共享该锁；</p>
<p>WriteLock：写锁是排它锁，即只有一个线程可以获取该锁；</p>


<h4 class="section-heading">1.2):Volatile</h4>
<p>而对于Volatile关键字来说，它的作用是保证每次读取该关键字修饰的变量的值都是从主存中读取，所以它能保证每次读取的变量值都是最新的，但是不保证是同步的。</p>

<a href="#">
    <img src="{{ site.baseurl }}/img/thread_synchronization/thread-synchronization6.png" alt="线程同步">
</a>

<p>一般来说，处理变量都包括从主存读——copy变量副本保存在本线程——使用变量——写回主存，而Volatile关键字的变量每次访问的时候都是从主存读取过来的，它们之间的区别就在于线程在访问一般变量时候只是从主存读取一次，而Volatile关键字的变量则是读取多次，个人感觉Volatile关键字的变量和引用变量相似。</p>

<h4 class="section-heading">1.3):信号量</h4>

<p>信号量在一定情况下（当许可数量为1个线程的时候）也可以达到同步的作用，也可达到共享资源的目的，下面我们来看下关于信号量的介绍；它的介绍如下：</p>

<blockquote>执行操作时可以首先获得许可【semaphore.acquire();】，并在使用后释放许可 【semaphore.release();】。如果没有许可，那么acquire方法将会一直阻塞直到有许可（或者直到被终端或者操作超时）。</blockquote>

<p>由上面定义我们可以看到信号量的作用：可以用来控制同时访问某个特定资源的操作数量，或者某个操作的数量，简单来说，它可以控制资源的访问数量但是不能控制资源的数量。</p>

<p>下面来看下例子，这个例子是在生产者-消费者基础更改，添加需求为：</p>

<blockquote>
<p>a）：每次只能最多两个消费者进行消费或者等待消费（等待生产，仓库库存为空）</p>
<p>b）：每次只能最多三个生产者进行生产或者等待生产（等待消费，仓库是满的）</p>
</blockquote>

<a href="#">
    <img src="{{ site.baseurl }}/img/thread_synchronization/thread-synchronization7.png" alt="线程同步">
</a>
<a href="#">
    <img src="{{ site.baseurl }}/img/thread_synchronization/thread-synchronization8.png" alt="线程同步">
</a>

<h2 class="section-heading">3):死锁</h2>

<h4 class="section-heading">3.1):死锁的介绍</h4>

<p>对象加锁的方式可以使得线程之前访问资源是同步的，但是有的时候加锁的顺序将会带来错误，就比如我们常常看见的死锁问题。</p>

<blockquote>简单来说，死锁的问题就是我占用着资源并且还要请求你的资源，但是你占用着自己的资源并且还需要我的资源，从而造成了一种循环等待的情形，这样就造成栏死锁。</blockquote>

<p>根据上面描述，我们可以找到死锁产生的四个条件:</p>

<blockquote>
<p>a）：循环等待（你等我 我等你）</p>
<p>b）：互斥条件（我和你共同竞争同一资源）</p>
<p>c）：不可抢占条件（你和我的资源只能自己释放而不能你进行抢占我的资源）</p>
<p>d）：占有且申请条件（竞争的资源需要申请，并且自己已经占有部分资源而且还需要申请新的资源）</p>
</blockquote>

<p>下面我们来看下死锁的例子：</p>
<a href="#">
    <img src="{{ site.baseurl }}/img/thread_synchronization/thread-synchronization9.png" alt="线程同步">
</a>
<a href="#">
    <img src="{{ site.baseurl }}/img/thread_synchronization/thread-synchronization10.png" alt="线程同步">
</a>

<h4 class="section-heading">3.2):死锁的预防</h4>
<blockquote>
<p>a）：可以选择在每个线程中让申请资源的顺序是一样的，如刚才的例子来说，可以选择把两个线程中的申请资源顺序都是t1,t2;</p>
<p>b）：将竞争的资源进行包装，所以需要用到其中资源的必须现申请该包装的锁，如刚才的例子，可以选择把t1和t2共同组成一个资源，则不论申请t1和t2的时候都需对该资源加锁。</p>
</blockquote>

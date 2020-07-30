---
title: leetcode-day27
date: 2020-07-14 19:55:03
tags:
- leetcode
- 并发编程
- java
categories:
- 程序人生
- 算法
declare: true
comments: true
---

![图片](http://api.mtyqx.cn/api/random.php?413)
<!-- more -->


### 题目[1114.按序打印](https://leetcode-cn.com/problems/print-in-order/)
我们提供了一个类：

```
public class Foo {
  public void one() { print("one"); }
  public void two() { print("two"); }
  public void three() { print("three"); }
}
```

三个不同的线程将会共用一个 Foo 实例。

    1. 线程 A 将会调用 one() 方法
    2. 线程 B 将会调用 two() 方法
    3. 线程 C 将会调用 three() 方法
请设计修改程序，以确保 two() 方法在 one() 方法之后被执行，three() 方法在 two() 方法之后被执行。


#### 代码

```java
class Foo {
    private AtomicInteger fistJobDone = new AtomicInteger(0);
    private AtomicInteger secondJobDone = new AtomicInteger(0);//通过两个原子变量来实现对工作流程的控制


    public Foo() {

    }

    public void first(Runnable printFirst) throws InterruptedException {

        // printFirst.run() outputs "first". Do not change or remove this line.
        printFirst.run();
        fistJobDone.incrementAndGet();
    }

    public void second(Runnable printSecond) throws InterruptedException {

        // printSecond.run() outputs "second". Do not change or remove this line.
        while (fistJobDone.get() != 1) {
        }
        printSecond.run();
        secondJobDone.incrementAndGet();
    }

    public void third(Runnable printThird) throws InterruptedException {

        // printThird.run() outputs "third". Do not change or remove this line.
        while (secondJobDone.get()!=1){}
        printThird.run();

    }
}
```

```java
class Foo3 {

    boolean firstJobDone = false;
    boolean secondJobDone = false;
    Object lock = new Object();

    public Foo3() {

    }

    public void first(Runnable printFirst) throws InterruptedException {

        // printFirst.run() outputs "first". Do not change or remove this line.
        synchronized (lock) {
            printFirst.run();
            firstJobDone = true;
            lock.notifyAll();//唤醒其他阻塞的线程，notifyAll不会释放锁，要一直到退出同步代码块，锁才会被释放
        }
    }

    public void second(Runnable printSecond) throws InterruptedException {

        // printSecond.run() outputs "second". Do not change or remove this line.
        synchronized (lock){
            while (!firstJobDone){
                lock.wait();//前面的工作没有做完，释放锁并进入阻塞状态，等待唤醒
            }
            printSecond.run();
            secondJobDone = true;
            lock.notifyAll();//工作做完了，唤醒阻塞的线程
        }
    }

    public void third(Runnable printThird) throws InterruptedException {

        // printThird.run() outputs "third". Do not change or remove this line.
        synchronized (lock) {
            while (!secondJobDone){
                lock.wait();
            }
            printThird.run();
        }
    }
}
```


```java
class Foo5 {

    private Semaphore sp1= new Semaphore(0);
    private Semaphore sp2 = new Semaphore(0);


    public Foo5() {

    }

    public void first(Runnable printFirst) throws InterruptedException {

        // printFirst.run() outputs "first". Do not change or remove this line.
        printFirst.run();
        sp1.release();
    }

    public void second(Runnable printSecond) throws InterruptedException {
        sp1.acquire();
        // printSecond.run() outputs "second". Do not change or remove this line.
        printSecond.run();
        sp2.release();
    }

    public void third(Runnable printThird) throws InterruptedException {
        sp2.acquire();
        // printThird.run() outputs "third". Do not change or remove this line.
        printThird.run();
    }
}
```

```java
class Foo6 {

    private CountDownLatch lock1 = new CountDownLatch(1);
    private CountDownLatch lock2 = new CountDownLatch(1);

    public Foo6() {
    }

    public void first(Runnable printFirst) throws InterruptedException {

        // printFirst.run() outputs "first". Do not change or remove this line.
        printFirst.run();
        lock1.countDown();
    }

    public void second(Runnable printSecond) throws InterruptedException {
        lock1.await();
        // printSecond.run() outputs "second". Do not change or remove this line.
        printSecond.run();
        lock2.countDown();
    }

    public void third(Runnable printThird) throws InterruptedException {
        lock2.await();
        // printThird.run() outputs "third". Do not change or remove this line.
        printThird.run();
    }
}
```


### 题目[1115. 交替打印FooBar](https://leetcode-cn.com/problems/print-foobar-alternately/)
两个不同的线程将会共用一个 FooBar 实例。其中一个线程将会调用 foo() 方法，另一个线程将会调用 bar() 方法。

请设计修改程序，以确保 "foobar" 被输出 n 次。

#### 代码

```java
class FooBar {
    private int n;
    private Semaphore sp1 = new Semaphore(1);
    private Semaphore sp2 = new Semaphore(0);
    public FooBar(int n) {
        this.n = n;
    }

    public void foo(Runnable printFoo) throws InterruptedException {

        for (int i = 0; i < n; i++) {
            sp1.acquire();
            // printFoo.run() outputs "foo". Do not change or remove this line.
            printFoo.run();
            sp2.release();
        }
    }

    public void bar(Runnable printBar) throws InterruptedException {

        for (int i = 0; i < n; i++) {
            sp2.acquire();
            // printBar.run() outputs "bar". Do not change or remove this line.
            printBar.run();
            sp1.release();
        }
    }
}

class FooBar2 {
    private int n;
    Lock lock = new ReentrantLock(true);
    volatile boolean permitted = true;

    public FooBar2(int n) {
        this.n = n;
    }

    public void foo(Runnable printFoo) throws InterruptedException {

        for (int i = 0; i < n;) {
            lock.lock();
            // printFoo.run() outputs "foo". Do not change or remove this line.
            try {
                if (permitted) {
                    printFoo.run();
                    i++;
                    permitted = false;
                }
            }finally {
                lock.unlock();
            }
        }
    }

    public void bar(Runnable printBar) throws InterruptedException {

        for (int i = 0; i < n; ) {
            lock.lock();
            // printBar.run() outputs "bar". Do not change or remove this line.
            try {
                if (!permitted){
                    i++;//这里造成线程自旋
                    printBar.run();
                    permitted = true;
                }
            }finally {
                lock.unlock();
            }
        }
    }
}

class FooBar3 {
    private int n;
    private CyclicBarrier cyclicBarrier = new CyclicBarrier(2);
    volatile boolean fin = true;
    public FooBar3(int n) {
        this.n = n;
    }

    public void foo(Runnable printFoo) throws InterruptedException {

        for (int i = 0; i < n; i++) {
            while (!fin) {
                // printFoo.run() outputs "foo". Do not change or remove this line.
                printFoo.run();
                fin = false;
                try {
                    cyclicBarrier.await();
                }catch (BrokenBarrierException e){
                }
            }
        }
    }

    public void bar(Runnable printBar) throws InterruptedException {

        for (int i = 0; i < n; i++) {

            try {
                // printBar.run() outputs "bar". Do not change or remove this line.
                cyclicBarrier.await();
            } catch (Exception e) {
            }
            printBar.run();
            fin = true;
        }
    }
}


class FooBar4 {
    private int n;
    private ReentrantLock lock = new ReentrantLock();
    private Condition fooCondition = lock.newCondition();
    private Condition barCondition = lock.newCondition();
    int count = 1;


    public FooBar4(int n) {
        this.n = n;
    }

    public void foo(Runnable printFoo) throws InterruptedException {

        for (int i = 0; i < n; i++) {
            lock.lock();
            if (count!=1){
                fooCondition.await();
            }
            // printFoo.run() outputs "foo". Do not change or remove this line.
            printFoo.run();
            barCondition.signal();
            count = 2;//happen-before原则
            lock.unlock();
        }
    }

    public void bar(Runnable printBar) throws InterruptedException {

        for (int i = 0; i < n; i++) {
            lock.lock();
            if (count!=2){
                barCondition.await();
            }
            // printBar.run() outputs "bar". Do not change or remove this line.
            printBar.run();
            fooCondition.signal();
            count = 1;
            lock.unlock();
        }
    }
}
```

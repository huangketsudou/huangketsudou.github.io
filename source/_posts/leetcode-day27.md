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


### 题目[1116. 打印零与奇偶数](https://leetcode-cn.com/problems/print-zero-even-odd/)
假设有这么一个类：
```java
class ZeroEvenOdd {
  public ZeroEvenOdd(int n) { ... }      // 构造函数
  public void zero(printNumber) { ... }  // 仅打印出 0
  public void even(printNumber) { ... }  // 仅打印出 偶数
  public void odd(printNumber) { ... }   // 仅打印出 奇数
}
```
相同的一个 ZeroEvenOdd 类实例将会传递给三个不同的线程：

线程 A 将调用 zero()，它只输出 0 。
线程 B 将调用 even()，它只输出偶数。
线程 C 将调用 odd()，它只输出奇数。
每个线程都有一个 printNumber 方法来输出一个整数。请修改给出的代码以输出整数序列 010203040506... ，其中序列的长度必须为 2n。


#### 用例
输入：n = 2
输出："0102"

输入：n = 5
输出："0102030405"

#### 解题思路
1. ReentrantLock
2. Semaphore


#### 代码
```java
class ZeroEvenOdd2 {
    private int n;
    private volatile int who = 0;
    private ReentrantLock lock = new ReentrantLock();
    private Condition conditionZero = lock.newCondition();
    private Condition conditionEven = lock.newCondition();
    private Condition conditionOdd = lock.newCondition();


    public ZeroEvenOdd2(int n) {
        this.n = n;
    }

    public void zero(IntConsumer printNumber) throws InterruptedException {
        lock.lock();
        try {
            for (int i = 1; i <= n; i++) {
                while (who != 0) {
                    conditionZero.await();
                }
                printNumber.accept(0);
                if ((i & 1) == 0) {
                    who = 2;
                    conditionEven.signalAll();
                } else {
                    who = 1;
                    conditionOdd.signalAll();
                }
            }
        } finally {
            lock.unlock();
        }
    }

    public void even(IntConsumer printNumber) throws InterruptedException {
        lock.lock();
        try {
            for (int i = 2; i <= n; i = i + 2) {
                while (who != 2) {
                    conditionEven.await();
                }
                printNumber.accept(i);
                who = 0;
                conditionZero.signalAll();
            }
        } finally {
            lock.unlock();
        }
    }

    public void odd(IntConsumer printNumber) throws InterruptedException {
        lock.lock();
        try {
            for (int i = 1; i <= n; i = i + 2) {
                while (who != 1) {
                    conditionOdd.await();
                }
                printNumber.accept(i);
                who = 0;
                conditionZero.signalAll();
            }
        } finally {
            lock.unlock();
        }
    }

    public static void main(String[] args) {
        ZeroEvenOdd2 o = new ZeroEvenOdd2(5);
        Thread t1 = new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    o.zero(System.out::println);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        Thread t2 = new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    o.even(System.out::println);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        Thread t3 = new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    o.odd(System.out::println);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        t1.start();
        t2.start();
        t3.start();
    }
}

class ZeroEvenOdd3 {
    private int n;
    private Semaphore zero = new Semaphore(1);
    private Semaphore odd = new Semaphore(0);
    private Semaphore even = new Semaphore(0);

    public ZeroEvenOdd3(int n) {
        this.n = n;
    }

    // printNumber.accept(x) outputs "x", where x is an integer.
    public void zero(IntConsumer printNumber) throws InterruptedException {
        for (int i = 1; i <= n; i++) {
            zero.acquire();
            printNumber.accept(i);
            if (i % 2 == 1) {
                odd.release();
            } else {
                even.release();
            }
        }
    }

    public void even(IntConsumer printNumber) throws InterruptedException {
        for (int i = 2; i <= n; i = i + 2) {
            even.acquire();
            printNumber.accept(i);
            zero.release();
        }
    }

    public void odd(IntConsumer printNumber) throws InterruptedException {
        for (int i = 1; i <= n; i = i + 2) {
            odd.acquire();
            printNumber.accept(i);
            zero.release();
        }
    }
}
```

### 题目[1117. H2O 生成](https://leetcode-cn.com/problems/building-h2o/)
现在有两种线程，氧 oxygen 和氢 hydrogen，你的目标是组织这两种线程来产生水分子。

存在一个屏障（barrier）使得每个线程必须等候直到一个完整水分子能够被产生出来。

氢和氧线程会被分别给予 releaseHydrogen 和 releaseOxygen 方法来允许它们突破屏障。

这些线程应该三三成组突破屏障并能立即组合产生一个水分子。

你必须保证产生一个水分子所需线程的结合必须发生在下一个水分子产生之前。

换句话说:

如果一个氧线程到达屏障时没有氢线程到达，它必须等候直到两个氢线程到达。
如果一个氢线程到达屏障时没有其它线程到达，它必须等候直到一个氧线程和另一个氢线程到达。
书写满足这些限制条件的氢、氧线程同步代码。


#### 用例
输入: "HOH"
输出: "HHO"


输入: "OOHHHH"
输出: "HHOHHO"
#### 解题思路
利用Semaphore和CycleBarrier来实现控制，最开始想用CountdownLatch来实现，但是CountDownLatch是一个不可复用的类型

#### 代码
```java
class H2O {
    private Semaphore H = new Semaphore(2);
    private Semaphore O = new Semaphore(1);
    private CyclicBarrier cyclicBarrier = new CyclicBarrier(3);
    
    
    public H2O() {

    }

    public void hydrogen(Runnable releaseHydrogen) throws InterruptedException {
        H.acquire();
        try {
            cyclicBarrier.await();
        }catch (Exception e){
        }
        
        // releaseHydrogen.run() outputs "H". Do not change or remove this line.
        releaseHydrogen.run();
        H.release();
    }

    public void oxygen(Runnable releaseOxygen) throws InterruptedException {
        O.acquire();
        try {
            cyclicBarrier.await();
        }catch (Exception e){
        }
        // releaseOxygen.run() outputs "O". Do not change or remove this line.
        releaseOxygen.run();
        O.release();
    }
}
```

### 题目[](哲学家用餐问题解决)

#### 解题思路
思路一：哲学家用餐的时候要么同时拿起两只叉子，要么都不要拿
思路二：要求哲学家拿筷子的顺序要相同，先拿序号小的

#### 代码

```java
public class Philosopher extends Thread {
    private String name;
    private Fork fork;

    public Philosopher(String name, Fork fork) {
        this.name = name;
        this.fork = fork;
    }

    public void run() {
        while (true) {
            thinking();
            fork.takefork();
            eating();
            fork.putfork();
        }
    }


    public void thinking() {
        System.out.println("thinking....");
    }

    public void eating() {
        System.out.println("eating...");
    }
}

class Fork {
    private boolean[] used = {false, false, false, false, false};

    public synchronized void takefork() {//在同一时刻只有一个人可以拿筷子，但是在哲学家思考，吃饭的时候其他人可以拿
        String name = Thread.currentThread().getName();
        int i = Integer.parseInt(name);
        while (used[i] || used[(i + 1) % 5]) {
            try {
                wait();
            } catch (InterruptedException e) {

            }
        }
        used[i] = true;
        used[(i + 1) % 5] = true;
    }

    public synchronized void putfork() {
        String name = Thread.currentThread().getName();
        int i = Integer.parseInt(name);
        used[i] = false;
        used[(i + 1) % 5] = false;
        notifyAll();
    }
}
```

```java
import java.util.concurrent.Semaphore;

class DiningPhilosophers {

    private Semaphore[] chopsticks = new Semaphore[5];

    public DiningPhilosophers() {
        for (int i = 0; i < 5; i++) {
            chopsticks[i] = new Semaphore(1);
        }

    }

    // call the run() method of any runnable to execute its code
    public void wantsToEat(int philosopher,
                           Runnable pickLeftFork,
                           Runnable pickRightFork,
                           Runnable eat,
                           Runnable putLeftFork,
                           Runnable putRightFork) throws InterruptedException {
        int left = philosopher % 5;
        int right = (philosopher + 1) % 5;
        if (philosopher==4){
            chopsticks[right].acquire();
            chopsticks[left].acquire();
        }else {
            chopsticks[left].acquire();

            chopsticks[right].acquire();

        }
        eat.run();
        pickRightFork.run();
        pickLeftFork.run();
        putLeftFork.run();
        putRightFork.run();
        chopsticks[left].release();
        chopsticks[right].release();
    }

}
```


### 题目[]()


#### 用例


#### 解题思路


#### 代码
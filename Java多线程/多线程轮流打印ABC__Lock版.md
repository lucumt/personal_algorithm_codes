# 题目要求

> 有3个线程，要求按照指定的次数依次轮流打印`A`、`B`、`C`3个字符



# 实现思路

通过`Lock`接口的`await()`(条件等待)方式实现线程间的依次唤醒，其核心是利用一个变量来记录当前哪个线程可以执行。

# 代码实现

```java
package com.lucumt.review.nov02;

import java.util.concurrent.CountDownLatch;
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class ConditionTest {

    public static void main(String[] args) {
        print();
    }

    private static int num = 1;
    private static Lock lock = new ReentrantLock();
    private static Condition conditionA = lock.newCondition();
    private static Condition conditionB = lock.newCondition();
    private static Condition conditionC = lock.newCondition();

    private static CountDownLatch latch = new CountDownLatch(10);

    private static void print() {
        long loop = latch.getCount();

        new Thread(() -> {
            try {
                for (int i = 0; i < loop; i++) {
                    printA();
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }, "A").start();

        new Thread(() -> {
            try {
                for (int i = 0; i < loop; i++) {
                    printB();
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }, "B").start();

        new Thread(() -> {
            try {
                for (int i = 0; i < loop; i++) {
                    printC(i);
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }, "C").start();

        try {
            latch.await();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("打印完毕");
    }

    private static void printA() throws InterruptedException {
        lock.lock();
        if (num != 1) {
            conditionA.await();
        }
        System.out.print(Thread.currentThread().getName());
        num = 2;
        conditionB.signal();
        lock.unlock();
    }

    private static void printB() throws InterruptedException {
        lock.lock();
        if (num != 2) {
            conditionB.await();
        }
        System.out.print(Thread.currentThread().getName());
        num = 3;
        conditionC.signal();
        lock.unlock();
    }

    private static void printC(long loop) throws InterruptedException {
        lock.lock();
        if (num != 3) {
            conditionC.await();
        }
        System.out.print(Thread.currentThread().getName());
        System.out.println("[" + loop + "]");
        num = 1;
        conditionA.signal();
        latch.countDown();
        lock.unlock();
    }

}
```


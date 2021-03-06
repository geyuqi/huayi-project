---
description: 新生（NEW）运行（RUNNABLE）阻塞（BLOCKED）等待（WAITING）超时等待（TIMED_WATING）死亡（TERMINATED）
---

# 线程的6种状态

## 一、概念理解

### 新生（NEW）

**线程调用start方法之前的状态**

> ```text
> /**
>  * Thread state for a thread which has not yet started.
>  */
> NEW
> ```

### **运行（RUNNABLE）**

**线程调用start方法之后线程进入运行状态**

> ```text
> /**
>  * Thread state for a runnable thread.  A thread in the runnable
>  * state is executing in the Java virtual machine but it may
>  * be waiting for other resources from the operating system
>  * such as processor.
>  */
> RUNNABLE,
> ```

### 阻塞（BLOCKED）

**线程执行时等待同步方法执行时的状态，通常是等待锁释放时的状态，常与`synchronized`关键字相关**

> ```text
> /**
>  * Thread state for a thread blocked waiting for a monitor lock.
>  * A thread in the blocked state is waiting for a monitor lock
>  * to enter a synchronized block/method or
>  * reenter a synchronized block/method after calling
>  * {@link Object#wait() Object.wait}.
>  */
> BLOCKED,
> ```

### **等待（WATING）**

线程中执行了Object.wait方法或LockSupport.park方法或Thread.join方法时,线程会进入waiting状态

{% hint style="info" %}
Object.wait\(\)和LockSupport.park\(\)是当前线程主动进入等待状

Thread.join方法是其他线程执行join方法导致当前线程被动进入等待状态
{% endhint %}



> ```text
> /**
>  * Thread state for a waiting thread.
>  * A thread is in the waiting state due to calling one of the
>  * following methods:
>  * <ul>
>  *   <li>{@link Object#wait() Object.wait} with no timeout</li>
>  *   <li>{@link #join() Thread.join} with no timeout</li>
>  *   <li>{@link LockSupport#park() LockSupport.park}</li>
>  * </ul>
>  *
>  * <p>A thread in the waiting state is waiting for another thread to
>  * perform a particular action.
>  *
>  * For example, a thread that has called <tt>Object.wait()</tt>
>  * on an object is waiting for another thread to call
>  * <tt>Object.notify()</tt> or <tt>Object.notifyAll()</tt> on
>  * that object. A thread that has called <tt>Thread.join()</tt>
>  * is waiting for a specified thread to terminate.
>  */
> WAITING,
> ```



### 超时等待（TIMED\_WATING）

**原理同等待一致只是调用方法不同**



> ```text
> /**
>  * Thread state for a waiting thread with a specified waiting time.
>  * A thread is in the timed waiting state due to calling one of
>  * the following methods with a specified positive waiting time:
>  * <ul>
>  *   <li>{@link #sleep Thread.sleep}</li>
>  *   <li>{@link Object#wait(long) Object.wait} with timeout</li>
>  *   <li>{@link #join(long) Thread.join} with timeout</li>
>  *   <li>{@link LockSupport#parkNanos LockSupport.parkNanos}</li>
>  *   <li>{@link LockSupport#parkUntil LockSupport.parkUntil}</li>
>  * </ul>
>  */
> TIMED_WAITING,
> ```

 

### 死亡

**线程执行完成时的状态**

> ```text
> /**
>  * Thread state for a terminated thread.
>  * The thread has completed execution.
>  */
> TERMINATED;
> ```

## 二、代码演示

**线程创建/运行/销毁**

```text
package state;

import java.util.concurrent.TimeUnit;

/**
 * @Author: geyuqi
 * @DateTime: 8/10/2020 5:04 PM
 * @Description: 线程状态
 */
public class Demo01 {
    public static void main(String[] args) 
            throws InterruptedException {
        Thread thread = new Thread(() -> {
            System.out.println("hello thread");
        });
        System.out.println("线程状态=======>" 
                + thread.getState().name());//新生 start方法
        thread.start();
        System.out.println("线程状态=======>" 
                + thread.getState().name());//执行
        TimeUnit.SECONDS.sleep(1);//休眠一秒保证线程执行结束后再执行主线程
        System.out.println("线程状态=======>" 
                + thread.getState().name());//死亡
    }
}

```

运行结果：

```text
线程状态=======>NEW
线程状态=======>RUNNABLE
hello thread
线程状态=======>TERMINATED
```

**线程阻塞\(BLOCKED\)**

```text
package state;

import java.util.concurrent.TimeUnit;

/**
 * @Author: geyuqi
 * @DateTime: 8/10/2020 5:51 PM
 * @Description: blocked测试
 */
public class Demo02 {
    public static void main(String[] args) throws InterruptedException {
        String lock = "lock";
        Thread thread1 = new Thread(new MyThread(lock));
        Thread thread2 = new Thread(new MyThread(lock));
        thread1.start();
        thread2.start();
        TimeUnit.SECONDS.sleep(1);
        System.out.println(thread2.getState());
    }
}
```

```text
package state;

class MyThread implements Runnable {

    private String lock;

    public MyThread(String lock) {
        this.lock = lock;
    }

    @Override
    public void run() {
        synchronized (lock) {
            try {
                TimeUnit.SECONDS.sleep(3);
                System.out.println(Thread.currentThread().getName());
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

**运行结果：**

```text
thread2====>BLOCKED
```

**线程等待/超时等待**

**LockSupport.park\(\);**




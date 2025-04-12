---
title: Multi-Thread
date: 2023-10-26 00:00:00
tags: java
categories: lang
---

---

# 实现多线程的方法

## 继承 `Thread` 类, 重写 `run()` 方法

```java
// 1. 继承 Thread
class MyThread extends Thread{
    // 2. 重写 run 方法
    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            System.out.println("My Thread" + i);
        }
    }
}

public class Main {
    // main 方法本身也是一条线程
    public static void main(String[] args) {
        Thread myThread = new MyThread();  // 3. 创建自定义的线程
        myThread.start();  // 4. 执行自定义的线程
        for (int i = 0; i < 10; i++) {
            System.out.println("Main Thread: " + i);
        }
    }
}
```

优点: 

- 编码简单

缺点: 

- 已经继承了 Thread 类, 不能再继承别的类, 不利于功能扩展

注意: 

- 启动线程必须使用 start() 方法, 如果调用 run() 方法, 那么只能起到单线程的效果
- 不要把主线程 (main) 的任务放在子线程之前

## 实现 `Runnable` 接口, 实现 `run()` 方法

```java
// 1. 实现 Runnable (任务对象) 接口
class MyRunnable implements Runnable {
    // 2. 重写 run 方法
    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            System.out.println("My Runnable: " + i);
        }
    }
}

public class Main {
    public static void main(String[] args) {
        Runnable mr = new MyRunnable();  // 3. 创建任务对象
        Thread t = new Thread(mr);  // 4. 把任务对象交给线程对象, 并创建线程对象
        t.start();
        for (int i = 0; i < 10; i++) {
            System.out.println("Main Thread: " + i);
        }
    }
}
```

优点;

- 可扩展性强, `MyRunable` 可以继承别的类, 也可以实现别的接口

### 匿名内部类写法

```java
public class Main {
    public static void main(String[] args) {
        // 1. 直接创建 Runnable 接口的匿名内部类
        Runnable r = new Runnable() {
            @Override
            public void run() {
                for (int i = 0; i < 10; i++) {
                    System.out.println(Thread.currentThread().getName() + ": " + i);
                }
            }
        };
        new Thread(r).start();
        for (int i = 0; i < 10; i++) {
            System.out.println(Thread.currentThread().getName() + ": " + i);
        }
    }
}
```

进一步简化

```java
public class Main {
    public static void main(String[] args) {
        new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i = 0; i < 10; i++) {
                    System.out.println(Thread.currentThread().getName() + ": " + i);
                }
            }
        }).start();
        for (int i = 0; i < 10; i++) {
            System.out.println(Thread.currentThread().getName() + ": " + i);
        }
    }
}
```

再简化 (Lambda 表达式)

```java
public class Main {
    public static void main(String[] args) {
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                System.out.println(Thread.currentThread().getName() + ": " + i);
            }
        }).start();
        for (int i = 0; i < 10; i++) {
            System.out.println(Thread.currentThread().getName() + ": " + i);
        }
    }
}
```

## 实现 `Callable` 接口

以上两种方式的 `run()` 方法都是 `void`, 当线程执行完毕后有 `return`, 就不好用了

```java
// 1. 实现 Callable 泛型接口 (声明线程返回的数据类型)
class MyCallable implements Callable<String> {
    private int n;
    public MyCallable(int n) {
        if (n <0 ) throw new IllegalArgumentException();
        this.n = n;
    }

    // 2. 重写 call 方法
    @Override
    public String call() throws Exception {
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < n; i++) {
            sb.append(i);
        }
        return sb.toString();
    }
}

public class Main {
    public static void main(String[] args) {
        Callable<String> c5 = new MyCallable(5);  // 3. 创建 Callable 对象

        // FutureTask 是一个对象, 实现了 Runnable 接口
        FutureTask<String> f5 = new FutureTask<>(c5);  // 4. 把 Callable 对象封装成 FutureTask 任务对象 (相对于 Runnable)

        new Thread(f5).start();  // 5. 把任务对象交给 Thread
		
        // 6. 从任务对象 (FutureTask) 获取线程的结果 (注意抛异常)
        try {
            String res = f5.get();  // 当线程任务没有执行完毕, 主线程会在此等待
            System.out.println(res);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

优点: 

- 可以获取线程方法的 `return`, 可扩展性更强

缺点:

- 代码复杂

# Thread 常用方法

```java
public void run();
public void start();
public String getName();
public void setName();  // 在 start() 之前 set name
public static Thread currentThread();  // 获取调用该方法的线程
public static void sleep();
public final void join();
```

```java
public Thread(String name);
public Thread(Runable target);
public Thread(Runable target, String name);  // 在构造的时候给线程分配 name
```

## `currentThread()`

```java
public class Main {
    public static void main(String[] args) {
        new Thread(() -> {
            System.out.println(Thread.currentThread().getName());
            System.out.println("Hello World");
        }).start();

        new Thread(() -> {
            System.out.println(Thread.currentThread().getName());
            System.out.println("Hello World");
        }).start();

        System.out.println(Thread.currentThread().getName());
    }
}
```

```
Thread-0
Hello World
main
Thread-1
Hello World
```

## `sleep() & join()`

```java
public class Main {
    public static void main(String[] args) throws InterruptedException {
        for (int i = 0; i < 4; i++) {
            System.out.println(i);
            Thread.sleep(1000);
        }
    }
}
```

```java
public class Main {
    public static void main(String[] args) throws InterruptedException {
        Runnable r = new Runnable() {
            @Override
            public void run() {
                for (int i = 0; i < 10; i++) {
                    System.out.println(Thread.currentThread().getName() + ": " + i);
                }
            }
        };
//        Runnable r = () -> {
//            for (int i = 0; i < 10; i++) {
//                System.out.println(Thread.currentThread().getName() + ": " + i);
//            }
//        };
        Thread t1 = new Thread(r, "t1");
        Thread t2 = new Thread(r, "t2");
        Thread t3 = new Thread(r, "t3");

        t1.start();
        t1.join();  // 使用 join 让 t1 执行完成后主线程再往下执行
        t2.start();
        t3.start();
    }
}
```

# 线程安全

多个线程同时操作 (修改) 同一个共享资源

## 模拟线程安全问题

```java
public class Main {
    public static void main(String[] args) throws InterruptedException {
        Account account = new Account(8964, 10000.0);
        new GetMoneyThread(account, "B").start();
        new GetMoneyThread(account, "A").start();
    }
}

class GetMoneyThread extends Thread {
    private final Account account;
    
    public GetMoneyThread(Account account, String threadName) {
        super(threadName);
        this.account = account;
    }
    
    @Override
    public void run() {
        account.drawMoney(10000.0);
    }
}

@Getter
@Setter
class Account {
    private int cardId;
    private double money;

    public Account(int cardId, double money) {
        this.cardId = cardId;
        this.money = money;
    }

    public void drawMoney(double money) {
        String name = Thread.currentThread().getName();
        if (money > this.money) {
            System.out.printf("money in account %d is not enough\n", this.cardId);
            return;
        }
        this.money -= money;
        System.out.printf("%s draw money %f, account %d rest %f\n", name, money, cardId, this.money);
    }
}
```

```java
A draw money 10000.000000, account 8964 rest -10000.000000
B draw money 10000.000000, account 8964 rest -10000.000000
```

# 线程同步 --- 解决线程安全问题

让多个线程向后依次访问资源 --- 加锁

每次只允许一个线程加锁 (拿钥匙), 加锁的 (有钥匙的) 线程才能进入访问, 访问完毕后解锁 (还钥匙). 

## 同步代码块

```java
public class Main {
    public static void main(String[] args) throws InterruptedException {
        Account account = new Account(8964, 10000.0);
        new GetMoneyThread(account, "B").start();
        new GetMoneyThread(account, "A").start();
    }
}

class GetMoneyThread extends Thread {
    private final Account account;

    public GetMoneyThread(Account account, String threadName) {
        super(threadName);
        this.account = account;
    }

    @Override
    public void run() {
        account.drawMoney(10000.0);
    }
}

@Getter
@Setter
class Account {
    private int cardId;
    private double money;

    public Account(int cardId, double money) {
        this.cardId = cardId;
        this.money = money;
    }

    public void drawMoney(double money) {
        String name = Thread.currentThread().getName();
        synchronized ("lock" /* this */) {  // "lock" 是常量池里的对象, 仅此一份, 所有线程都公有它
            if (money > this.money) {
                System.out.printf("money in account %d is not enough\n", this.cardId);
                return;
            }
            this.money -= money;
            System.out.printf("%s draw money %f, account %d rest %f\n", name, money, cardId, this.money);
        }
    }
}
```

以上将同步锁设置成 "lock" 并不科学, 因为如果另外一个 account 的两名用户也来取钱, 那么它们的线程会受 "lock" 的影响. 相当于锁的范围过大, 因此, 应该使用 this 作为锁

```java
synchronized (this) {
	...
}
```

如果是静态方法, 则使用 类名.class 作为锁

```java
synchronized (MyClass.class) {
	...
}
```

## 同步方法

把访问共享核心资源的方法上锁

```java
public class Main {
    public static void main(String[] args) throws InterruptedException {
        Account account = new Account(8964, 10000.0);
        new GetMoneyThread(account, "B").start();
        new GetMoneyThread(account, "A").start();
    }
}

class GetMoneyThread extends Thread {
    private final Account account;

    public GetMoneyThread(Account account, String threadName) {
        super(threadName);
        this.account = account;
    }

    @Override
    public void run() {
        account.drawMoney(10000.0);
    }
}

@Getter
@Setter
class Account {
    private int cardId;
    private double money;

    public Account(int cardId, double money) {
        this.cardId = cardId;
        this.money = money;
    }

    public synchronized void drawMoney(double money) {  // 加上 synchronized 关键字, 变成同步方法
        String name = Thread.currentThread().getName();
        if (money > this.money) {
            System.out.printf("money in account %d is not enough\n", this.cardId);
            return;
        }
        this.money -= money;
        System.out.printf("%s draw money %f, account %d rest %f\n", name, money, cardId, this.money);
    }
}
```

### 同步方法和同步代码块比较

同步方法把整个方法给锁住了, 性能较差, 但可读性好

同步代码块可以灵活选取方法内真正需要访问公共资源的部分, 性能更好, 可读性差

## Lock 锁

自己创建锁对象 (Lock), 自己加锁, 解锁

Lock 是接口

```java
public class Main {
    public static void main(String[] args) throws InterruptedException {
        Account account = new Account(8964, 10000.0);
        new GetMoneyThread(account, "B").start();
        new GetMoneyThread(account, "A").start();
    }
}

class GetMoneyThread extends Thread {
    private final Account account;

    public GetMoneyThread(Account account, String threadName) {
        super(threadName);
        this.account = account;
    }

    @Override
    public void run() {
        account.drawMoney(10000.0);
    }
}

@Getter
@Setter
class Account {
    private int cardId;
    private double money;
    private final Lock lk = new ReentrantLock();  // 创建锁对象, 最好用 final 修饰

    public Account(int cardId, double money) {
        this.cardId = cardId;
        this.money = money;
    }

    public void drawMoney(double money) {
        String name = Thread.currentThread().getName();
        
        try {
            lk.lock();  // 加锁, 写在 try 中
            if (money > this.money) {
                System.out.printf("money in account %d is not enough\n", this.cardId);
                return;
            }
            this.money -= money;
            System.out.printf("%s draw money %f, account %d rest %f\n", name, money, cardId, this.money);
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            lk.unlock();  // 解锁操作一定要在 finally 中
        }

    }
}
```

# 线程通信

多个线程共享资源时, 线程通过某种方法相互告知当前状态, 以相互协调, 避免无效资源争夺.

## 生产者与消费者模型

Object 类的等待和唤醒方法

```
void wait()
void notify()
void notifyAll()
```

这些方法应使用当前同步锁对象 (作为锁的对象) 进行调用

```java
public class Main {
    public static void main(String[] args) {
        // 三个生产者线程, 负责生产包子, 每个线程每次只能生产一个包子并放在桌子上
        // 两个消费者线程, 负责吃包子, 每个线程每次只能在桌子上拿一个包子吃
        Desk desk = new Desk();

        // 3 个生产者
        for (int i = 1; i <= 3; i++) {
            new Thread(() -> {
                while (true) {
                    desk.put();
                }
            }, "Cooker" + i).start();
        }

        // 2 个消费者
        for (int i = 1; i <= 2; i++) {
            new Thread(() -> {
                while (true) {
                    desk.get();
                }
            }, "Customer" + i).start();
        }

    }
}

class Desk {
    private List<String> lst = new ArrayList<>();
    private String[] types = new String[]{"肉包", "菜包", "x包"};

    public synchronized void put() {
        String cookerName = Thread.currentThread().getName();
        try {
            if (lst.isEmpty()) {
                // 做包子
                String baozi = types[(int) (Math.random() * 3)];
                baozi = String.format("%sMadeBy%s", baozi, cookerName);
                System.out.println(baozi);
                lst.add(baozi);
                Thread.sleep(2000);  // 做包子耗时

                // 先唤醒别人, 再等待自己
                this.notifyAll();
                this.wait();
            } else {
                // 有包子, 不做了, 睡大觉
                this.notifyAll();
                this.wait();
            }

        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public synchronized void get() {
        String customerName = Thread.currentThread().getName();
        try {
            if (!lst.isEmpty()) {
                String baozi = lst.get(0);
                System.out.println(customerName + " eat " + baozi);
                lst.clear();
                Thread.sleep(1000);  // 吃包子耗时

                this.notifyAll();
                this.wait();
            } else {
                this.notifyAll();
                this.wait();
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```



```
x包MadeByCooker1
Customer2 eat x包MadeByCooker1
菜包MadeByCooker3
Customer1 eat 菜包MadeByCooker3
x包MadeByCooker1
Customer1 eat x包MadeByCooker1
肉包MadeByCooker3
Customer1 eat 肉包MadeByCooker3
肉包MadeByCooker1
Customer2 eat 肉包MadeByCooker1
菜包MadeByCooker2
Customer1 eat 菜包MadeByCooker2
```



# 线程池

线程池就是一个可以复用线程的技术

## 工作原理

工作线程 WorkThread

任务队列 WorkQueue (必须是实现了 Runnable 或 Callable 的对象)

## 创建线程池

**ExecutorService 线程池接口 + ThreadPoolExecutor 线程池实现类**

```java
public ThreadPoolExecutor(
    int corePoolSize,  // 核心线程数量
    int maximumPoolSize,  // 最大线程数量 >= 核心线程数量, 多出来的线程叫临时线程
    long keepAliveTime,  // 空闲临时线程的存活时间
    TimeUnit unit,  // 时间单位
    BlockingQueue<Runnable> workQueue,  // 任务队列 缓存队列
    ThreadFactory threadFactory,  // 线程工厂对象 负责创建线程 (核心 + 临时)
    RejectedExecutionHandler handler  // 拒绝策略 (线程都在忙 (核心 + 临时), 任务队列也满了, 新任务来了怎么处理)
) {...}
```

```java
public class Main {
    public static void main(String[] args) {
        ExecutorService pool = new ThreadPoolExecutor(
                3,
                5,
                8,
                TimeUnit.SECONDS,
                new ArrayBlockingQueue<>(4),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.AbortPolicy()
        );
    }
}
```

1. 临时线程什么时候创建?

   新任务提交时发现核心线程都在慢, 任务队列也满了, 并且还可以创建临时线程

2. 什么时候开始拒绝新任务?

   核心线程和临时线程都在忙, 任务队列也满了, 也不能再创建临时线程了

## 线程池处理 Runnable 任务

`void execute(Runnable command)` 方法处理 Runnable 任务

```java
public class Main {
    public static void main(String[] args) {
        ExecutorService pool = new ThreadPoolExecutor(
                3,
                5,
                8,
                TimeUnit.SECONDS,
                new ArrayBlockingQueue<>(4),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.AbortPolicy()
        );
        Runnable myRunnable = new MyRunnable();
        pool.execute(myRunnable);  // 创建一个线程 (核心线程)
        pool.execute(myRunnable);  // 创建一个线程 (核心线程)
        pool.execute(myRunnable);  // 创建一个线程 (核心线程)
        pool.execute(myRunnable);  // 复用核心线程 (存入任务队列)
        pool.execute(myRunnable);  // 复用核心线程 (存入任务队列)
        pool.execute(myRunnable);  // 复用核心线程 (存入任务队列)
        pool.execute(myRunnable);  // 复用核心线程 (存入任务队列)
        pool.execute(myRunnable);  // 临时线程 (任务队列满了)
        pool.execute(myRunnable);  // 临时线程 (任务队列满了)
        pool.execute(myRunnable);  // 拒绝
        pool.shutdown();  // 等任务全部执行完毕后关闭
    }
}

class MyRunnable implements Runnable {
    private final String[] dazibaos = {
            "伟大的毛主席万岁,万岁,万万岁!!!!!",
            "战无不胜的毛泽东思想万岁,万岁,万万岁!!!!!"
    };
    @Override
    public void run() {
        // 随便写个任务
        String name = Thread.currentThread().getName();
        try {
            for (String dazibao : dazibaos) {
                System.out.println(name + ": " + dazibao);
                Thread.sleep(10000);  // 等待时间较长
            }
        } catch (Exception e) {
            e.printStackTrace();
        }

    }
}
```

```java
pool-1-thread-2: 伟大的毛主席万岁,万岁,万万岁!!!!!
pool-1-thread-4: 伟大的毛主席万岁,万岁,万万岁!!!!!
pool-1-thread-3: 伟大的毛主席万岁,万岁,万万岁!!!!!
pool-1-thread-1: 伟大的毛主席万岁,万岁,万万岁!!!!!
pool-1-thread-5: 伟大的毛主席万岁,万岁,万万岁!!!!!
Exception in thread "main" java.util.concurrent.RejectedExecutionException: Task com.ypy.MyRunnable@6d6f6e28 rejected from java.util.concurrent.ThreadPoolExecutor@135fbaa4[Running, pool size = 5, active threads = 5, queued tasks = 4, completed tasks = 0]
	at java.util.concurrent.ThreadPoolExecutor$AbortPolicy.rejectedExecution(ThreadPoolExecutor.java:2063)
	at java.util.concurrent.ThreadPoolExecutor.reject(ThreadPoolExecutor.java:830)
	at java.util.concurrent.ThreadPoolExecutor.execute(ThreadPoolExecutor.java:1379)
	at com.ypy.Main.main(Main.java:29)
pool-1-thread-4: 战无不胜的毛泽东思想万岁,万岁,万万岁!!!!!
pool-1-thread-1: 战无不胜的毛泽东思想万岁,万岁,万万岁!!!!!
pool-1-thread-2: 战无不胜的毛泽东思想万岁,万岁,万万岁!!!!!
pool-1-thread-3: 战无不胜的毛泽东思想万岁,万岁,万万岁!!!!!
pool-1-thread-5: 战无不胜的毛泽东思想万岁,万岁,万万岁!!!!!
pool-1-thread-5: 伟大的毛主席万岁,万岁,万万岁!!!!!
pool-1-thread-1: 伟大的毛主席万岁,万岁,万万岁!!!!!
pool-1-thread-4: 伟大的毛主席万岁,万岁,万万岁!!!!!
pool-1-thread-3: 伟大的毛主席万岁,万岁,万万岁!!!!!
pool-1-thread-1: 战无不胜的毛泽东思想万岁,万岁,万万岁!!!!!
pool-1-thread-3: 战无不胜的毛泽东思想万岁,万岁,万万岁!!!!!
pool-1-thread-5: 战无不胜的毛泽东思想万岁,万岁,万万岁!!!!!
pool-1-thread-4: 战无不胜的毛泽东思想万岁,万岁,万万岁!!!!!
```

### 几类拒绝策略

```
ThreadPoolExecutor.AbortPolicy  // 抛出异常
ThreadPoolExecutor.DiscardPolicy  // 丢弃新任务, 不抛异常
ThreadPoolExecutor.DiscardOldestPolicy  // 丢弃任务队列的最老的任务, 不抛异常
ThreadPoolExecutor.CallerRunsPolicy  // 由主线程调用 run 方法
```

## 线程池处理 Callable 任务

`Future<T> submit(Callable<T> task)` 方法处理 Callable 任务. 执行任务, 返回未来任务对象以获取任务执行结果

```java
public class Main {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ExecutorService pool = new ThreadPoolExecutor(
                3,
                5,
                8,
                TimeUnit.SECONDS,
                new ArrayBlockingQueue<>(4),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.AbortPolicy()
        );
        // 使用 future 接受线程结果
        Future<String> f100 = pool.submit(new MyCallable(100));
        Future<String> f200 = pool.submit(new MyCallable(200));
        Future<String> f300 = pool.submit(new MyCallable(300));
        Future<String> f400 = pool.submit(new MyCallable(400));  // 复用之前的线程
        Future<String> f500 = pool.submit(new MyCallable(500));

        System.out.println(f100.get());
        System.out.println(f200.get());
        System.out.println(f300.get());
        System.out.println(f400.get());
        System.out.println(f500.get());
    }
}

class MyCallable implements Callable<String> {
    private int n;
    public MyCallable(int n) { this.n = n; }
    public String call() throws Exception {
        int sum = 0;
        for (int i = 1; i <= n; i++) sum += i;
        return String.format("%s: 1 + ... + %d = %d", Thread.currentThread().getName(), n, sum);
    }
}
```

```
pool-1-thread-1: 1 + ... + 100 = 5050
pool-1-thread-2: 1 + ... + 200 = 20100
pool-1-thread-3: 1 + ... + 300 = 45150
pool-1-thread-1: 1 + ... + 400 = 80200
pool-1-thread-3: 1 + ... + 500 = 125250
```

## Executors 工具类 (静态方法)

```
public static ExecutorService newFixedThreadPool(int nThreads)  // 创建固定线程数量的线程池 核心 = 最大
public static ExecutorService newSingleThreadExecutor()  // 创建只有一个线程的线程池
public static ExecutorService newCachedThreadPool()  // 创建线程数随任务增加而增加的线程池 (空闲 60s 回收)
public static ScheduledExecutorService newScheduledThreadPool(int corePoolSize)  // 给定的延时后执行任务, 或定期执行任务
```

底层都是通过 ThreadPoolExecutor 创建的线程池对象

```java
public class Main {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ExecutorService pool = Executors.newFixedThreadPool(3);
        // 使用 future 接受线程结果
        Future<String> f100 = pool.submit(new MyCallable(100));
        Future<String> f200 = pool.submit(new MyCallable(200));
        Future<String> f300 = pool.submit(new MyCallable(300));
        Future<String> f400 = pool.submit(new MyCallable(400));  // 复用之前的线程
        Future<String> f500 = pool.submit(new MyCallable(500));

        System.out.println(f100.get());
        System.out.println(f200.get());
        System.out.println(f300.get());
        System.out.println(f400.get());
        System.out.println(f500.get());
    }
}

class MyCallable implements Callable<String> {
    private int n;
    public MyCallable(int n) { this.n = n; }
    public String call() throws Exception {
        int sum = 0;
        for (int i = 1; i <= n; i++) sum += i;
        return String.format("%s: 1 + ... + %d = %d", Thread.currentThread().getName(), n, sum);
    }
}
```

### 大型并发系统, 使用 Executors 创建线程池可能出现风险

newFixedThreadPool, newSingleThreadPool, newCachedThreadPoll 方法搞出来的线程池的允许请求队列长度是 Integer.MAX_VALUE, 可能导致任务堆积, 造成 OOM

## 核心线程数量的配置

- 计算密集型任务: corePoolSize = CPU 核数 + 1
- IO 密集型任务: corePoolSize = CPU 核数 * 2

# 并发 / 并行

进程: 运行的程序

线程: 从属于进程

进程中的多个线程是并发和并行执行的

## 并发

进程中的线程是由 CPU 调度执行的, CPU 同时处理线程的数量有限, 为保证全部线程都能总体向前推进, CPU 会**轮询**为系统的每个线程服务, 由于 CPU 切换的速度很快, 给我们感觉这些线程在同时执行, 这就是**并发**.

## 并行

在同一个时刻上, 同时由多个线程在被 CPU 调度执行. 单核 CPU 不存在并行, CPU 核数决定了最大并行数量. 

## 多线程是怎么执行的?

并发和并行同时进行.

# 线程的生命周期

线程在一个生命周期中, 在一系列状态中不断转换

## Java 线程的六中状态 `Thread.State`

```java
public enum State {
        /**
         * Thread state for a thread which has not yet started.
         */
        NEW,

        /**
         * Thread state for a runnable thread.  A thread in the runnable
         * state is executing in the Java virtual machine but it may
         * be waiting for other resources from the operating system
         * such as processor.
         */
        RUNNABLE,

        /**
         * Thread state for a thread blocked waiting for a monitor lock.
         * A thread in the blocked state is waiting for a monitor lock
         * to enter a synchronized block/method or
         * reenter a synchronized block/method after calling
         * {@link Object#wait() Object.wait}.
         */
        BLOCKED,

        /**
         * Thread state for a waiting thread.
         * A thread is in the waiting state due to calling one of the
         * following methods:
         * <ul>
         *   <li>{@link Object#wait() Object.wait} with no timeout</li>
         *   <li>{@link #join() Thread.join} with no timeout</li>
         *   <li>{@link LockSupport#park() LockSupport.park}</li>
         * </ul>
         *
         * <p>A thread in the waiting state is waiting for another thread to
         * perform a particular action.
         *
         * For example, a thread that has called <tt>Object.wait()</tt>
         * on an object is waiting for another thread to call
         * <tt>Object.notify()</tt> or <tt>Object.notifyAll()</tt> on
         * that object. A thread that has called <tt>Thread.join()</tt>
         * is waiting for a specified thread to terminate.
         */
        WAITING,

        /**
         * Thread state for a waiting thread with a specified waiting time.
         * A thread is in the timed waiting state due to calling one of
         * the following methods with a specified positive waiting time:
         * <ul>
         *   <li>{@link #sleep Thread.sleep}</li>
         *   <li>{@link Object#wait(long) Object.wait} with timeout</li>
         *   <li>{@link #join(long) Thread.join} with timeout</li>
         *   <li>{@link LockSupport#parkNanos LockSupport.parkNanos}</li>
         *   <li>{@link LockSupport#parkUntil LockSupport.parkUntil}</li>
         * </ul>
         */
        TIMED_WAITING,

        /**
         * Thread state for a terminated thread.
         * The thread has completed execution.
         */
        TERMINATED;
    }
```

```
使用 new Thread() 新建线程 (NEW)
|
使用.start() 运行 (RUNNABLE)
|                        |
|                        |- 没抢到锁 锁阻塞 (BLOCKED)
|                        |
|                        |- 获得的锁对象调用了.wait() (WAITING)
|                        |
|                        `- 使用了 .sleep(毫秒) (不释放锁) or .wait(毫秒) (释放锁) (TIMED_WAITING)
|
执行完毕 (TERMINATED)      
```

# 乐观锁 / 悲观锁

以上的内容仅涉及悲观锁

## 悲观锁

一上来就加锁, 每次只能一个线程访问完毕后再解锁. 线程安全, 性能较差 (存在大量线程阻塞). 

## 乐观锁

一开始不上锁, 等要出现线程安全的时候才开始控制. 线程安全, 性能好.

## 例子

```java
public class Main {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        Runnable myRunnable = new MyRunnable();

        for (int i = 1; i <= 100; i ++) {
            new Thread(myRunnable).start();
        }
    }
}

class MyRunnable implements Runnable {
    private int cnt = 0;

    public void run() {
        for (int i = 0; i < 100; i++) {
            cnt ++;
            System.out.println(cnt);
        }
    }
}
```

```
...
9995
9996
9997  // 没有到 10000, 很明显出现了线程安全问题
```

### 使用 synchronized 加锁 (悲观锁)

```java
public class Main {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        Runnable myRunnable = new MyRunnable();

        for (int i = 1; i <= 100; i++) {
            new Thread(myRunnable).start();
        }
    }
}

class MyRunnable implements Runnable {
    private int cnt = 0;

    public void run() {
        for (int i = 0; i < 100; i++) {
            synchronized (this) {
                cnt++;
                System.out.println(cnt);
            }
        }
    }
}
```

```
...
9998
9999
10000  // 结果正确了 线程安全
```

### 使用原子类引入乐观锁

我们发现, 不加锁的情况下, 结果与正确值十分接近, 出现冲突的概率很低

**CAS 算法**: 每次记录拿到的数据, 计算出操作数据后的值, 再比对数据是否等于记录的数据 (没有被别的线程修改), 如果相等, 则把计算结果赋值, 不相等则计算结果作废. (版本比较)

```java
public class Main {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        Runnable myRunnable = new MyRunnable();

        for (int i = 1; i <= 100; i++) {
            new Thread(myRunnable).start();
        }
    }
}

class MyRunnable implements Runnable {
    private AtomicInteger cnt = new AtomicInteger(0);

    public void run() {
        for (int i = 0; i < 100; i++) {
            System.out.println(cnt.incrementAndGet());
        }
    }
}
```

# 多线程案例

## 分礼物

100 份礼物, A, B 两人同时发送, 当剩下的礼物小于 10 份则不再发送. 利用多线程模拟, 并打印 A, B 各种发送的礼物.

```java
public class Main {
    public static void main(String[] args) throws InterruptedException {
        // 搞100份礼物
        List<String> gifts = new LinkedList<>();
        String[] types = {"book", "pen", "card", "candy", "cake"};
        Random r = new Random();
        for (int i = 1; i <= 100; i++) gifts.add(i + types[r.nextInt(types.length)]);

        // 开始送
        SendGift A = new SendGift(gifts, "A");
        SendGift B = new SendGift(gifts, "B");

        B.start();
        A.start();

        A.join();
        B.join();

        System.out.println(A.getSendCnt());
        System.out.println(B.getSendCnt());
    }
}

class SendGift extends Thread {
    private final List<String> gifts;
    private final String threadName;
    private Integer sendCnt = 0;

    public SendGift(List<String> gifts, String threadName) {
        super.setName(threadName);
        this.threadName = threadName;
        this.gifts = gifts;
    }

    public Integer getSendCnt() {
        return sendCnt;
    }

    @Override
    public void run() {
        Random r = new Random();
        while (true) {
            synchronized (gifts) {
                if (gifts.size() <= 10) {
                    System.out.printf("%s has send %d gifts \n", threadName, sendCnt);
                    return;
                } else {
                    String sendGift = gifts.remove(r.nextInt(gifts.size()));
                    System.out.printf("%s send %s \n", threadName, sendGift);
                    sendCnt++;
                }

                // position 1
                /**
                 * B has send 58 gifts
                 * A has send 32 gifts
                 * 32
                 * 58
                 */
                try {
                    Thread.sleep(100);
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }

//            // position 2
//            /**
//             * B has send 45 gifts 
//             * A has send 45 gifts 
//             * 45
//             * 45
//             */
//            try {
//                Thread.sleep(100);
//            } catch (Exception e) {
//                e.printStackTrace();
//            }
        }
    }
}
```

### 没有 `sleep` 时

当不使用 `sleep` 时，由于线程 `B` 执行得更快，所以 `B` 会始终优先获得 `gifts` 的锁，导致 `A` 几乎没有机会操作 `gifts`。因此，输出的结果几乎全由 `B` 完成。

### 在 `position1` 使用 `sleep`

在 `position1` 位置添加 `sleep` 后，每次线程移除礼物后会短暂地暂停。这样一来，两个线程 `A` 和 `B` 的竞争就会变得更加均匀，线程在尝试获取锁时会发生交替执行。由于线程在进入 `synchronized` 代码块之前没有暂停，它们可能会争抢锁，导致每次发送礼物的次数不固定、随机分布。

### 在 `position2` 使用 `sleep`

在 `position2` 位置添加 `sleep` 后，每个线程在释放锁之后都会短暂暂停。这意味着 `A` 和 `B` 在释放锁后进入休眠状态，使得另一个线程更有机会获取锁，从而导致两个线程严格交替运行。在这种情况下，`A` 和 `B` 会平等分配到礼物的发送次数，使得发送的次数大致对半分。




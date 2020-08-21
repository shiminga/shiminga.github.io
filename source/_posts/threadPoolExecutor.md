---
title: 关于ThreadPoolExecutor线程池
date: 2019-04-29 18:52:10
tags: [JAVA,线程]
categories: 线程
---

​		这几天用到spring线程池ThreadPoolTaskExecutor的时候，出现了如下情况，其中qtp是调用线程池的线程,当时没理解为什么外面的线程会执行任务。后来通过ThreadPoolExecutor源代码找到了原因。
`Pda-Service1---->`
`Pda-Service2---->`
`Pda-Service3---->`
`Pda-Service4---->`
`Pda-Service5---->`
`Pda-Service6---->`
`Pda-Service7---->`
`Pda-Service8---->`
`Pda-Service9---->`
`Pda-Service10---->`
`Pda-Service11---->`
`Pda-Service12---->`
`Pda-Service13---->`
`Pda-Service14---->`
`Pda-Service15---->`
`Pda-Service16---->`
`Pda-Service17---->`
`qtp1009916891-30---->`
`Pda-Service18---->`

#### 初始化ThreadPoolExecutor

​		ThreadPoolExecutor的最完整的构造函数初始化了

```
int corePoolSize,//线程池中的核心线程数
int maximumPoolSize,//线程池的最大线程数量
long keepAliveTime,//线程池维护线程所允许的空闲时间
TimeUnit unit,//上述空闲时间的单位
BlockingQueue<Runnable> workQueue,//线程池所使用的等待队列
ThreadFactory threadFactory,//创建线程的线程工厂
RejectedExecutionHandler handler//任务满时的拒绝策略
```

#### 添加任务时的处理流程

​		当有任务通过execute(Runnable task)添加到线程池中时，

1 当前线程数小于corePoolSize，则创建新的线程来处理任务

2  当前线程数等于 corePoolSize，等待队列 workQueue未满，则任务放入等待队列

3  当前线程数大于corePoolSize，等待队列workQueue已满，并且当前线程数小于maximumPoolSize，创建新的线程处理任务

4  当前线程数大于corePoolSize，等待队列workQueue已满，并且当前线程数等于maximumPoolSize，那么通过 handler(拒绝执行策略)来处理此任务

**备注:**

**优先级 corePoolSize-->workQueue-->maximumPoolSize-->handler**

**默认当前线程数大于 corePoolSize时，某线程空闲时间超过keepAliveTime，线程将被回收。可通过allowCoreThreadTimeOut（boolean value）方法设置核心线程可销毁。**

#### 任务拒绝策略

​		ThreadPoolExecutor的拒绝策略有四种:

##### 调度者执行策略

​		即调用线程池的线程自己执行任务，如下源代码所示

```
public static class CallerRunsPolicy implements RejectedExecutionHandler {
    /**
     * Creates a {@code CallerRunsPolicy}.
     */
    public CallerRunsPolicy() { }

    /**
     * Executes task r in the caller's thread, unless the executor
     * has been shut down, in which case the task is discarded.
     *
     * @param r the runnable task requested to be executed
     * @param e the executor attempting to execute this task
     */
    public void rejectedExecution(Runnable r, ThreadPoolExecutor e) {
        if (!e.isShutdown()) {
            r.run();
        }
    }
}
```

##### 终止策略

​		即需要拒绝任务时终止线程池工作，如下源代码所示为抛出异常

```
public static class AbortPolicy implements RejectedExecutionHandler {
    /**
     * Creates an {@code AbortPolicy}.
     */
    public AbortPolicy() { }

    /**
     * Always throws RejectedExecutionException.
     *
     * @param r the runnable task requested to be executed
     * @param e the executor attempting to execute this task
     * @throws RejectedExecutionException always
     */
    public void rejectedExecution(Runnable r, ThreadPoolExecutor e) {
        throw new RejectedExecutionException("Task " + r.toString() +
                                             " rejected from " +
                                             e.toString());
    }
}
```

##### 抛弃策略

​		即抛弃新来无法处理的任务，如下源代码所示

```
public static class DiscardPolicy implements RejectedExecutionHandler {
    /**
     * Creates a {@code DiscardPolicy}.
     */
    public DiscardPolicy() { }

    /**
     * Does nothing, which has the effect of discarding task r.
     *
     * @param r the runnable task requested to be executed
     * @param e the executor attempting to execute this task
     */
    public void rejectedExecution(Runnable r, ThreadPoolExecutor e) {
    }
}
```

##### 抛弃最旧任务策略

​		即抛弃等待队列中最旧的任务，添加新任务，如下源代码所示

```
public static class DiscardOldestPolicy implements RejectedExecutionHandler {
    /**
     * Creates a {@code DiscardOldestPolicy} for the given executor.
     */
    public DiscardOldestPolicy() { }

    /**
     * Obtains and ignores the next task that the executor
     * would otherwise execute, if one is immediately available,
     * and then retries execution of task r, unless the executor
     * is shut down, in which case task r is instead discarded.
     *
     * @param r the runnable task requested to be executed
     * @param e the executor attempting to execute this task
     */
    public void rejectedExecution(Runnable r, ThreadPoolExecutor e) {
        if (!e.isShutdown()) {
            e.getQueue().poll();
            e.execute(r);
        }
    }
}
```

使用者可以根据自己的需求决定使用哪种拒绝策略。
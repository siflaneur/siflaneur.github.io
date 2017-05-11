---
title: Python-GIL
date: 2017-04-16 20:18:22
categories: Python
---

### GIL (全局解释器锁)

---

尽管 Python（特指 Cpython） 支持多线程编程，但是在解释器的 C语言实现中，有一部分并不是线程安全的，因此不能完全支持并发执行。

事实上解释器被一个称为全局解释器锁的东西保护着，任意时刻只允许一个 Python 线程投入执行。GIL 带来的最明显的影响就是，一个采用多线程技术的计算密集型应用只能在一个 CPU 运行。（多进程不受影响）

需要强调的一点是，GIL 只会对 CPU 密集型的程序造成影响（也就是完成计算任务的程序，非读取文件类型的程序），如果我们的程序主要是在做I/O操作，比如处理网络链接，那么选择多线程技术是一个明智的选择，因为大部分时间都花在等待对方发起连接上了。

对于 CPU 密集型的程序，类似于 Numpy 这样用 C 实现的库，就不会受到 GIL 的影响。



所以，要规避 GIL 限制，主要有两种常用的策略：

- 使用`mulitiprocessing` 模块来创建进程池，把它当作协处理器使用。

  使用进程池可以规避 GIL 的限制，每当有线程要执行 CPU 密集型的任务时，它就把任务提交到池中，然后进程池将任务转交给运行在另一个进程中的 Python 解释器。当线程等待结果的时候就释放 GIL 。此外，由于计算是在另一个单独的解释器中进行的，这就不再受到 GIL 的限制了，在多核系统上，将会发现采用这种技术能轻易地利用到所有的 CPU 核心。

```python
  # 处理一个较大计算量的任务
def some_work(args):
    ...
    return result


  # 单线程方式  
def some_thread():
    while true:
    ...
    r = some_work(args)
    ...


  # 进程池方式
pool = None
def some_work(args):
    ...
    return result

def some_thread():
    while True:
        ...
        r = pool.apply(some_work, (args))
        ...


if __name__ == '__main__':
    import multiprocessing
    pool = multiprocessing.Pool()
```




- 第二种方法是 C语言拓展编程，主要思想就是将计算密集型的任务转移到 C语言中，将其独立于 Python ，在 C代码中释放 GIL。通过在 C代码中插入特殊的宏来实现，就不展开讲述了。

---
title: Pool - Python多进程管理
date: 2016-11-28 17:20:48
tags: multiprocessing
categories: Python
---

__在Python中多进程管理使用Pool,可以设定最大进程数n，一次执行n个任务，当有一个进程完成后会有新的进程被添加进Pool。__

__示例代码：__


    import multiprocessing
    import os, time, random

    def Lee():
        print "Run task Lee - %s" % (os.getpid())  # os.getpid()获取当前的进程的ID
        start=time.time()
        time.sleep(random.random()*10)  # random.random()随机生成0-1之间的小数
        end=time.time()
        print 'Task Lee, runs %0.2f seconds.' %(end - start)

    def Marlon():
        print "Run task Marlon - %s" % (os.getpid())
        start = time.time()
        time.sleep(random.random()*40)
        end = time.time()
        print 'Task Marlon runs %0.2f seconds.' % (end - start)

    def Allen():
        print "Run task Allen - %s" % (os.getpid())
        start = time.time()
        time.sleep(random.random()*30)
        end = time.time()
        print 'Task Allen runs %0.2f seconds.' % (end - start)

    def Frank():
        print "Run task Frank - %s" % (os.getpid())
        start = time.time()
        time.sleep(random.random()*20)
        end = time.time()
        print 'Task Frank runs %0.2f seconds.' % (end - start)

    if __name__ == '__main__':
        function_list = [Lee, Marlon, Allen, Frank]
        print "parent process %s" % (os.getpid())
        pool = multiprocessing.Pool(2)
        for func in function_list:
            pool.apply_async(func)  # Pool执行函数，apply执行函数,当有一个进程执行完毕后，会添加一个新的进程到pool中
        print 'Waiting for all subprocesses done...'
        pool.close()
        pool.join()  # 调用join之前，一定要先调用close() 函数，否则会出错, close()执行后不会有新的进程加入到pool,join函数等待素有子进程结束
        print 'All subprocesses done.'

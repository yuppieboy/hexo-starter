---
title: Python单例的4种实现方法
date: 2016-11-28 10:00:56
tags: 设计模式
categories: Python

---

## 方法一 ##

实现`__new__`方法  
并在将一个类的实例绑定到类变量_instance上,  
如果`cls._instance`为None说明该类还没有实例化过,实例化该类,并返回  
如果`cls._instance`不为None,直接返回`cls._instance`

    class Singleton(object):
    def __new__(cls, *args, **kwargs):
        if not hasattr(cls, '_instance'):
            cls._instance = super(Singleton, cls).__new__(cls, *args, **kwargs)
        return cls._instance

    class A(Singleton):
    def __init__(self, s, v):
        self.s = s
        self.v = v

    a = A(1, 5)
    b = A(2, 4)

    print id(a), a.s
    print id(b), b.v

    """
    import borg

    其他文件调用borg的A类的实例即可调用其属性

    eg:

    print borg.a.s
    print borg.b.v
    """

## 方法二 ##

`共享模式`;所谓单例就是所有引用(实例、对象)拥有相同的状态(属性)和行为(方法)  
同一个类的所有实例天然拥有相同的行为(方法),  
只需要保证同一个类的所有实例具有相同的状态(属性)即可  
所有实例共享属性的最简单最直接的方法就是`__dict__`属性指向(引用)同一个字典(dict)

Tips: 这是一种特殊的单例方式,多个实例对象共享属性`a singleton with shared-state among instances`

    class Borg:
    __shared_state = {}

    def __init__(self):
        self.__dict__ = self.__shared_state
        self.state = 'Init'

    def __str__(self):
        return self.state


    class YourBorg(Borg):
        pass

    if __name__ == '__main__':
    rm1 = Borg()
    rm2 = Borg()

    rm1.state = 'Idle'
    rm2.state = 'Running'

    print('rm1: {0}'.format(rm1))
    print('rm2: {0}'.format(rm2))

    rm2.state = 'Zombie'

    print('rm1: {0}'.format(rm1))
    print('rm2: {0}'.format(rm2))

    print('rm1 id: {0}'.format(id(rm1)))
    print('rm2 id: {0}'.format(id(rm2)))

    rm3 = YourBorg()

    print('rm1: {0}'.format(rm1))
    print('rm2: {0}'.format(rm2))
    print('rm3: {0}'.format(rm3))

## 方法三 ##
本质上是方法1的升级（或者说高级版)  
使用`__metaclass__`（元类）的高级python用法

    class Basic(type):

    def __init__(cls, *args, **kwargs):
        super(Basic, cls).__init__(*args, **kwargs)

    def __new__(cls, *args, **kwargs):
        return super(Basic, cls).__new__(cls, *args, **kwargs)

    class Foo:
        __metaclass__ = Basic

        Foo.color = 'red'
        Foo.borderWidth = 1

    class rect:
        def __init__(self, color, borderWidth):
            self.color = color
            self.borderWidth = borderWidth


    if __name__ == '__main__':
        Foo.color = 'blue'
        a = rect(Foo.color, Foo.borderWidth)
        print a.__dict__

    """
    from borg import Foo

    其他文件调用Foo类的属性即可

    eg:

    print Foo.color
    print Foo.borderWidth
    """

## 方法四 ##
使用装饰器(`decorator`),  
这是一种更`pythonic`,更elegant的方法,  
单例类本身根本不知道自己是单例的,因为他本身(自己的代码)并不是单例的  

    def singleton(cls, *args, **kw):
        instances = {}
        def _singleton():
        if cls not in instances:
            instances[cls] = cls(*args, **kw)
            return instances[cls]
        return _singleton

    @singleton
    class MyClass(object):
        a = 1

    def __init__(self, x=0):
        self.x = x

    one = MyClass()
    two = MyClass()

    two.a = 3
    print one.a
    # 3
    print id(one)
    # 29660784
    print id(two)
    # 29660784
    print one == two
    # True
    print one is two
    # True
    one.x = 1
    print one.x
    # 1
    print two.x
    # 1

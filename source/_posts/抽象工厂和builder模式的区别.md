---
title: 抽象工厂和builder模式的区别
date: 2016-11-24 16:29:28
tags: 设计模式
categories: Python
---

__在工厂模式里，我们无需关心产品的各部分是如何被创建的，但在Builder模式里，会把产品的创建过程抽象为多个部分。也就是说工厂模式被关注的是整个产品，而Builder模式被关注的是产品的组成部分的创建过程。__

Builder模式的类图:

<div align="left">
<img src="https://raw.githubusercontent.com/yuppieboy/yuppieboy.github.io/master/images/builder_pattern.png" width = "50%" >
</div>

__Factory模式一进一出,Builder模式是分步流水线作业。__
当你需要做一系列有序的工作或者按照一定的逻辑来完成创建一个对象时 Builder就派上用场啦。

我们可以看到，Builer模式与工厂模式是极为相似的，但是他们还是有区别的：

__一、从结构上来看，Builder模式比工厂模式多了一个“导演类”的角色。__
在Builder模式的类图中，假如把这个导演类看做是最终调用的客户端，那么图中剩余的部分就可以看作是一个简单的工厂模式了。



__二、两者所创建的产品类型完全不一样。__
Factory创建只能是单一的产品(单一在这指它非复合产品),而Builder所创建的产品是复合产品，即产品本身就是由其它部件产品组成的。

举个例子来说，现在要生产一辆车，假设它就只由这三个部分组成：玻璃、轮子、发动机。
对于工厂模式来说，他创建后返回的，只能是玻璃，或者轮子，抑或是发动机。
不管怎么样，他不能向客户返回一辆完整的汽车，要得到一辆完整的汽车，客户必须自己动手去把这些零部件组装成一辆汽车。
从这个意义上来讲，工厂模式中的工厂，只是充当了零件厂的角色。
那Builder又是如何创建产品的呢？在Builder模式中，一般不需要、也不充许向客户返回单个部件，他向客户返回的，仅仅就是一部已经完全组装好的汽车成品。
对于汽车部件的生产细节，客户不需要、也不应该让他们知道。


__三、Builder和Factory的差别，还在于他们所生产部件产品所在产品树的问题。__
这样说，可能有点拗口。具体来说吧，在工厂模式中，我们知道，一个工厂可以创建多个产品，但一个工厂模式中所创建的某个产品，都会和另一个工厂中所创建的产品在同一棵继承树上。
Builder可以创建出可以创建出风马不相及的产品(虽然我们不这么做)，但是Factory模式一般是创建一类产品。

---

以下贴两端Python的抽象工厂和Builder模式的经典代码:

### 抽象工厂

    #!/usr/bin/env python
    # -*- coding: utf-8 -*-
    #http://ginstrom.com/scribbles/2007/10/08/design-patterns-python-style/

    """Implementation of the abstract factory pattern"""

    import random


    class PetShop:

        """A pet shop"""

        def __init__(self, animal_factory=None):
            """pet_factory is our abstract factory.  We can set it at will."""

            self.pet_factory = animal_factory

        def show_pet(self):
            """Creates and shows a pet using the abstract factory"""

            pet = self.pet_factory.get_pet()
            print("We have a lovely {}".format(pet))
            print("It says {}".format(pet.speak()))
            print("We also have {}".format(self.pet_factory.get_food()))


    # Stuff that our factory makes

    class Dog:

        def speak(self):
            return "woof"

        def __str__(self):
            return "Dog"


    class Cat:

        def speak(self):
            return "meow"

        def __str__(self):
            return "Cat"


    # Factory classes

    class DogFactory:

        def get_pet(self):
            return Dog()

        def get_food(self):
            return "dog food"


    class CatFactory:

        def get_pet(self):
            return Cat()

        def get_food(self):
            return "cat food"


    # Create the proper family
    def get_factory():
        """Let's be dynamic!"""
        return random.choice([DogFactory, CatFactory])()


    # Show pets with various factories
    if __name__ == "__main__":
        for i in range(3):
            shop = PetShop(get_factory())
            shop.show_pet()
            print("=" * 20)

    ### OUTPUT ###
    # We have a lovely Dog
    # It says woof
    # We also have dog food
    # ====================
    # We have a lovely Dog
    # It says woof
    # We also have dog food
    # ====================
    # We have a lovely Cat
    # It says meow
    # We also have cat food
    # ====================


### Builder模式

    #!/usr/bin/python
    # -*- coding : utf-8 -*-

    """
    @author: Diogenes Augusto Fernandes Herminio <diofeher@gmail.com>https://gist.github.com/420905#file_builder_python.py
    """

    # Director
    class Director(object):

        def __init__(self):
            self.builder = None

        def construct_building(self):
            self.builder.new_building()
            self.builder.build_floor()
            self.builder.build_size()

        def get_building(self):
            return self.builder.building


    # Abstract Builder
    class Builder(object):

        def __init__(self):
            self.building = None

        def new_building(self):
            self.building = Building()

        def build_floor(self):
            raise NotImplementedError

        def build_size(self):
            raise NotImplementedError

    # Concrete Builder
    class BuilderHouse(Builder):

        def build_floor(self):
            self.building.floor = 'One'

        def build_size(self):
            self.building.size = 'Big'


    class BuilderFlat(Builder):

        def build_floor(self):
            self.building.floor = 'More than One'

        def build_size(self):
            self.building.size = 'Small'


    # Product
    class Building(object):

        def __init__(self):
            self.floor = None
            self.size = None

        def __repr__(self):
            return 'Floor: {0.floor} | Size: {0.size}'.format(self)


    # Client
    if __name__ == "__main__":
        director = Director()
        director.builder = BuilderHouse()
        director.construct_building()
        building = director.get_building()
        print(building)
        director.builder = BuilderFlat()
        director.construct_building()
        building = director.get_building()
        print(building)

    ### OUTPUT ###
    # Floor: One | Size: Big
    # Floor: More than One | Size: Small

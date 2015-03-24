### 元类

我们知道`python`是支持面向对象编程的，在python中一切皆是对象，那么我们看下面一段代码。

```Bash
In [1]: class Test(object):
   ...:     pass
   ...:

In [2]: test = Test()

In [3]: print(type(test))
<class '__main__.Test'>

In [4]: print(type(Test))
<type 'type'>

In [5]: print(type(type))
<type 'type'>
```
以上，可以发现，实例的类型是类，类的类型是`type`，type的类型也是type，那么type究竟是什么呢？

[type](https://docs.python.org/2/library/functions.html#type)是python中的内建元类。元类(`metaclass`)是类(`class`)的类，也就是说，类是元类的实例，如同对象(`object`)是类的实例。

#### 1. 内建元类`type`
type的原型如下：

```python
type(name, bases, dict)
"""
name: 类名字符串
bases: 父类元组
dict: 类属性字典
"""
```

上面代码中的`Test`类，也可以通过以下方式创建：

```Bash
In [1]: Test = type('Test', (), {})

In [2]: test = Test()

In [3]: print(type(test))
<class '__main__.Test'>

In [4]: print(type(Test))
<type 'type'>
```
---

#### 2. 自定义元类


#### 3. 元类`lookup`
python类在创建的时候，会按一下步骤来查找`__metaclass__`属性：

1. 如果类的`__metaclass__`属性存在，则使用类的`__metaclass__`。
2. 如果类的`__metaclass__`属性不存在，则使用父类的`__metaclass__`。
3. 如果父类的`__metaclass__`属性不存在，则使用全局的`__metaclass_`。
4. 如果全局的`__metaclass__`属性不存在，则使用内建元类`type`。

#### 4. `__call__`方法
通过重写元类的`__call__`方法，我们可以自定义，通过元类生成的类的对象生成时的行为。如：实现单例模式，代码如下：

```python
class SingletonMeta(type):
    _instances = {}

    def __call__(cls, *args, **kwargs):
        if cls not in cls._instances:
            cls._instances[cls] = type.__call__(*args, **kwargs)
        return cls._instances[cls]

class SingletonClass(object):
    __metaclass__ = SingletonMeta

sm1 = SingletonClass()
sm2 = SingletonClass()
print sm1 is sm2
```

#### 5. `__new__`方法
通过重写`__new__`方法，我们可以自定义元类生成类时的行为。如：修改类属性，代码如下：

```python
class Meta(type):

    def __new__(meta, name, bases, attrs):
        new_attrs = dict()
        for key, value in attrs.items():
            if not key.startswith('_'):
                new_attrs['meta_' + key] = value
        return type.__new__(meta, name, bases, new_attrs)

class Test(object):

    __metaclass__ = Meta

    name = 'hello from Test'

test = Test()
print(test.meta_name)
print(test.name)
```

#### 6. `__init__`方法
通过重写`__init__`方法，我们可以自定义元类初始化类时的行为。如：添加类属性，代码如下：

```python
class Meta(type):

    def __init__(meta, name, bases, attrs):
        meta.name = 'Test from Meta'
        type.__init__(meta, name, bases, attrs)

class Test(object):

    __metaclass__ = Meta

test = Test()
print(test.name)
```

#### 7. 何时使用元类

>Metaclasses are deeper magic than 99% of users should ever worry about. If you wonder whether you need them, you don’t (the people who actually need them know with certainty that they need them, and don’t need an explanation about why).

>– Tim Peters


#### 8. 相关文档
1. [python官网](https://docs.python.org/2/reference/datamodel.html#customizing-class-creation)
2. [stackoverflow上metaclass讨论](http://stackoverflow.com/questions/100003/what-is-a-metaclass-in-python)
3. [stackoverflow上元类中__new__和__call__使用讨论](http://stackoverflow.com/questions/6966772/using-the-call-method-of-a-metaclass-instead-of-new/6966942#6966942)

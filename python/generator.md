### 生成器

python中生成器(generator)是一个能记住上一次返回状态的函数，它通过`yield`关键字来创建。

`yield`关键字会挂起当前的函数执行流程，保存所有的局部变量，当下一次`next`调用的时候，继续执行函数，直至抛出`StopIteration`异常。


#### next
`next()`函数是继续执行函数体，直到遇到`yield`关键字，然后继续挂起函数。`next()`函数返回`None`。

#### send
`send(value)`函数是重新执行生成器，并使`value`成为`yield`表达式的值。`next()`就相当于`send(None)`。

```python
# -*- coding: utf-8 -*-

def simple_generator():
    a = yield 1
    print 'a', a
    b = yield 2
    print 'b', b
    c = yield 3
    print 'c', c

our_generator = simple_generator()
print our_generator.next()
print our_generator.send(8)
print our_generator.send(None)
print our_generator.next()
```

#### 生成器表达式

生成器表达式和列表表达式非常相似，不过生成器表达式并不是真正的创建列表，而是返回一个生成器。生成器表达式使用延迟计算，使得它在内存使用上更节省。

生成器表达式语法如下：

```python
(expr for iter in iterable if cond_expr)
```

下面是用生成器表达式，找出文件中长度最长的一行。

```python
def find_longest_line(filename):
	return max(len(line.strip()) for line in open(filename))
```







---
layout: post
title: lazy-loading
date: 2017-04-23 22:45:28 +8000
categories: Python
tags: 模块导入
author: flaneur
comments: true
---

> 引言
>
> 在**Python高手之路**里看到关于PEP302导入钩子机制的说明，于是翻了翻cookbook，打算把导入模块哪一章再看一遍。

Python 加载模块有很多方式，我就不在这里一一阐述了。

*Python Cookbook* `10.4`节 *将模块分解成多个文件* 末尾提及了惰性加载这个名词

### 什么是惰性加载？

> 在`__init__.py` 文件内部可以将所在文件夹内需要使用的部分导入，可以一次性将所有需要的组件都导入进来。但是，对于**非常庞大**的模块，也许只希望在实际需要的时候才加载那些组件。对于这个问题，可以引入惰性加载：

```python
# __init__.py 

# 文件夹内有a.py，b.py
# a.py中定义了一个类A，A中定义了一个函数spam()，负责输出‘A.spam’
# b.py定义了类B

# lazy-loading
def A():
	from .a import A
    return A

def B():
	from .b import B
    return B

# non lazy-loading
from .a import A
from .b import B
```

在前者版本中，class A, class B 用函数取代，首次访问他们时才会加载所需的类。

```python
>>> import mymodule
>>> a = mymodule.A()
>>> a.spam()
A.spam() # spam函数负责print(‘A.spam’)
>>>
```

###  优缺点

惰性加载的优点，显而易见，在导入非常庞大的模块时，可以节省内存。

但是它的缺点是会破坏继承和类型检查机制。



### 在Werkzeug库中的应用

[Werkzeug](https://github.com/pallets/werkzeug/blob/master/werkzeug/__init__.py)中就使用了lazy-loading，作者将Werkzeug的各个模块及其内部的类，生成一个key为文件夹名，value为文件夹内部的对象的字典，由于太长不完全展示出来

```python
# import mapping to objects in other modules
all_by_module = {
    'werkzeug.debug': ['DebuggedApplication'],
    'werkzeug.local': ['Local', 'LocalManager', 'LocalProxy', 'LocalStack',
                       'release_local'],
    'werkzeug.serving': ['run_simple'],
    'werkzeug.test': ['Client', 'EnvironBuilder', 'create_environ',
                      'run_wsgi_app'],
# skip
    'werkzeug.security': ['generate_password_hash', 'check_password_hash'],
    'werkzeug._internal': ['_easteregg']
}
```



交换键值，得到`object_value`，以方便后面的调用

```python
object_origins = {}
for module, items in iteritems(all_by_module):
    for item in items:
        o
```
